---
title: crm管理系统
date: 2018-11-8 20:23:00
tags: [nodeJS, js,vue]
---

最近我在学习nodeJS
做了个简单的后台项目,前端用了 vue ,实际上就是把我以前的项目改一改,后端用到的技术是 node +koa+ mongodb + mongoose,这里主要给大家介绍后端的服务搭建

#### 1.后端结构目录
![logo](/images/crmNode-1.png)
#### 1.app.js 入口文件
``` javascript
const Koa = require('koa'),
bodyParser = require('koa-bodyparser'), // 解析post 请求参数
serve = require('koa-static'), // 静态资源文件
session = require('koa-session') // session 设置
// 路由
const router = require('koa-router')({
prefix: '/api/v1' // 配置路由前缀
})
// 实例化koa 对象
let app = new Koa()
// 使用中间件
app.keys = ['some secret hurr']
app.use(session(CONFIG, app))
app.use(bodyParser())
app.use(serve('.'))
// 路由懒加载
const index = require('./routes/index')
// router.use(index)
router.use(index)
// 使用路由中间件
app.use(router.routes()).use(router.allowedMethods())
app.listen(3000)
```
#### 2.全局错误处理
``` javascript
// 错误处理 - 返回前端参数
app.use(async (ctx, next) => {
  try {
    if (
      JSON.stringify(ctx.session.userInfo) ||
      ctx.URL.pathname == '/api/v1/login'
    ) {
      await next()
    } else {
      ctx.data = {
        returnCode: 401,
        returnMessage: '请先登录',
        returnUserMessage: '请先登录'
      }
    }
  } catch (err) {
    ctx.type = 'json'
    ctx.body = {
      error: {
        returnCode: 1,
        returnMessage: err,
        returnUserMessage: err
      },
      data: null
    }
  }
  if (ctx.data) {
    ctx.type = 'json'
    ctx.body = {
      error: {
        returnCode: ctx.data.returnCode || 0,
        returnMessage: ctx.data.returnMessage || '',
        returnUserMessage: ctx.data.returnUserMessage || ''
      },
      data: ctx.data
    }
  }
})
```
#### 3.路由懒加载
我们在设计模块的时，并不期望加载app.js 时就把所有的模块全部引入，比如 默认只引入 index 模块
``` javascript
const index = require('./routes/index')
router.use(index)
```
在 index 模块中 又再次区分当前请求的接口请求对应的模块
``` javascript
const customers = require('./customers')
const login = require('./login')
const router = require('koa-router')()
router.use('/customers', customers)
router.use('/login', login)
module.exports = router.routes()
```
如 收到 '/api/v1/customers' 的接口请求，那只需要使用 customers 模块
``` javascript
router.get('/', async ctx => {
  try {
    let { pageNum, pageSize } = ctx.query
    let list = await customersModel.aggregate([
      { $skip: (pageNum - 1) * pageSize },
      {
        $limit: Number(pageSize)
      }
    ])
    list.map(item => {
      if (item.userImg !== '') {
        item.userImg = `${tools.host}/${item.userImg}`
      }
    })
    let totalCount = await customersModel.countDocuments()
    ctx.data = {
      list,
      pageOpt: {
        totalPage: 0, //总页数
        currPageNo: Number(pageNum), //当前页数
        pageSize: Number(pageSize), //每页条数
        totalCount
      }
    }
  } catch (e) {
    throw '获取列表失败'
  }
})
router.post(
  '/add',
  tools.upload().fields([{ name: 'userImg' }]),
  async (ctx, next) => {
    let file = ctx.req.files['userImg']
    let userImg = file ? file[0].path : ''
    try {
      let doc = await new customersModel(
        Object.assign(ctx.req.body, { userImg })
      ).save()
      ctx.data = doc.id
    } catch (e) {
      throw '新建数据失败'
    }
  }
)
router.get('/:id', async ctx => {
  let id = ctx.params.id
  let result2
  try {
    await customersModel.find(
      {
        _id: id
      },
      (err, result) => {
        if (err) {
          tools.error('没有当前数据')
        }
        result2 = result[0]
        if (result2.userImg !== '') {
          result2.userImg = `${tools.host}/${result2.userImg}`
        }
      }
    )
  } catch (e) {
    throw '详情页面获取数据失败'
  }
  ctx.data = result2
})
```
customers 根据不同的接口响应不同的数据
---
title: vue 学习第二章
categories: 追求优雅的程序媛的日常
date: 2017-05-11 14:06:00
tags:
---

接上一篇vue学习总结


#### 17.子组件将数据发送到父组件:

``` bash
默认情况下，子组件没法访问父组件的数据，子组件要放在负组件的模板中
在父组件的模板中调用数据
     <bbb :m = "数据" :my-msg=""></bbb>
     子组件内：
     props:['m','myMsg']
     props:{
          'm':'String',
          'myMsg':Number
     }

```


#### 18.父组件获取子组件中的数据：

``` bash
        vm.emit(事件名，数据)；
        v-on:@
```

#### 19.slot(占位符)：


``` bash
      <aaa>
        <ul slot="ul-slot">  </ul>
      </aaa>
      <template>
          <slot name='ul-slot'></slot>
      </template>

```


#### 20.vue-router 单页面应用（根据不同的url,出现不同的效果）：
 单页应用：在浏览器端运行的应用，他们在使用的时候不会重复的加载页面。
``` bash
      <a v-link="{path:'/home'}"></a> 跳转
      <router-view></router-view> 展示
      js配置：
      // 准备一个根组件
      var App = Vue.extend();
      // 准备组件
      var Home = Vue.extend({
      template:'<h3>我是主页</h3>'
      })
      var News = Vue.extend({
      template:'<h3>我是新闻</h3>'
      });
      // 准备路由
      var router = new VueRouter()

      // 关联
      router.map({
      'home':{
      component:Home
      },
      'news':{
      component:News
      }
      })
      // 启动整个路由
      router.start(App,'#box');
      // 路由跳转
      router.redirect({
      '/':'home'
      })

```
#### 20.路由嵌套（多层路由）

``` bash
router.map({
   'home':{
      component:Home,
      subRoutes:{
         'login':{
            component:{
               template:'<strong>我是登录信息</strong>'
            }
         },
         'reg':{
            component:{
               template:'<strong>我是注册信息</strong>'
            }
         }
      }
   },
   'news':{
      component:News,
      subRoutes:{
         '/detail/:id':{
            component:Detail
         }
      }
   }
```

#### 21.路由传参数

``` bash
'news': {
  component: News,
  subRoutes:{
  '/detail/:id':{
  component:Detail
  }
  }
}

```

#### 22.vue-loader

 vue-loader 基于 webpack 
.vue 放置的是vue 组件代码

``` bash
<template>
html 
</template>
<script>
js
</script>
<style>
css
</style>

```

#### 23.与webpack 整合配置目录结构

``` bash
  简单的目录结构：
     index.html
     main.js
     App.vue
     package.json
     webpack.config.js  webpack 默认的配置文件
     ES6: 模块化开发
     导出模块 export default{}
     导入模块 import 模块名 from 地址

```

#### 24.webpack 准备工作

``` bash
    npm install webpack --save-dev
    npm install webpack-dev-server --save-dev
    App.vue  ->变成正常代码        vue-loader
    npm install vue-loader --save-dev
    npm install vue-html-loader --save-dev
    npm install css-loader --save-dev
    npm install vue-style-loader --save-dev
    npm install vue-hot-reload-api --save-dev
    babel 相关：
    babel 相关的插件
    babel-loader 
    babel-core
    babel-plugin-transform-runtime
    babel-preset-es2015
    babel-runtime
```
#### 25.vue+loader+vue-router

``` bash
    1.npm install vue-router
    2.引用路由 import VueRouter from 'vue-router'
    3.Vue.use(VueRouter); // 使用路由
    4.配置路由 var router = new Router();
      router.map({
            'home':{
                  components:Home
            }
        })
    5.开启路由router.start(App,#aaa);

```

#### 26.上线

``` bash
npm run build
               ->webpack -p

```
#### 27.vue 脚手架-> vue-cli

``` bash
本身集成很多项目模板：(提供项目的基本结构)
     simple   
     webpack     eslint 检查代码规范，单元测试  可以使用（比较大型的项目）
     webpack-simple      没有代码检查

```
#### 28.vue2.0 相对变化

``` bash
1）组件的定义：
     a)在每个组件中，不在支持代码片段
          组件不能用template 直接包裹，而是外层要放一个div
     b)      1Vue.extend() 这种方式在2.0 中有所改动，但是尽量少用
     2. 用 Vue.component('组件名'，{
          })
     2.0新出了组件的定义方式
2） 生命周期：
    beforeCreate  组件刚刚被创建，属性都没有挂载
    created            实例已经创建完成，属性已经绑定
    beforeMount  模板编译之前
    mounted         模板编译之后
    beforeUpdate  组件更新前
    updated            组件更新后
    beforeDestory  组件销毁前
    destory              组件销毁后
3）循环的改动：
    2.0中默认可以添加重复数据
4）过滤器：全部删除
5）组件通讯：
    vm.$emit();
    vm.$on();
    子组件拿到父组件的数据：通过 props
    之前子组件可以更改负组件的数据 但是 2.0 不被允许
  问题：父组件 把数据传给子组件，子组件内修改父组件传过来的数据，修改的值再传回子组件。
      a) 父组件 每次传一个对象给子组件，对象之间的引用
6）可以使用单一事件管理通讯 （组件与组件之间的通讯） vuex
      var Event = new Vue();
      Event.$emit("事件名称"，数据)；
      Event.$on('事件名称'，function(data){ }.bind(this))
```
#### 29.未完待续...
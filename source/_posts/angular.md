---
title: angular5 项目构建
date: 2017-07-09 16:20:11
tags:
---
做了公司的财务管理项目，主要用到angular开发，顺便把 angular 技术给大家分享一波


#### 1.安装angular/cli

``` bash
npm install -g @angular/cli
```
#### 2.创建项目
``` bash
ng new my-app
```
### 3.目录结构分析
![logo](/images/angular-1.png)
### 4.app.module.ts 
![logo](/images/angular-3.png)
### 5.自定义
``` bash
ng g component **  新建组件
ng g directive **  新建指令
ng g pipe **  新建管道
ng g service **  新建服务

```
### 6.启动项目
1.ng server
2.反向代理的方式启动 配置 proxy.conf.json
``` bash
{
    "/outApi": {
        "target": "http://100.66.172.200:8080",
        "changeOrigin": true,
        "secure": false
    }
}
```
执行 ng serve --proxy-config proxy.conf.json




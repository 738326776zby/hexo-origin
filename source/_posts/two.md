layout: '[layout]'
title: 一些前端应该知道的知识
categories: 追求优雅的程序媛的日常
date: 2017-05-26 19:50:00
tags:
---

&emsp;&emsp;最近听惠玲说起面试某某公司四年经验的FE,基础问题都打不出来。。。默默总结一波：

#### 1.什么是跨域？跨域具体有哪些解决办法？解决办法

``` bash

    浏览器对javascript的同源策略是限制的，网页不能执行其他网页的脚本。
    什么是同源？ 域名，协议，端口 都相同。
    浏览器在执行javascript 脚本时，会检查这个脚本属于哪个页面，如果不是同源页面，就不会执行
    解决办法：
    1)jsonp+jquery:只能使用get请求不能使用post 请求
    2)基于script标签实现跨域请求
    3)iframe + document.domain :实现父子间的交互
    4)后端设置脚本：header('Access-Control-Allow-Origin:*');//允许所有来源访问

```

#### 2.cookie 的属性
***
![logo](/images/YY.jpg)

``` bash
name字段为一个cookie的名称。
value字段为一个cookie的值。
domain字段为可以访问此cookie的域名
path字段为可以访问此cookie的页面路径
expires/Max-Age 字段为此cookie超时时间
Size字段 此cookie大小。
http字段  cookie的httponly属性。若此属性为true，则只有在http请求头中会带有此cookie的信息，而不能通过document.cookie来访问此cookie
secure 字段 设置是否只能通过https来传递此条cookie
```



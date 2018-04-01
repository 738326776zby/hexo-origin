---
title: bower
date: 2016-08-23 14:06:00
categories: 追求优雅的程序媛的日常
---
[这篇文章](http://www.jianshu.com/p/1f3cf01fb028)是我曾经在简书中记录，现在改版在这里:

&emsp;&emsp;[bower](https://bower.io/) 作为现在非常流行的前端开发的包管理器 很多前端的工具都打包成了bower的包发布在上面 方便到我们利用bower 的命令就可以下载到我们所需的工具和其依赖。

#### 1.安装bower:

``` bash
npm install -g bower 将bower 安装在全局
```


#### 2.查找分为两种方法：

``` bash
bower search   在命令中查看你所有寻找的包
另一方法是到其官网搜索相应的包，下载你需要的保本
```

#### 3.查看包具体版本号的信息：

``` bash
bower info
```
在这条命令下 你可以看到你查询的包的名称，报的描述，官网地址 ，还有现在目前的可以用的版本号

``` bash
bower info   包名#版本号
```
这条命令你可以查看具体版本号包的信息

#### 4.安装包（版本号没有给出会默认安装当前最高版本）：

``` bash
bower install  包名#版本号
```
这样我们就安装好了需要的工具和它的依赖 ，默认会存放在bower_components 文件夹中，然而我们真正需要的文件一般是包种dist（distribution）意为分布的文件夹中。

#### 5.初始化一个bower 项目

``` bash
bower init
或者手工创建
```
要依次填写 bower 项目的名字（默认为当前文件夹的名字），描述
，主要的文件，关键字，版本，主页等信息，还要设置当前的依赖记录在dependences中，一路回车我们能生成一下：

``` bash
 {
  "name": "test",
  "authors": [
  "zhangboyakuaile <738326776@qq.com>"
   ],
   "description": "",
   "main": "",
   "license": "MIT",
   "homepage": "",
   "ignore": [
   "**/.*",
   "node_modules",
   "bower_components",
   "test",
   "tests"
   ]
 }
```
这就是我们项目的描述。注意的是 bower.json 的作用不但是描述包的信息，而且还会在再次安装时留住版本的信息。

#### 6.升级安装的版本

``` bash
bower list   查看当前项目下包的版本和包的最新版
vim bower.json 命令修改其 的版本号
bower update  命令更新项目下的包
```
再次执行bower list 发现包已经变成你修改的版本

#### 7.将bower 安装的包添加到依赖的列表中

``` bash
bower install  包名 --save|-S
bower install  包名 -- save|-D(添加到项目运行时的依赖)
```

#### 8.卸载包

``` bash
bower uninstall 包名 --save
```

#### 9.卸载项目没有依赖的库

``` bash
bower prune  根君bower.json 中的dependencies 删除多余的包
```
#### 10.缓存

``` bash
bower cache list 查看本地的缓存
bower install 包名 --offline --save 根据本地的缓存进行安装。
bower cache clean 清除缓存
```

#### 11.修改版本号

``` bash
bower version major|minor|patch
```
#### 12.配置bower 的默认行为

``` bash
设置.bowerrc文件
配置其中的默认信息
    {
      "directory":"app/download"
     }
```
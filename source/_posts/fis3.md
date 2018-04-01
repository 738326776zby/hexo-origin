---
title: fis3
categories: 追求优雅的程序媛的日常
---
&emsp;&emsp;虽然gulp和grunt的竞争如火如荼，但是很显然gulp仍让在傲视群雄吧，在github 上最新的 star 数量目前直接逼近3万了，可见前端的架构越来越重要了。
&emsp;&emsp;今天我要介绍的是fis3,最近资管项目用了fis3 做了项目的前端架构，给我的感觉很轻量，通过简单方便的配置就能把项目加架起来。

下面是我对项目的分析：

### 官网学习总结：

#### 1.构建:

&emsp;&emsp;资源定位： fis3 在将 资源发布后 会将相对路径换成绝对路径。 将开发路径和部署路径分离，开发时只需要使用相对路径定位自己开发的资源。 FIS3 的构建是不会对源码做修改的，而是构建产出到了另外一个目录，并且构建的结果才是用来上线使用的

&emsp;&emsp;配置文件：默认的配置文件 为fis3-conf.js .js  fis3 编译整个过程是通过配置来控制的，固话了构建流程，让工程构建更加简单.

``` bash
fis.match(selector,props)
selector :   FIS3 把匹配文件路径的路径作为selector
props:  编译规则属性，包括文件属性和插件属性
```
&emsp;&emsp;规则覆盖：如果不同规则命中同一个文件，后面的属性规则会覆盖前一个。

``` bash
fis.media() : 提供多种状态功能（有一些配置是需要在开发中使用的，有些是仅在生产环境中使用的）
```
&emsp;&emsp;文件指纹：唯一标识的一个文件。构建出的文件携带了md5 戳。

&emsp;&emsp;压缩资源：为了减少网络传输的大小， 通过压缩器对js,css,图片进行压缩，有些是已经内置好了的，有些需要自己下载安装

#### 2.调试：

``` bash
目录：fis3 server open 打开fis3 输出的目录（web Server）
发布：fis3 release  -w 监听  -l 自动刷新
启动： fis3 server start 本来启动本地的web Server  默认为8080端口 ctrl +c 终止程序
```

#### 3.内置语法：

&emsp;&emsp;html 中嵌入其他文件内容活base64 编码，在资源定位的基础上，给资源加上__inline参数来标记资源嵌入需求。
&emsp;&emsp;资源定位：通过配置文件定义文件的发布路径。

### 项目分析总结：

#### 1.项目目录如下:

![logo](../../../../images/5.png)


components 文件夹 主要是一些页面公用的方法，比如bootstrap,jquery,jquery-validate,moment,underscore,均被模块化，遵循commentJS规范
common 文件夹 主要存放 全局共用的 css,图片资源文件,js文件,以及 laydate,layer,laypage 插件，widget文件夹下的 页面片段
installPlugin.sh文件(执行此文件的目的是下载fis3相关的插件):

``` bash
npm install -g fis-optimizer-html-minifier
npm install -g fis-optimizer-shutup
npm install -g fis-packager-depscombine-pattern
npm install -g fis-packager-stable-map
npm install -g fis-parser-es6-babel
npm install -g fis-parser-less-2.x
npm install -g fis-postpackager-autoload
npm install -g fis-postprocessor-cssprefixer
npm install -g fis-postprocessor-jswrapper
npm install -g fis-preprocessor-cssgrace
npm install -g fis-preprocessor-cssprefixer
npm install -g fis3-deploy-replace
npm install -g fis3-hook-commonjs
npm install -g fis3-hook-module
npm install -g fis3-hook-relative
npm install -g fis3-packager-deps-pack
npm install -g fis3-parser-dustc
npm install -g fis3-parser-underscore
npm install -g fis3-postpackager-loader
```
fis-conf.js

``` bash
fis.set('project.fileType.text', 'html');

fis.set('project.ignore', ['.svn', '.git',  '**/fis-conf.js', '/*.sh', '/output/**', 'component.json', '**/*.md', '.log', 'BCLOUD']);

 //fis.match('*.html', {
 //    //invoke fis-optimizer-html-minifier
 //    optimizer: fis.plugin('html-minifier')
 //});

 /*fis.hook('module', {
     mode: 'commonJs'     // 模块化支持 commonJs 规范，适应 mod.js
 });*/
fis.hook('commonjs');
// 设置组件库里面的 js 都是模块化 js.
fis.match('components/**.js', {
    isMod: true
});
fis.match('*.js', {
     isMod: true,
     useMap: true,
     useHash: true,
    // wrap: 'amd' //,
    // optimizer: fis.plugin('uglify-js')
 });
fis.match('common/js/**.js', {
    isMod: false
});
fis.match('common/lib/**.js', {
    isMod: true
});
fis.match('**/{mod.js}', {
     isMod: false,
     wrap: false
 });

fis.match('::package', {
     packager: fis.plugin('map'),
     // npm install [-g] fis3-postpackager-loader
     // 分析 __RESOURCE_MAP__ 结构，来解决资源加载问题
     postpackager: fis.plugin('loader', {
         resourceType: 'commonJs',
         useInlineMap: true // 资源映射表内嵌
     })
 });
// 开启同名依赖
fis.match('/module/**', {
    useSameNameRequire: true
});

 // 让所有文件，都使用相对路径。
 fis.hook('relative');

 // 让所有文件，都使用相对路径。
 fis.match('*', {
     useHash: true,
     release: '/$0',
     relative: true
 });

 fis.match('*.html', {
     extras: {
         isPage: true
     }
 });

 fis.match('*.{html,json,conf}', {
     useHash: false
 });

 // 模板预编译
 fis.match('**/tpl/*.tpl', {
     isMod: true,
     rExt: '.js',
     parser: fis.plugin('underscore')
 });

 fis.match('*.less', {
     useHash: true,
     rExt: '.css',
     optimizer: fis.plugin('clean-css'),
     parser: fis.plugin('less-2.x'),
     release: '/$0'
 });

 fis.match('components/bootstrap/**/*.less', {
         parser: null,
         preprocessor: null,
         optimizer: null
     });

// server.conf处理
var allServerConf = '';
fis.match(/^\/[^\/]+\/server\.conf$/, {
    parser: function(content) {
        allServerConf += content + '\n';
        return allServerConf;
    },
    release: '/config/server.conf'
});

 // 线上环境配置
 fis.media('prod')
     .match('::packager', {
         packager: fis.plugin('map'),
         // 分析 __RESOURCE_MAP__ 结构，来解决资源加载问题
         postpackager: fis.plugin('loader', {
             resourceType: 'commonJs',
             allInOne: true, // 零散资源包合并
             useInlineMap: true // 资源映射表内嵌
         })
     })
     .match('*.less', {
         optimizer: fis.plugin('clean-css')
     })
     .match('*.js', {
         optimizer: fis.plugin('uglify-js')
     });
```

用 fis3 搭建起来项目还需要各位对官网的研究和探索。

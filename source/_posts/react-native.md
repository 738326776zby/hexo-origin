---
title: react-native 初探
date: 2016-08-26 20:27:00
categories: 追求优雅的程序媛的日常
---
[这篇文章](http://www.jianshu.com/p/60443df9c160)是我曾经在简书中记录，现在改版在这里:

&emsp;&emsp;[react-native](http://reactnative.cn/) 今天我试着尝试了react-native,我用的是win系统，当然踩了很多坑，不过好在把react-native的环境和配置安装好了，分享如下：
### 一.环境配置
#### 1.下载chocolatey 命令如下:

``` bash
@powershell -NoProfile -ExecutionPolicy Bypass -Command "iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))" && SET PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin
```


#### 2.使用Chocolatey来安装Python 2.：

``` bash
choco install python2
```

#### 3.node环境也必不可少

``` bash
choco install npm
```

#### 4.全局安装 react-native-cli

``` bash
npm install -g react-native-cli
```
#### 5.下载 Android Studio ,并配置环境变量（环境变量配置可自行百度），这个过程会很慢，所以我用了Lantern 可以翻墙但是还是很慢。


#### 6.下载了 [Genymotion](https://www.genymotion.com/)


#### 7.下载了Oracle VM VirtualBox 虚拟机

准备工作完成


### 二.项目的启动

#### 1.首相新建一个react native 的项目（这个过程很长我用了大概8分钟）

``` bash
react-native init AwesomeProject
```
下载好后项目的目录结构

![logo](/images/1.png)

#### 2.进入项目当前目录的文件夹下命令行中手动运行 Packager

``` bash
cd AwesomeProject
react-native start
```

![logo](/images/2.png)

#### 3.打开虚拟机 和 Genymotion ，在 Genymotion 要添加设备，并启动设备

#### 4.项目启动好了以后 在android Studio中导入 项目目录中android文件夹

![logo](/images/3.png)
#### 5.android studio 中启动项目， Genymotion 的虚拟机 上的react-native 的项目启动好了

![logo](/images/4.png)
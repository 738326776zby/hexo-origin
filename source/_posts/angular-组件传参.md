---
title: angular-组件传参
date: 2017-07-10 19:20:10
tags:
---
angular 组件 传参的方式和 vue 类似，但是确实比vue 麻烦很多，要引一系列辅助的模块，没有像 vuex 这样的状态存储，兄弟组件间的数据共享着实让人抓狂。。。

### 一.父组件传数据给子组件
#### 1）父组件调用子组件时直接传递数据
``` bash
<app-header [msg]="msg"></app-header>
```
#### 2）子组件引入 Input 模块
``` bash
import { Component, OnInit ,Input } from '@angular/core';
```
#### 3）子组件中利用 @Input 接收父组件传过来的数据
``` bash
@Input() msg:string
```
#### 4）子组件中使用父组 件的数据
``` bash
{{msg}}
```
### 二.子组件执行父组件中的方法
#### 1）父组件定义方法
``` bash
    run(){
        alert('这是父组件的 run 方法');
    }
```
#### 2）调用子组件 传入当前方法
``` bash
    <app-header [msg]="msg" [run]="run"></app-header>
```
#### 3）子组件通过 @Input 接收父组件 传过来的方法
``` bash
    import { Component, OnInit ,Input } from '@angular/core';
    @Input() run:any;
```
#### 4）子组件使用父组件 的方法。
``` bash
    this.run(); /*子组件调用父组件的 run 方法*/
```
### 三.子组件通过 @Output 执行父组件的方法
#### 1）子组件引入 Output 和 EventEmitter
``` bash
    import { Component, OnInit ,Input,Output,EventEmitter} from '@angular/core';
```
#### 2）子组件中实例化 EventEmitter
``` bash
    @Output() private outer=new EventEmitter<string>();
```
#### 3）子组件通过 EventEmitter 对象 outer 实例广播数据
``` bash
    this.outer.emit('msg from child')
```
#### 4）父组件调用子组件的时候，定义接收事件 , outer 就是子组件的 EventEmitter 对象 outer
``` bash
    <app-header (outer)="runParent($event)"></app-header>
``` 
#### 5）父组件接收到数据会调用自己的 runParent 方法，这个时候就能拿到子组件的数据
``` bash
    runParent(msg:string){
        alert(msg); 
    }
``` 
### 四.父组件获取子组件的数据和方法
#### 1）父组件调用子组件 的时候给子组件起个名字
``` bash
    <app-footer #footer></app-footer>
``` 
#### 2）直接获取执行子组 件的方法
``` bash
    <button (click)='footer.footerRun()'>获取子组件的数据</button>
``` 
### 五.父组件通过 @ViewChild 获取子组件的数据和方法
#### 1）调用子 组件给子组件定义一个名称
``` bash
    <app-footer #footer></app-footer>
``` 
#### 2）引入 ViewChild
``` bash
    import { Component, OnInit ,ViewChild} from '@angular/core';
``` 
#### 3）ViewChild 和刚才的子组件关联起来
``` bash
    @ViewChild('footerChild') footer;
``` 
#### 4）调用子组件
``` bash
    this.footer.footerRun();
``` 
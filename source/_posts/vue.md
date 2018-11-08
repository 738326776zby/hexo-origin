---
title: vue 学习
date: 2017-05-02 20:05:43
categories: 追求优雅的程序媛的日常
---
下面是这段时间vue的总结与梳理：
#### 1.vue 是什么？
``` bash
是一个mvvm 框架，和angular 类似 比较小巧
  mvvm 框架：
view ：html
viewMoudule: 事件方法
moudule:与数据源进行对接
```
![logo](/images/Image.png)
#### 2.指令
``` bash
 v-model: 用于一般表单的双向数据绑定
     v-for:循环
          track-by = '索引' 提高循环性能
          v-for ='name in arr'      {{$index}}
          v-for ='name in json'      {{$index}}{{$key}}
          v-for ='(k,v) in json'
      v-on:事件
          v-on:click/mouseout/mouseover/dblclick/mousedown ....  === @click
                   事件对象
          @click =  "func($event)"
 阻止冒泡： @click.stop
 阻止默认行为：@contentmenu.prevent
                  @keyup.13      @keyup.enter
      v-show: 显示与隐藏
      v-if:显示隐藏
      v-bind:绑定属性  简写为：src
```
#### 3.只绑定一次
``` bash
{{*msg}}
```
#### 4.过滤器
``` bash
{{msg | filterA}}
{{msg | filterA | filterB}}
uppercase  lowercase  capitalize currency
// 与数组配合使用的过滤器
limitBy(取几个，从哪开始)
filterBy 过滤数据 过滤 ‘谁’
orderBy 排序
1：正序
2：倒序
```
#### 5.交互
``` bash
    a) this.$http.get()/post()/jsonp()
    b) this.$http({
          url:'地址' ，
          data:'给后台传输的数据'，
           method:'get/post/jsonp',
           jsonp:'cb'
    })

```
#### 6.生命钩子函数
``` bash
     created : 实例已经创建
     beforeCompile:编译之前
     compiled: 编译之后
     beforeDestory: 销毁之前
     destoryed:销毁之后
```
#### 7.防止闪烁
``` bash
     v-cloak v-text v-html
```
#### 8.computed(放置一些业务逻辑) 计算属性的使用
``` bash
     computed:{
          b:function(){
              return 值；
          }
     }
    computed:{
         b:{
             get:
             set:
         }
    }

```
#### 9.vue实例简单方法
``` bash
    vm.$el :实例的元素
    vm.$data 实例的data
    vm.$mount （）   手动挂载vue 程序
    vm.$options     获取自定义指令
    vm.$destory     销毁vue 实例
    vm.$log（）     查看数据状态
```
#### 10.自定义过滤器
``` bash
    a)  简单过滤器 Vue.filter(name,function(input){})
    b)  双向过滤器
         Vue.filter('filterHtml',{
              read:function(input){     //mode->view
                  return input.replace(/<[^<+]>/g,'')
              },
              write:function(val){     //view -<model
                   return val
              }
         })
```
#### 11.自定义指令
``` bash
     Vue.directive('指令名称'，function(参数){
          this.el ->原生的dom 元素
     })
     <div v-red="参数"></div>
     指令名称 v-red -> red
     注意：必须以v- 开头
```
#### 12.数据监听
``` bash
    vm.$watch(name,fn)
```
#### 13.vue 组件
``` bash
    组件中放数据必须以函数的形式,data 必须是函数的形式，函数必须返回一个对象
（1）定义一个全局组件
        var Aaa = Vue.extend({
              template:'<h3>我是标题3</h3>'
        })；
        Vue.components('aaa',Aaa);
        或：
        Vue.component('my-aaa',{
             template:'<strong>好</strong>'
        });
（2） 定义一个局部组件
      var vm = new Vue({
          el:'#box',
          data:{
               bSign:true
          },
          components:{ // 此处为局部组件
          }
     })
     或：
     var vm = neww Vue({
          el:'#box',
          components:{
               template:'<h2>标题2</h2>'
          }
     })
```
#### 14. 组件配合模板
``` bash
   1.写在 components 中
          components:{
               template:'<h2 @click="change">标题2</h2>'
          }
     2.单独的模板文件
          a) <script type="x-template" id="#aaa">
                   <h2 @click="change">{{msg}}</h2>
               </script>
          b)<template id="aaa">
                <h1>标题</h1>
                <ul>
                         <li v-for="val in arr"> </li>
                </ul>
         </template>
```
#### 15.动态组件
``` bash
    <component :is="组件名称"></component>
```
#### 16.默认情况下，子组件没法访问父组件的数据，子组件要放在负组件的模板中
``` bash
      在父组件的模板中调用数据
          <bbb :m = "数据" :my-msg=""></bbb>
      子组件内：
      props:['m','myMsg']
      props:{
          'm':'String',
          'myMsg':Number
      }
```
#### 17.子组件将数据发送到父组件

``` bash
未完待续...(回家看择天记了！)
```










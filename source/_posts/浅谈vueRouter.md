---
title: 浅谈vueRouter
date: 2018-11-12 11:27:56
tags: vue
---
上次去今日头条面试时，被问及 vue-router 的诸多问题，比如 vue-router 的两种模式的区别，是如何实现的？ 如何给 vue-router 扩展一个其他的方法？ vue 跳转路由的方式有哪些，这些的区别是什么？...


#### 1.vue-router 的两种模式。
* hash 模式是 vue-router 的默认的模式，路由的表现形式为穿插 # 符号，比如 http://localhost:8080/#/, 虽然 # 在url上略显奇怪，但是他优有点就是在发送请求时，hash 值不会作为 URL 发送给后台
* history 模式利用了 history.pushState API 来完成 URL 跳转而无须重新加载页面。 比如 http://localhost:8080/hello 但是这使得前端的URL 和后端的 api 紧密关联起来 ，后端需要提供带有 /hello/*** 的接口了
``` javascript
const router = new VueRouter({
    mode: 'history',
    routes: [...]
})
```
#### 2.vue-router 的跳转方式
``` javascript
 this.$router.push({ path: '/two' })
 this.$router.replace({path：'/two' })
 <router-link to="two">跳转 </router-link> 
```
那么第一种 push 的方式和第二种 replace 的方式，有什么区别呢？我们在源码中看一下区别，两种方式都会触发 pushState 方法，不同的是 push 方式执行 history.pushState({ key: _key }, '', url);而 replace 方式，执行 history.replaceState({ key: _key }, '', url);他们有什么区别呢？
pushState 与 replaceStates 都是html 在 history 对象上增加的 api ,都能起到修改路由地址，但是不刷新页面的作用, pushState 会在当前history 对象的中新增一个历史记录,而 replaceState 会直接新生成一个histroy 对象替换当前的。简而言之，两种不同的方法进行路由跳转，在执行history.go(-1) 时，第二种模式是失效的，因为他是一个全新的 history
``` javascript
function pushState (url, replace) {
    saveScrollPosition();
    // try...catch the pushState call to get around Safari
    // DOM Exception 18 where it limits to 100 pushState calls
    var history = window.history;
    try {
      if (replace) {
        history.replaceState({ key: _key }, '', url);
      } else {
        _key = genKey();
        history.pushState({ key: _key }, '', url);
      }
    } catch (e) {
      window.location[replace ? 'replace' : 'assign'](url);
    }
}
```

#### 3.现在我们已经修改了路由的值，那么页面是如何根据不同的路由进行更新的呢？
``` javascript
var supportsPushState = inBrowser && (function () {
    var ua = window.navigator.userAgent;
    if (
      (ua.indexOf('Android 2.') !== -1 || ua.indexOf('Android 4.0') !== -1) &&
      ua.indexOf('Mobile Safari') !== -1 &&
      ua.indexOf('Chrome') === -1 &&
      ua.indexOf('Windows Phone') === -1
    ) {
      return false
    }
  
    return window.history && 'pushState' in window.history
  })();
```
 * 手动修改 url 地址 
    这段代码是检测当前的浏览器版本是否支持 history 对象 ，history 对象上是否有 pushState 方法，如果不支持路由改变时将触发 onhashChange 事件，如果支持，触发的 则是 popState 事件。这里 有些文章说 hash 模式 触发的是 onhashChange 是不绝对的 ，经过我的 debugger , 及使是 hash 模式，如果浏览器支持 history ,触发的也是 popState 事件！相比写那些博客的人都是老版本的浏览器吧。
    ``` javascript
    window.addEventListener(supportsPushState ? 'popstate' : 'hashchange', function () {
        var current = this$1.current;
        if (!ensureSlash()) {
          return
        }
        this$1.transitionTo(getHash(), function (route) {
          if (supportsScroll) {
            handleScroll(this$1.router, route, current, true);
          }
          if (!supportsPushState) {
            replaceHash(route.fullPath);
          }
        });
      });
      ```
* 利用 push 和 replace 方法触发的路由跳转 $router.push() --> HashHistory.push() --> History.transitionTo() --> History.updateRoute() ，最终路由改变时 ，触发了 history.listen 方法；
``` javascript
push (location: RawLocation, onComplete?: Function, onAbort?: Function) {
    this.transitionTo(location, route => {
      pushHash(route.fullPath)
      onComplete && onComplete(route)
    }, onAbort)
  }
  transitionTo (location: RawLocation, onComplete?: Function, onAbort?: Function) {
    const route = this.router.match(location, this.current)
    this.confirmTransition(route, () => {
      this.updateRoute(route)
      ...
    })
  }
  
  updateRoute (route: Route) {
  
    this.cb && this.cb(route)
  
  }
  
  listen (cb: Function) {
    this.cb = cb
  }
  history.listen(route => {
    this.apps.forEach((app) => {
      app._route = route
    })
  })
```
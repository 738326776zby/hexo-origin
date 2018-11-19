---
title: vue源码分析一
date: 2018-8-09 19:40:32
tags:
---
#### 1.整体实现逻辑
```bash
;(function(global, factory) {
    typeof exports === 'object' && typeof module !== 'undefined'
      ? (module.exports = factory())
      : typeof define === 'function' && define.amd
      ? define(factory)
      : (global.Vue = factory())
  })(this, function() {
    return Vue
  }）
```
首先是一个自执行的匿名函数两个形参 global,factory 与两个实参 this,fn 。在实参的fn 中返回了 Vue 对象, 并在方法执行时将 factory() 返回的 Vue 挂在到 global 上, 于是全局就可以使用Vue 对象了。
#### 2. new Vue() 时经历了什么？
```bash
function Vue(options) {
  if ('development' !== 'production' && !(this instanceof Vue)) {
    warn('Vue is a constructor and should be called with the `new` keyword')
  }
  this._init(options)
}
```
这段代码片段是 new Vue() 时执行的入口方法，虽然他很简短但是我紧接着向下看 this._init(options)。
PS有一个问题 'development' !== 'production' 字符串一定是 true, 为什么要这样判断呢？
原来 webpack 打包判断执行环境是不是生产环境，这段代码是编译过得，源代码为  process.env.NODE_ENV !== 'production'
```bash
Vue.prototype._init = function (options) {
  var vm = this;
  vm._isVue = true;
  // merge options
  if (options && options._isComponent) {
    // optimize internal component instantiation
    // since dynamic options merging is pretty slow, and none of the
    // internal component options needs special treatment.
    initInternalComponent(vm, options);
  } else {
    vm.$options = mergeOptions(
      resolveConstructorOptions(vm.constructor),
      options || {},
      vm
    );
  }
  ...
};
```
_init 方法主要是对 Vue 的实例 vm 挂在必要的属性, 我们拿一个方法去继续探索 mergeOptions() 方法要传递三个参数,分别是实例的 constructor 函数构造体，包含 已经实例化好的 部分属性，这里下文会提到，用户传递的 options 参数，以及当前 实例化的 vm 对象，把最终的 options 挂在到 vm 的 $options 上,挂在后的vm 实例件下图
![logo](/images/vue源码-1.png)

_init 方法执行后 vm 实例上已经挂好了部分属性，虽然具体属性的功能还没有完善
![logo](/images/vue源码-2.png)
#### 3.initGlobalAPI() 在 Vue 的方法上设置静态属性和方法。
和_init 的方法区别是前者设在Vue 构造方法上设置静态方法，后者是在 Vue 的实例上挂在属性和方法
```bash
var configDef = {}
configDef.get = function() {
  return config
}
{
  configDef.set = function() {
    warn(
      'Do not replace the Vue.config object, set individual fields instead.'
    )
  }
}
Object.defineProperty(Vue, 'config', configDef)
Vue.util = {
  warn: warn,
  extend: extend,
  mergeOptions: mergeOptions,
  defineReactive: defineReactive
}
Vue.set = set
Vue.delete = del
Vue.nextTick = nextTick
Vue.options = Object.create(null)

ASSET_TYPES.forEach(function(type) {
  Vue.options[type + 's'] = Object.create(null)
})
Vue.options._base = Vue

extend(Vue.options.components, builtInComponents)

initUse(Vue)
initMixin$1(Vue)
initExtend(Vue)
initAssetRegisters(Vue)
```
initGlobalAPI 方法是在 new Vue 之前执行的，传入的参数是 Vue() 方法，紧接着在 Vue 方法上添加了 set,delete,options... 等静态属性和方法 
![logo](/images/vue源码-3.png)
#### 4.initUse()
这里我们向下专研 initUse 方法
```bash
Vue.use = function(plugin) {
  var installedPlugins =
    this._installedPlugins || (this._installedPlugins = [])
  if (installedPlugins.indexOf(plugin) > -1) {
    return this
  }

  // additional parameters
  var args = toArray(arguments, 1)
  args.unshift(this)
  if (typeof plugin.install === 'function') {
    plugin.install.apply(plugin, args)
  } else if (typeof plugin === 'function') {
    plugin.apply(null, args)
  }
  installedPlugins.push(plugin)
  return this
}
```
比如我们在使用其他人的组件时 Vue.use(VueRouter) 这样引用,下面我们针对这个方法进行分析 this._installedPlugins 表示当前使用的组件是否被注册过，如果注册过返回 Vue,如果没有注册过
首先通过 toArray() 方法将类数组转换为数组，并且只有传入的第一个参数为有效的 toArray(arguments, 1) ，args.unshift(this) 将 Vue 插入到数组头部，如果我们传入的 plugin 是对象，并且存在 install 方法，那么 plugin.install() 时传入 args 作为参数 ，否则走else 并将 plugin 中的this 指向 window。 我们来以 vue-router 为例来分析
![logo](/images/vue源码-4.png)
我们看一下 Vue.use(vueRouter) 执行时，数据的变化

![logo](/images/vue源码-6.png)

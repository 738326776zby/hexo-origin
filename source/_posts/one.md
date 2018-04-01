layout: '[layout]'
title: 手机端字体的适配问题
categories: 追求优雅的程序媛的日常
date: 2017-05-24 20:27:00
tags:
---


手机端的字体是如何设置的，字体如何适配不同的手机屏幕？是我一直疑惑的问题，今天终于明白了，并做了个小demo 便于大家理解：


``` bash
rem 这个单位还是很有意思的,首先收一下 px,em和 rem 的区别

px :相对长度单位，是物理设备上显示出最小的一个点。

em:是相对长度单位。相对于当前对象内文本的字体尺寸。好比说父级的font-size:12px,子元素设置font-size:1em;那么子元素字体的大小为12px;也就是说 em会继承父级元素的字体大小。

rem:仍然为相对大小，但是与em 不同的是它相对于html 的根元素
```


任意浏览器的默认字体高都是16px。所有未经调整的浏览器都符合: 1em=16px。那么12px=0.75em,10px=0.625em。为了简化font-size的换算，需要在css中的body选择器中声明Font-size=62.5%，这就使em值变为 16px*62.5%=10px, 这样12px=1.2em, 10px=1em, 也就是说只需要将你的原来的px数值除以10，然后换上em作为单位就行了。
``` bash
<style>
    html{
        font-size:62.5%;
    }   
</style>
```
引入js:
``` bash
(function(doc, win) {
    var docEl = doc.documentElement, resizeEvt = 'orientationchange' in window ? 'orientationchange'
            : 'resize', recalc = function() {
        var clientWidth = docEl.clientWidth;
        if (clientWidth >= 640) {
            clientWidth = 640;
        };
        if (!clientWidth)
            return;
        docEl.style.fontSize = 100 * (clientWidth / 640) + 'px';
    };
    if (!doc.addEventListener)
        return;
    win.addEventListener(resizeEvt, recalc, false);
    doc.addEventListener('DOMContentLoaded', recalc, false);
})(document, window);
```
如果页面的宽度小于了了640px，那么页面中html的font-size的大小为： 100 * (当前页面宽度 / 640) 

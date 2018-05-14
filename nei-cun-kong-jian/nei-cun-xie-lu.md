# 内存泄露

前边其实已经说明了什么是内存泄漏，很多前端开发者并不会去注意这个问题，但是在大型复杂项目场景下，内存泄漏是不得不重视的一个性能优化点。

首先你得学会怎么用工具去监测是否内存泄漏，[请猛戳这里](https://developers.google.com/web/tools/chrome-devtools/memory-problems/?hl=zh-cn)。

内存泄漏的可能形式多种多样，核心都在于让 GC 认为它还在使用，而实际你没用了。

内存泄漏都是堆区，栈区不会发生内存泄漏的。

这里我列举一些网上说的各种形式和解决方式，更多的案例请联系我，我好做补充。

_滥用全局变量_

```javascript
function Foo(){
    bar = 'hello world!'
}
```

上面会意外的在全局绑定一个 bar 属性，是不会被 GC 释放的，解决办法是 `'use strict'`

 _console_

在代码中使用 `console.log`、`console.dir` 等进行调试，记录错误的对象可以将大量数据保留在内存中。

_DOM（ IE ）_

IE 对 DOM 元素使用的是引用计数的清除算法，此类场景下泄露的情况很多。

```javascript
function Foo(){
    let a = document.createElement('div');
    let b = document.createElement('div');
    a.onclick = function(){
        alert( b.outerHTML );
    }
    return a;
}
```

DOM 元素 a 中引用了一个匿名函数，该函数引用了 Foo 的作用域，持有对 a，b 的访问权限，这就是循环引用，造成内存泄漏。

解决办法：

```javascript
function Foo(){
    let a = document.createElement('div');
    let b = document.createElement('div');
    a.onclick = Bar(b);
    return a;
}
function Bar(b){
    alert(b.outerHTML);
}
```

_闭包_

```javascript
var theThing = null;var replaceThing = function () {  var originalThing = theThing;  // 这是一个闭包，里面引用了 originalThing  // originalThing 会被挂载到当前作用域下所有闭包的作用域中  // 这里去除对 originalThing 的引用也可以避免内存泄漏  var unused = function () {    if(originalThing) {}  };  theThing = {    longStr: Date.now() +  Array(1000000).join('*'),    // 这个闭包中也挂载了 originalThing    // 所以造成了循环的引用    someMethod: function () {}  };  // originalThing = null 加这一句就不会泄露了};setInterval(replaceThing, 100);
```

除了以上的场景还有很多，主需要抓住内存泄露的本质，具体问题具体分析，具体解决。当然，IE 是大坑。

另外，ES6 中的 `WeakSet、WeakMap` 也可以[避免内存泄露](http://es6.ruanyifeng.com/#docs/set-map)，你造吗?


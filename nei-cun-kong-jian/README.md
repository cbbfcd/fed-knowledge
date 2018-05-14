---
description: 详解内存空间的知识点
---

# 内存空间

内存空间一个常常被前端工程师忽视的知识点。

如果想要成为一个优秀的前端儿，必须对内存空间有了解。

这个章节将会整理内存空间的部分知识点，抛砖引玉，更多的内容需要你去了解底层的知识。

### 内存泄漏

前边其实已经说明了什么是内存泄漏，很多前端开发者并不会去注意这个问题，但是在大型复杂项目场景下，内存泄漏是不得不重视的一个性能优化点。

首先你得学会怎么用工具去监测是否内存泄漏，[请猛戳这里](https://developers.google.com/web/tools/chrome-devtools/memory-problems/?hl=zh-cn)。

内存泄漏的可能形式多种多样，核心都在于让 GC 认为它还在使用，而实际你没用了。

内存泄漏都是堆区，栈区不会发生内存泄漏的。

这里我列举一些网上说的各种形式和解决方式，更多的案例请联系我，我好做补充。

_滥用全局变量_

```text
function Foo(){
    bar = 'hello world!'
}
```

上面会意外的在全局绑定一个 bar 属性，是不会被 GC 释放的，解决办法是 `'use strict'`

 _console_

在代码中使用 `console.log`、`console.dir` 等进行调试，记录错误的对象可以将大量数据保留在内存中。

_DOM（ IE ）_

IE 对 DOM 元素使用的是引用计数的清除算法，此类场景下泄露的情况很多。

```text
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

```text
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

```text
// 经典代码
var theThing = null;var replaceThing = function () {    var originalThing = theThing;    // 这是一个闭包，里面引用了 originalThing    // originalThing 会被挂载到当前作用域下所有闭包的作用域中    // 这里去除对 originalThing 的引用也可以避免内存泄漏    var unused = function () {        if(originalThing) {}    };    theThing = {        longStr: Date.now() +  Array(1000000).join('*'),        // 这个闭包中也挂载了 originalThing        // 所以造成了循环的引用        someMethod: function () {}    };    // originalThing = null 加这一句就不会泄露了};setInterval(replaceThing, 100);
```

除了以上的场景还有很多，主需要抓住内存泄露的本质，具体问题具体分析，具体解决。当然，IE 是大坑。

另外，ES6 中的 `WeakSet、WeakMap` 也可以[避免内存泄露](http://es6.ruanyifeng.com/#docs/set-map)，你造吗?

### 异常处理与堆栈追踪

首先，函数调用栈是 LIFO 的，所以我们可以判断出下面代码的调用栈：

```text
function a(){
    console.log('a');
}
function b(){
    console.log('b');
    a();
}
function c(){
    console.log('c');
    b();
}
c(); // 调用栈（从栈顶开始依次是） a -> b -> c
```

a 函数执行完成之后从堆栈弹出，控制流交给 b，依此类推。

我们可以使用 `console.trace()` 打印出堆栈记录。

```text
function a(){
    console.log('a');
    console.trace();
}
function b(){
    console.log('b');
    a();
    // console.trace(); 可以再放这里试试
}
function c(){
    console.log('c');
    b();
}
c();
// 打印出
console.trace
a @ VM2979:3
b @ VM2979:7
c @ VM2979:11
(anonymous) @ VM2979:13

```

了解了函数调用栈以及堆栈追踪，我们再看看[异常处理](https://www.jianshu.com/p/945502a43eaa)。

首先我们得熟悉自 ES3 时代就有的 `try/catch/finally`，用法和 Java 中的如出一辙，就不再赘述。 

它可以嵌套，嵌套的子 try/catch 会自己捕获内部的异常，不会给外层捕获的机会，如果包裹的是一个异步的代码，熟悉事件循环机制的应该知道，当前执行栈不能捕获到`marcotasks`队列中的抛出异常。

```text
// 嵌套
try{
    try{
        throw new Error('some error');
    }catch(e){
        console.log('will print!');
    }
}catch(e){
    console.log('never print!')
}

// 异步
try{
    setTimeout(()=>{
         throw new Error('some error');
    }, 0)
}catch(e){
    console.log('never print!');
}
```

解决异步代码中异常捕获的办法有很多，你可以使用 `error-first` 风格：

```text
function handleAsyncError(cb){
    setTimeout(() => {
        try{
            if(someError){
                 throw new Error('some error!');
            }else{
                cb(null, result);
            }
        }catch(e){
            cb(e)
        }
    }, 0)
}
```

这里的 `Error `对象包含 `message`、`stack`、`name `等属性，其中 `stack `属性中包含了错误的堆栈轨迹。而一个错误实例的堆栈轨迹包含了自构造函数之后的所有堆栈结构。

如何操作堆栈追踪呢？ Node 环境提供了 [`Error.captureStackTrace`](https://nodejs.org/api/errors.html#errors_error_capturestacktrace_targetobject_constructoropt)。

该函数有两个参数，第一个参数是一个对象，用来保存堆栈记录到其 `stack` 属性。第二个参数表示调用堆栈的终点，也就是只会保存其之前的堆栈记录，这样可以过滤掉对用户没用的信息。

```text
const ErrorStack = {};
function a(){
    console.log('a');
    Error.captureStackTrace(ErrorStack);
    //  Error.captureStackTrace(ErrorStack, b); 这样再试试
}
function b(){
    console.log('b');
    a();
}
function c(){
    console.log('c');
    b();
}
c();
ErrorStack.stack;
// 打印出
"Error
    at a (<anonymous>:4:11)
    at b (<anonymous>:8:5)
    at c (<anonymous>:12:5)
    at <anonymous>:14:1"
```

你可能会问这玩意儿有什么鸟用，这么说吧，比如牛逼哄哄的测试框架 Chai 就是使用了上面的[堆栈操作技术使堆栈跟踪更加与他们的用户相关](https://github.com/chaijs/chai/pull/922)。


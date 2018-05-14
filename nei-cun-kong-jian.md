---
description: 详解内存空间的知识点
---

# 内存空间

内存空间一个常常被前端工程师忽视的知识点。

如果想要成为一个优秀的前端儿，必须对内存空间有了解。

这个章节将会整理内存空间的部分知识点，抛砖引玉，更多的内容需要你去了解底层的知识。

### 一些误区

有一些常见的误区:

1. 前端工程师不需要了解内存空间的知识，因为我们有 GC。
2. GC 能够确保所有的 "垃圾" 都被准确的回收。
3. 分不清堆栈，堆栈就代表内存空间了。
4. 所有的基本类型都是存在栈中的，引用类型就是存在堆中的。
5. .....

为了干掉这些误区，我们需要一步一步的学习相关的知识点。

### 一个问题

比如这样一个问题: 

```text
const Foo = (x, y) => {
    this.x = x;
    this.y = y;
}
const foo = new Foo(1, 2); // 1
const bar = Foo(1, 2); // 2
```

这里面的 new 的作用是什么? 第 2 个表达式与之区别?

[MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/new) 上有答案：

> new constructor\(\[args\]\)  
>
> new 操作符的作用是创建一个用户定义的对象类型的实例或具有构造函数的内置对象的实例

所以你可能会这样回答：

new 的作用:

1.  一个继承自 _`Foo.prototype`_ 的新对象被创建
2.  [`this`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/this) 绑定到新创建的对象 `foo`
3.   `constructor `构建的对象就是 `new `操作返回的结果，或者是 第 1 步的对象。

二者的异同也就很明显了，第二个表达式只是单纯的执行了函数 `Foo`，并且不好的是在全局对象上绑定了 `x， y` 属性。

**这样的结果其实忽视掉了 new 操作最重要的一个作用，就是在堆中去申请一个空间存储这个对象的实体本身。**

为了理解这个，我们先认识堆栈 \( 通常所说的堆栈 = 栈，本书分开来说，避免混淆 \)。

### 数据结构中的堆栈

堆和栈既是计算机科学中的一种数据结构。同时内存分配中也有堆、栈的说法，不要混淆了。

我们的重点是了解内存分配中的堆栈，同时简单了解一下数据结构中的[堆](https://zh.wikipedia.org/wiki/%E5%A0%86_%28%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%29)、[栈](https://zh.wikipedia.org/wiki/%E5%A0%86%E6%A0%88)。

数据结构中的堆、栈：

1. 栈是一种串列形式的特殊数据类型，特点是 LIFO（后进先出），在 JS 中可以使用数组实现一个栈结构，基本的操作有压入\(push\)、弹出\(pop\)，分别对应栈顶部指针 +1、-1。
2. 堆，其实就是二叉堆，是二叉树的一种。可以想象成一颗倒过来的树，根节点要么是最小的（最小堆），要么是最大的（最大堆）。并且从根节点往下只能依次增大或者减小（堆序性）。堆结构有一个经典的案例就是[堆排序](https://zh.wikipedia.org/wiki/%E5%A0%86%E6%8E%92%E5%BA%8F)。

#### 堆排序

JavaScript 实现堆排序：

```text
const heapSort = target => {
    const arr = target.slice(0);
    // 交换的方法
    const swap = (i, j) => {
        let temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }
    // 确保父节点永远大于子节点
    const max_heap = (start, end) => {
        let dad = start;
        // 左边子节点
        let son = dad * 2 + 1;
        if(son >= end){
            return;
        }
        // 两个子节点中选择大的
        if(son+1<end && arr[son]<arr[son+1]){
            son++;
        }
        // 如果大于父节点，交换
        if(arr[dad] <= arr[son]){
            swap(dad, son);
            max_heap(son, end);
        }
    }
    let len = arr.length;
    // 最后一个父节点 Math.floor(len/2)-1
    for(let i = Math.floor(len/2)-1; i >= 0; i--){
         max_heap(i, len);
    }
    for(let i = len-1; i > 0; i--){
        swap(0, i);
        max_heap(0, i);
    }
    return arr;
}

heapSort([3,5,3,0,8,0,1,5,1,6,5,4,0,8]);
//[0, 0, 0, 1, 1, 3, 3, 4, 5, 5, 5, 6, 8, 8]
```

### 内存中的堆栈

我们以 C 为例来说明，对二者进行比较分析。

首先我们得大致了解一下内存分配，通俗的说就是程序在运行的时候是拷贝在 RAM 执行，它会为之分配出一些区域比如栈区、堆区、静态区等。

**内存分配策略：**

 静态区内存分配：编译器自动完成，时机是在编译或者连接的过程中，整个运行期都会存在，比如全局变量就是存在于数据段，所以少用全局变量可以提升启动速度。

 动态内存分配：也叫做堆分配，就是在运行的时候由程序员自己进行空间的申请。通俗的说就是事先也不知道要多大的内存，只有运行的时候才能确定，自己去 `malloc`。

栈分配：效率高，大小有限，可能溢出，比如函数执行时为其形参、局部变量分配栈空间。

**堆区、栈区比较：**

栈区是由系统自动进行分配空间的，而堆区的空间是由程序员自己通过 `new`、`malloc `等申请的。

栈上的空间是自动分配自动回收的，所以栈上的数据的生存周期只是在函数的运行过程中，运行后就释放掉，不可以再访问。而堆上的需要程序员手动去 `free `或者 `delete`，不然就可能导致内存泄漏。

栈空间的申请效率更高，由系统自动分配，而堆空间的申请效率较之要低效一点，因为要在空闲空间地址的链表上挨个找，找到空间大小满足的之后再从链表中移除该地址，分配给该程序，这一过程就决定了其效率肯定比不上前者了。

栈空间是高地址向低地址拓展的数据结果，是连续的内存，大小是系统定好了的，受限，有溢出的风险。堆空间是向高地址的数据结构，是不连续的，具有更灵活、自由、更大的空间（受限于 计算机系统中有效的虚拟内存）。

到这里为止基本可以解决前边说的 4 个误区，针对第 4 个误区，特地提一下，因为很多文章都直接武断的说 JS 中的基本数据类型（`string, number, bool, null, undefined, symbol`）是分配了栈空间的。

学习堆栈的目的是为了利用其结构特性来增强我们的代码表现，栈空间只需要简单的进行指针的上下移动就可以快速的实现内存的开辟和释放，对于局部变量这种需要快速存储和释放的数据，栈空间存储当然是最好的方式。

但是，JS 的 Closure 是一个神奇的东西，在 Closure 中的局部变量（哪怕是基本类型），因为闭包需要一直持有对其的访问，所以函数调用结束它也不应该被释放，证明它是存在于堆中的。

### GC

JS 可以直接访问保存在栈中的值，而对象的堆空间却不能直接访问，只能操作其对应的保存在栈中的引用（指针、地址）。当让该引用 = `null` 的时候，就可能会在下一次 GC 的时候释放该空间。

上面之所以说可能会在下一次回收，就是因为确定一块内存不再使用了是很复杂的。如果没有使用了，但是也没有释放该空间，就造成了内存泄漏。

#### GC 算法

早期的浏览器（其实我想吐槽 IE ）采用的是一种叫做[引用计数](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Memory_Management)的算法，原理就是如果一块内存没有指针指向它，那么它就可以被回收了。但是，诸如循环引用这种情况，它就无能为力了。

```text
function Foo(){
    let a = {};
    let b = {};
    a.name = b;
    b.name = a;
    return 'cycle reference!'
}
Foo();
```

`Foo` 函数执行完毕了，`a，b` 其实都不需要了，但是因为循环引用的存在，引用计数算法就会认为它们不应该回收，造成了内存的泄露。

现代浏览器基本都使用了各种版本的标记[清除算法](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Memory_Management)

> 这个算法假定设置一个叫做根（root）的对象（在 JavaScript 里，根是全局对象）。定期的，垃圾回收器将从根开始，找所有从根开始引用的对象，然后找这些对象引用的对象……从根开始，垃圾回收器将找到所有可以获得的对象和所有不能获得的对象。

意思很简单，就是从根出发，不能访问到的对象给个标记，等会儿就可以释放它了。这种算法可以解决上面的循环引用的问题。

GC 在执行的时候会阻塞其他的进程，至少在 50 ms 以上（这个数据可能是瞎掰的），这在普通的web项目上还好，在游戏中或者是动画连贯要求高的项目就不能忍了 。

针对上面的问题主要有 2 种优化的方式：

1. 分代 GC : 就是 Java 中的那一套，分成 `young `区和  `tenured `区，多去回收前者区域的，减少每次需要遍历的数量，从而减少耗时。
2. 增量 GC : 类比增量更新技术，就是一次整一点，下一次接着整。

对于浏览器环境，比如 Chrome 中，V8 中的所有的 JavaScript 对象都是通过堆来进行分配的。如果已申请的堆空闲内存不够分配新的对象，将继续申请堆内存，直到堆的大小超过 V8的限制为止。

它限制了堆内存在1.4G（64位），它为什么要做这个限制的原因也是因为 GC 在更大的堆内存下需要更多的耗时，而这样的阻塞是不能容忍的。

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


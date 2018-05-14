# 异常处理与堆栈追踪

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


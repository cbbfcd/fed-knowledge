# 一个问题

比如这样一个问题:

```text
const Foo = (x, y) => {    this.x = x;    this.y = y;}const foo = new Foo(1, 2); // 1const bar = Foo(1, 2); // 2
```

这里面的 new 的作用是什么? 第 2 个表达式与之区别?

​[MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/new) 上有答案：

> new constructor\(\[args\]\)
>
> new 操作符的作用是创建一个用户定义的对象类型的实例或具有构造函数的内置对象的实例

所以你可能会这样回答：

new 的作用:

1.  一个继承自 _`Foo.prototype`_ 的新对象被创建
2.  [`this`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/this) 绑定到新创建的对象 `foo`
3.  `constructor` 构建的对象就是 `new` 操作返回的结果，或者是 第 1 步的对象。

二者的异同也就很明显了，第二个表达式只是单纯的执行了函数 `Foo`，并且不好的是在全局对象上绑定了 `x， y` 属性。

**这样的结果其实忽视掉了 new 操作最重要的一个作用，就是在堆中去申请一个空间存储这个对象的实体本身。**

为了理解这个，我们先认识堆栈 \( 通常所说的堆栈 = 栈，本书分开来说，避免混淆 \)。


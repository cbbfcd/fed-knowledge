---
description: 编译过程的基础知识学习
---

# 编译过程基础

我们看看 C 的编译过程，我整理了一个流程图如下：

![c &#x7F16;&#x8BD1;&#x6D41;&#x7A0B;&#x56FE;](../.gitbook/assets/by.png)

### [**预处理**](https://zh.wikipedia.org/wiki/预处理器)：

**预处理器**是[程序](https://zh.wikipedia.org/wiki/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%A8%8B%E5%BA%8F)中处理输入数据，产生能用来输入到其他程序的数据的程序。

一些预处理器只能够执行相对简单的文本替换和[宏](https://zh.wikipedia.org/wiki/%E5%B7%A8%E9%9B%86)展开（比如[C预处理器](https://zh.wikipedia.org/wiki/C预处理器)处理 \#include 等指示），而另一些则有着完全成熟的[编程语言](https://zh.wikipedia.org/wiki/%E7%BC%96%E7%A8%8B%E8%AF%AD%E8%A8%80)的能力。

讲道理[预处理](https://zh.wikipedia.org/wiki/预处理器)的过程应该在[词法解析](https://zh.wikipedia.org/wiki/词法分析)之前比较合适，但是实际中（比如 GCC）中是一边进行词法解析一边进行预处理的。

### [词法分析](https://zh.wikipedia.org/wiki/词法分析)：

编译器中执行的过程大致包含词法分析、语法分析、中间代码的生成（这个不一定有）等。

词法分析是[计算机科学](https://zh.wikipedia.org/wiki/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%A7%91%E5%AD%A6)中将字符序列转换为**标记**（token）序列的过程。一般是以一个函数的形式存在供语法分析器调用。

意思就是编译器并不能直接看出你的代码如`int a =  strlen("hello world!");` 是什么意思。

它只能通过[状态机](https://zh.wikipedia.org/wiki/有限状态机)的方式实现一个词法分析器，然后挨个字符去读，然后根据字符的特性切换状态，比如 读到` 'int'`，就切换状态的到 **Identifier**（我瞎掰的一个状态，后面也是，这个看代码怎么定义了），读到 `'\n'` 就切换到 **Normal **状态等。（[这里](https://zhuanlan.zhihu.com/p/19878146)有一个 Java 实现词法解析器的例子，可以加深认识）。

维基百科的一个例子：

```python
sum=3+2;
```

上面的一行代码（注意没有空格）解析出来的 token 可能这样：

| 语素 | 标记类型 |
| --- | --- | --- | --- | --- | --- | --- |
| sum | 标识符 |
| = | 赋值操作符 |
| 3 | 数字 |
| + | 加法操作符 |
| 2 | 数字 |
| ; | 语句结束 |




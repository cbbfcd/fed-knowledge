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






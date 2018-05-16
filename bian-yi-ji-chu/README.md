# 编译基础

之前看过一个文章，主要阐述了为什么说懂编译原理是可以为所欲为的。

比如说他举了个例子，如何实现绕过微信小程序的审核实现热更新，这就需要懂编译原理的知识，然后让你的代码重新拥有 `eval` 的能力。

最后，还是那句话，[前端儿真的需要懂一点点编译原理](http://fullstack.blog/2017/06/24/大前端开发者需要了解的基础编译原理和语言知识/?nsukey=rUvDyBYKXUFLZHzd1GO76pWNdqXvVtKwhugdplbqb%2FlVbNuGn5rTFddfF55VoZHw4NqHboyWRfLOCH6CTmq0RtRStYuig6Kx%2FMSsM%2FoCsrH6CaHCyAu2OdkNn0CK2l08PCYTV4F9gBHVoMbUkfnXsBvbaBtarJbsz2%2BBo2HQg73lORR6fBPsiv07X%2F%2BQsRGq)。

JavaScript 是一门[解释型的语言](https://zh.wikipedia.org/wiki/%E7%9B%B4%E8%AD%AF%E8%AA%9E%E8%A8%80)，因此编译器的相关基础知识，我们将以 C 语言、[GCC](https://zh.wikipedia.org/wiki/GCC) 为例。

当然，JavaScript 解释器、[WebAssembly](https://developer.mozilla.org/zh-CN/docs/WebAssembly) 的知识是我们需要重点关注的。

编译原理真的挺难的，所以这一部分的知识点如果有不对的地方请及时指正。


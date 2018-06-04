# Asm.js

[Asm.js ](https://github.com/dherman/asm.js/)

这是一个具有前瞻性的意外发现，在研究 LLVM 过程中得到的灵感启发下的产物。

通过自己实现一个编译器后端，将 C/CPP 转换成 JavaScript 的子集，也就是 Asm.js。为了转换过程的顺利，需要 Asm 与 C 保持一致的特点：

1. 静态类型
2. 没有自动GC

虽然对 Asm.js 并不会有太多的介绍，但是我觉得它在 WebAssembly 的竞争下依然有生存的空间，毕竟是兼容性完美。

更多的资料可以参考：[点这里](http://www.ruanyifeng.com/blog/2017/09/asmjs_emscripten.html)，[点这里](https://www.oschina.net/translate/asmjs-javascript-compile-target) 


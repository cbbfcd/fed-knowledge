# WebAssembly 入门

### 什么是 [WebAssembly](https://developer.mozilla.org/en-US/docs/WebAssembly)

首先最直接的感受就是，这货非常的快、快、快！

如果你了解前一节的内容 Asm.js ，你会发觉二者就是继承与优化的关系，WebAssembly 继承了 Asm.js 的思想，但是做了更快的优化，就是不再通过 LLVM 编译成一个 Asm.js 文件，而直接是后缀为 .wasm 的二进制文件，体积更小，效率更高！


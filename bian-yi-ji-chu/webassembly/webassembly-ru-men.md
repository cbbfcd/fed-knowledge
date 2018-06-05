# WebAssembly 入门

### 什么是 [WebAssembly](https://developer.mozilla.org/en-US/docs/WebAssembly)

> WebAssembly是一种新的编码方式，可以在现代的网络浏览器中运行 － 它是一种低级的类汇编语言，具有紧凑的二进制格式，可以接近原生的性能运行，并为诸如C / C ++等语言提供一个编译目标，以便它们可以在Web上运行。它也被设计为可以与JavaScript共存，允许两者一起工作。

首先最直接的感受就是，这货会非常的快、快、快！

其次还有什么特点呢？-- [高效、安全、开放、标准](http://webassembly.org.cn/)。

如果你了解前一节的内容 Asm.js ，你会发觉二者就是继承与优化的关系，WebAssembly 继承了 Asm.js 的思想，但是做了更快的优化，就是不再通过 LLVM 编译成一个 Asm.js 文件，而直接是后缀为 .wasm 的二进制文件，体积更小，[效率更高](https://zhuanlan.zhihu.com/p/25773367)！

总结：

WebAssembly  是一种新的编码方式，字节码技术。将 C/CPP 通过编译器前端转成 LLVM IR，然后通过编译器后端工具（这里指的是官方提供的 Emscripten）转成 .wasm 格式的文件，这个其实就是 WebAssembly 。这个文件可以理解成目标汇编语言，浏览器能够极其迅速的将其转换成机器代码执行，所以其效率惊人。可以在 [MDN](https://developer.mozilla.org/zh-CN/docs/WebAssembly/Concepts) 学习更多的概念。

### WebAssembly 实践

这里分享好几个给力的文章，依样画葫芦即可。

[点这里](https://segmentfault.com/a/1190000008402872)

[点这里](https://zhuanlan.zhihu.com/p/27410280)

[点这里](http://webassembly.org.cn/getting-started/developers-guide/)

[API ](https://developer.mozilla.org/zh-CN/docs/WebAssembly)

总的来说，这是一项创新且优秀的技术。W3C 专门成立了小组来推动这个技术的发展，可以展望未来，可以直接像引入一个 lodash 模块一下引入一个 WebAssembly 的模块，对于前端开发来说太幸福了。

弊端就是前端开发需要多掌握一门 C/CPP 了。


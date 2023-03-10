# 将 emscripten 嵌入 Node.js 库中

> 原文：<https://dev.to/azure/embedding-emscripten-in-a-nodejs-library-3hdm>

我最近一直在尝试 Web 组装，现在我正处于通过 WASM 在 Node.js 中运行我的[无线 LED 控制系统](https://github.com/nebrius/raver-lights)的[消息堆栈](https://github.com/nebrius/RaverLightsMessaging)的早期阶段。我已经让它运行起来了(耶！)，但现在相当 hacky！).

## 场景

我的库是用 C++写的。我有意避免使用 C++标准库中的任何东西，而是专门使用 C 标准库(它更具可移植性，我怀疑编译起来不那么复杂)。不管怎样，这两个标准库都是需要编译到输出中的运行时库。如果您是一名 JavaScript 开发人员，不知道我在说什么，想象一下，如果 jQuery 或下划线/Lodash 被定义为 JavaScript 规范的一部分，并与 Node.js 捆绑在一起，但仍然是单独的库。这是 C/C++标准库。

所有的 C++编译器都内置了这些库，你不需要做什么特别的事情就可以使用它们。emscripten 也提供了这些功能的实现，但是 IME 仍然很难使用。你必须改变编译标志。具体来说，你必须移除我在之前关于 WASM 的博文中提到的`-s ONLY_MY_CODE=1`标志。如果这是你对帖子中所有内容的唯一更改，你的应用程序将会崩溃，如下所示:

```
[LinkError: WebAssembly Instantiation: Import #1 module="env" function="nullFunc_ii" error: function import requires a callable] 
```

嗯，请问一下，`nullFunc_ii`到底是什么，我为什么要关心？

据我所知，事情是这样的。emscripten 编译您的代码，并注入运行 C++应用程序所需的任何和所有运行时库。这包括前面提到的标准库，但也包括其他一些东西。最值得注意的是，emscripten 注入了一些运行时库来处理诸如堆栈溢出、异常处理、segfaults 等事情。我有 75%的把握认为`nullFunc_xxx`方法是后者的一部分。

这些方法都是在 *JavaScript* 中定义的，而不是 C++，因此不包含在`output.wasm`文件中。相反，它们包含在一个名为`output.js`的 JavaScript 运行时文件中(给定我的`-o`标志值)。

## 我的 Hacky 解

那么，我是如何解决这个问题的呢？我的第一步是检查关于[输出文件和格式](https://emscripten.org/docs/compiling/Building-Projects.html#emscripten-build-output-files)以及各种 [emscripten 特定配置标志](https://github.com/emscripten-core/emscripten/blob/master/src/settings.js)的 emscripten 文档。

据我所知，我想做的事是不可能的。emscripten 允许你编译代码(通过`-s ONLY_MY_CODE=1`标志)，或者编译一个包含`void main() {}`(即*而不是*库)的完整应用程序。嗯嗯...好的...？

经过大量的试验和错误，我找到了一个看起来很有效的*解决方案。*

首先，这是我正在使用的完整的编译命令(注意:你可以忽略`ERROR_ON_UNDEFINED_SYMBOLS`部分，我将在后面的文章中讨论):

```
em++ -s WASM=1 -s ERROR_ON_UNDEFINED_SYMBOLS=0 -s EXPORTED_FUNCTIONS=\"['_init','_loop','_setWaveParameters']\" -std=c++11 -Isrc -g4 -o js-dist/output.js js/*.cpp 
```

请注意我们在命令中是如何使用`-o js-dist/output.js`的。这告诉 emscripten 生成一个 JavaScript 运行时文件。该文件*打算*用作“主”文件，即*整个*应用程序。我们想把它作为一个库，而不是一个应用程序。这里有很多我们需要的东西，最值得注意的是它创建的两个变量`asmGlobalArg`和`asmLibraryArg`。这些变量定义了所有的`nullFunc_xxx`方法。不过，这些变量没有以任何方式导出，而且据我所知，它们并不意味着被直接消费。

不管结果如何，我们都要做！我写了一个脚本自动黑掉这个文件，内容如下:

```
const { readFileSync, writeFileSync } = require('fs');
const { join } = require('path');

const OUTPUT_FILE = join(__dirname, '..', 'js-dist', 'output.js');

let source = readFileSync(OUTPUT_FILE).toString();

source = source.replace('var asmGlobalArg =', 'var asmGlobalArg = module.exports.asmGlobalArg =');
source = source.replace('var asmLibraryArg =', 'var asmLibraryArg = module.exports.asmLibraryArg =');

writeFileSync(OUTPUT_FILE, source); 
```

现在我们可以将这些变量导入到我们的主文件中(现在用 TypeScript FWIW 编写):

```
import { readFile } from 'fs';
import { join } from 'path';

import { asmGlobalArg, asmLibraryArg } from './output';

let wasmExports: WebAssembly.ResultObject | undefined;
const memory = new WebAssembly.Memory({ initial: 256, maximum: 256 });

readFile(join(__dirname, 'output.wasm'), (readErr, buf) => {
  const bytes = new Uint8Array(buf);
  const env = {
    ...asmLibraryArg,
    table: new WebAssembly.Table({
      'initial': 192,
      'maximum': 192,
      'element': 'anyfunc'
    }),
    __table_base: 0,
    memory,
    __memory_base: 1024,
    STACKTOP: 0,
    STACK_MAX: memory.buffer.byteLength
  };
  const global = {
    ...asmGlobalArg
  };
  WebAssembly.instantiate(bytes, { env, global })
    .then((result) => {
      wasmExports = result;
      wasmExports.instance.exports._init(deviceId);
    })
}); 
```

而且很管用！但在我看来也很难看。不过我很兴奋，因为我离将这个系统与 [Azure IoT Edge](https://docs.microsoft.com/en-us/azure/iot-edge/?WT.mc_id=devto-blog-brhugh) 集成又近了一步，这样我就可以从云中控制我的 led 灯了！

## 给大家提个问题

我错过了什么吗？有合适的方法吗？我很难相信 emscripten 没有编译一个包含所有必要运行时库的库的选项，但我似乎想不出如何编译。
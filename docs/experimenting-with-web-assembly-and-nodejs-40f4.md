# 试用 Web 程序集和 Node.js

> 原文：<https://dev.to/azure/experimenting-with-web-assembly-and-nodejs-40f4>

有段时间一直想玩一下 [Web Assembly](https://webassembly.org/) ，终于有机会了。因为我主要是 Node.js 开发人员，所以我想在这里使用它，而不是在浏览器中。但是关于 Web 组装的文档并不多，而且 API 也在快速变化。我终于让它工作了，以下是我是如何做到的。

我基本上遵循了 [emscripten 入门指南](https://emscripten.org/docs/getting_started/index.html)，并对其进行了调整以适应 Node.js 并考虑到了该文档中过时的部分。

## 安装脚本

第一步是安装 emscripten，在 [emscripten 的网站](https://emscripten.org/docs/getting_started/downloads.html)上有很好的记载。我把它安装在运行 Ubuntu 的 Linux 的 Windows 子系统中，所以如果你使用的是不同的操作系统，可以使用 YMMV。

第一步是安装一些依赖项。这需要一段时间，所以我建议在这段时间里泡一杯好茶。这次我选了一根漂亮的银针:)

```
# Install Node.js using NodeSource's Ubuntu PPA, if not already installed
curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -

# Install dependencies, as defined in emscripten's getting started guide
sudo apt-get install python2.7 nodejs cmake default-jre 
```

然后我们就可以安装 emscripten 本身了。这也需要一些时间，所以多喝点茶吧。

```
git clone https://github.com/emscripten-core/emsdk.git
cd emsdk
./emsdk install latest
./emsdk activate latest
source ./emsdk_env.sh 
```

我在这个方法上遇到了一点问题。我不知道我是否犯了一个错误，或者事情只是被锁定。无论如何，emscripten 自己安装了 Node.js 的 8.x.x 版本，并覆盖了我的 Node.js 的 10.x.x 安装。由于我对我的 Node.js 代码使用了 async/await(这实际上需要 Node 10，如果不是在理论上的话)，我需要通过编辑`~/.emscripten`来手动覆盖它，以便下面的行与下面的代码匹配:

```
NODE_JS = '/usr/bin/node' # This line used to have a super long path to the emsdk directory 
```

我在交换 emscripten 期望的 Node.js 版本时从未遇到任何问题，但一如既往，YMMV。

## 创建代码

首先，我创建了一个非常简单的 C 文件，感谢 Dynamsoft 的一篇名为`test.c`的旧博文。(注:这篇博文中的说明不再起作用)。

```
int add(int a, int b) {
  return a + b;
} 
```

然后，我创建了消耗这个 C 模块的 JavaScript 文件:

```
const fs = require('fs').promises;
const util = require('util');

async function run() {
  async function createWebAssembly(bytes) {
    const memory = new WebAssembly.Memory({ initial: 256, maximum: 256 });
    const env = {
      abortStackOverflow: (err) => { throw new Error(`overflow: ${err}`); },
      table: new WebAssembly.Table({ initial: 0, maximum: 0, element: 'anyfunc' }),
      __table_base: 0,
      memory,
      __memory_base: 1024,
      STACKTOP: 0,
      STACK_MAX: memory.buffer.byteLength,
    };
    return WebAssembly.instantiate(bytes, { env });
  }

  const result = await createWebAssembly(new Uint8Array(await fs.readFile('./output.wasm')));
  console.log(util.inspect(result, true, 0));
  console.log(result.instance.exports._add(9, 9));
}
run(); 
```

关于这段 JavaScript 代码，需要注意一些事情。首先，该代码是来自 Dynamsoft 博客文章和 emscripten 入门指南的代码的 masheup。我建议阅读《emscripten 入门指南》,以深入了解这里发生的事情。接下来要注意的是`__table_base`和`__memory_base`条目。在 emscripten 文档和我找到的几乎所有其他文档中，它们被命名为`tableBase`和`memoryBase`，但是运行时会崩溃。我猜 API 最近发生了变化。向[致敬，这个要点](https://gist.github.com/kripken/59c67556dc03bb6d57052fedef1e61ab#gistcomment-2779875)为我指出了正确的方向。

## 运行代码

现在我们已经写好了文件，是时候运行代码了。首先，我们用下面的命令编译这段代码:

```
emcc -s WASM=1 -s ONLY_MY_CODE=1 -s EXPORTED_FUNCTIONS="['_add']" -o output.js *.c 
```

这个命令将生成 JavaScript 代码中引用的文件`output.wasm`。现在我们准备好运行我们的代码了！

```
> node index.js
{ instance: Instance [WebAssembly.Instance] {},
  module: Module [WebAssembly.Module] {} }
18 
```

而且很管用！我们确实可以将 9 和 9 相加得到 18，从 JavaScript 调用 C++并返回，所有这些都使用 Web 汇编！

我的下一步是从我的 [LED 同步和动画项目](https://github.com/nebrius/raver-lights)中获取 [C++消息堆栈](https://github.com/nebrius/RaverLightsMessaging)，将其包装在 Web Assembly 中，以便 Node.js 可以与我的主板进行对话，并将其与 [Azure IoT Edge](https://docs.microsoft.com/en-us/azure/iot-edge/?WT.mc_id=devto-blog-brhugh) 集成，以便我可以从云中控制我的 LED！
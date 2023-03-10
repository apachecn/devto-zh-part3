# 锈中的 WASM 没有节点

> 原文：<https://dev.to/dandyvica/wasm-in-rust-without-nodejs-2e0c>

在本文中，我将讨论如何从 JavaScript 调用 Rust WASM 方法，但不使用 NodeJS。到目前为止，我谷歌的几乎所有例子都只是描述了在 NodeJS 中使用 Rust 进行 WASM(但是现在在 *wasm-bindgen* 文档中有更好的解释)。我觉得太复杂了，难以把握整体思路。最好(至少对我来说)不要让太多玩家参与到游戏中来，解开整个过程。

由于 Rust 已经安装在我的 Linux 机器上，我将描述如何从头开始安装 Rust 和 WASM。

# 准备生锈

## 重装铁锈

这不是一个强制性的步骤，但当我试图安装 WASM 时遇到了一些问题，我完全重新安装了 Rust:

```
$ rustup self uninstall 
```

Enter fullscreen mode Exit fullscreen mode

然后重新安装所有的工具链:

```
$ curl https://sh.rustup.rs -sSf | sh 
```

Enter fullscreen mode Exit fullscreen mode

并验证其安装是否正确:

```
$ rustc --version
rustc 1.34.2 (6c2484dc3 2019-05-13) 
```

Enter fullscreen mode Exit fullscreen mode

安装附加实用程序:

```
$ rustup component add rustfmt 
```

Enter fullscreen mode Exit fullscreen mode

```
$ rustup component add rls rust-analysis rust-src 
```

Enter fullscreen mode Exit fullscreen mode

## 安装 WASM 特定的扩展和实用程序

现在是时候安装 WASM 扩展来直接编译 Rust 到 WASM 了:

```
rustup target add wasm32-unknown-unknown 
```

Enter fullscreen mode Exit fullscreen mode

然后，安装 *wasm-gc* 实用程序，它允许从编译中剥离产生的 wasm 二进制文件:

```
$ cargo install wasm-gc 
```

Enter fullscreen mode Exit fullscreen mode

## 安装 *wabt*

WAT 文件是二进制 WASM 的 ASCII 表示。可以转换二进制*。wasm* 文件到一个*中。wat* ASCII 文件与 *wasm2wat* 命令。按照[https://github.com/WebAssembly/wabt](https://github.com/WebAssembly/wabt)的说明安装 *wabt* 工具包。

## 安装 *wasm-bindgen*

```
$  cargo install wasm-bindgen-cli 
```

Enter fullscreen mode Exit fullscreen mode

# 一个简单的例子:2 个数相加

## 准备你的项目

现在该创建一个新项目了:

```
# --lib for creating a library instead of a binary
$ cargo new --lib wasm_sample 
```

Enter fullscreen mode Exit fullscreen mode

并编辑 *Cargo.toml* 文件添加*【lib】*标签:

```
[package]
name = "wasm_sample"
version = "0.1.0"
authors = ["dandyvica <dandyvica@gmail.com>"]
edition = "2018"

[dependencies]

[lib]
crate-type =["cdylib"] 
```

Enter fullscreen mode Exit fullscreen mode

意思是我们想创建一个动态库。

## 一个简单的锈 WASM 函数

现在让我们编写一个非常简单的函数:

```
// this is a simple interface without leveraging from the Rust language bells and whistles
#[no_mangle]
pub extern fn add(x: u32, y: u32) -> u32 {
    x + y
} 
```

Enter fullscreen mode Exit fullscreen mode

## 编译到 WASM

现在可以用
编译代码了

```
#  debug: the target is target/wasm32-unknown-unknown/debug/wasm_sample.wasm
$  cargo build --target wasm32-unknown-unknown 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
#  release: the target is target/wasm32-unknown-unknown/release/wasm_sample.wasm
$  cargo build --target wasm32-unknown-unknown --release 
```

Enter fullscreen mode Exit fullscreen mode

由此产生的*。wasm* 文件约 822 KB。使用 *wasm-gc* :
将其分解

```
$  wasm-gc target/wasm32-unknown-unknown/debug/wasm_sample.wasm 
```

Enter fullscreen mode Exit fullscreen mode

结果 *wasm_sample.wasm* 是 16 KB。

使用 *wasm2wat* 实用程序，我们可以检查 *add* 函数是否确实被导出:

```
$  cd target/wasm32-unknown-unknown/debug
$  wasm2wat wasm_sample.wasm -o wasm_sample.wat
$  grep export wasm_sample.wat
 (export "add" (func $add))
 (export "__rustc_debug_gdb_scripts_section__" (global 3)) 
```

Enter fullscreen mode Exit fullscreen mode

## 用 HTML 和 JavaScript 粘合

在 Rust 项目的根结构中创建一个`www`目录:

```
$  mkdir www
$  cd www 
```

Enter fullscreen mode Exit fullscreen mode

并创建一个`index.html`文件:

```
<!DOCTYPE html>
<html>
  <head>
    <script src="wasm_sample.js"></script>
  <head>
  <body>
    <form onSubmit="return false">
      Enter X: <input type="number" name="X" required><br>
      Enter Y: <input type="number" name="Y" required><br><br>
      <input 
        type="submit" 
        value="X+Y=" 
        onClick="result.innerText = wasm_add(X.value,Y.value)">
      <label id="result"></label>   
    </form>       
  </body>
<html> 
```

Enter fullscreen mode Exit fullscreen mode

并创建`wasm_sample.js` JavaScript 文件:

```
// use this JS API to load the WASM module and start using it in a streaming mode
// i.e. without having to wait
WebAssembly.instantiateStreaming(fetch("wasm_sample.wasm"))
    .then(wasmModule => {
        // this saves the exported function from WASM module for use in JS
        wasm_add = wasmModule.instance.exports.add;
    }); 
```

Enter fullscreen mode Exit fullscreen mode

此外，将 WASM 模块复制到您的`www`目录，以排除目录麻烦:

```
$  cp ../target/wasm32-unknown-unknown/debug/wasm_sample.wasm . 
```

Enter fullscreen mode Exit fullscreen mode

## 运行 web 服务器

在 Linux 上，可以通过 Python 获得一个简单的 web 服务器:

```
$  python3 -m http.server 
```

Enter fullscreen mode Exit fullscreen mode

但是这个实现不包括导入 WASM 模块所需的`application/wasm` mime 类型。

你可以在 GitHub gist 上找到一个处理 WASM mime 类型的例子。将以下内容保存到`server.py`文件中:

```
import http.server
import socketserver

PORT = 8000

Handler = http.server.SimpleHTTPRequestHandler
Handler.extensions_map.update({
    '.wasm': 'application/wasm',
})

socketserver.TCPServer.allow_reuse_address = True
with socketserver.TCPServer(("", PORT), Handler) as httpd:
    httpd.allow_reuse_address = True
    print("serving at port", PORT)
    httpd.serve_forever() 
```

Enter fullscreen mode Exit fullscreen mode

然后启动 web 服务器:

```
$  python3 server.py 
```

Enter fullscreen mode Exit fullscreen mode

然后，启动你最喜欢的支持 WASM et 的浏览器前往 [http://localhost:8000](http://localhost:8000)

[![WASM add](img/b3805577020bae995503a385d1854a85.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FxYBeWou--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/v1phz09zooyvqwx94fpv.png)

# 使用 *wasm-bindgen* 机箱

前面的例子展示了 Rust 函数，但是没有利用 Rust 的能力，比如在 JS 和 Rust 之间交换字符串或结构。为了实现这一点，您需要将`wasm-bindgen`箱子导入到您的`Cargo.toml`中。

如[https://rustwasm.github.io/wasm-bindgen/:](https://rustwasm.github.io/wasm-bindgen/:)中所述

> 这个项目允许 JS/wasm 与字符串、JS 对象、>类等进行通信，而不是纯粹的整数和浮点数。

现在我们要从 JS 来回传递一个字符串给 Rust。

## 添加 *wasm-bindgen* 板条箱

将依赖项添加到 *Cargo.toml* 文件:
中的 *wasm-bindgen*

```
[dependencies]
wasm-bindgen = "0.2.45" 
```

Enter fullscreen mode Exit fullscreen mode

## 修改 *lib.rs* 源文件

```
extern crate wasm_bindgen;
use wasm_bindgen::prelude::*;

// Reverse a string coming from JS 
#[wasm_bindgen]
pub fn reverse(s: String) -> String {
    s.chars().rev().collect::<String>()
} 
```

Enter fullscreen mode Exit fullscreen mode

并编译:

```
$ cargo build --target wasm32-unknown-unknown 
```

Enter fullscreen mode Exit fullscreen mode

## 用铁锈包扎

下一步是运行 *wasm-bindgen* 来利用 wasm 模块和 JavaScript:
之间的高级交互

```
$  cd target/wasm32-unknown-unknown/debug
$  wasm-bindgen --target web --no-typescript --out-dir . wasm_sample.wasm 
```

Enter fullscreen mode Exit fullscreen mode

这将创建 2 个 WASM 文件: *wasm_sample.wasm* 和 *wasm_sample_bg.wasm* 。这是我们最后一次使用。

## 剥下 WASM 双星

```
$ wasm-gc wasm_sample_bg.wasm 
```

Enter fullscreen mode Exit fullscreen mode

并将 WASM 二进制( *wasm_sample_bg.wasm* )和生成的 JS 模块( *wasm_sample.js* )复制到 *www* 目录下

## 编辑【index.html】的*文件*

最后，编辑*index.html*文件如下:

```
<html>
  <head>
    <meta content="text/html;charset=utf-8" http-equiv="Content-Type"/>
  </head>
  <body>
    <!-- Note the usage of `type=module` here as this is an ES6 module -->
    <script type="module">
      import { reverse, default as init } from './wasm_sample.js';

      async function run() {
        await init('./wasm_sample_bg.wasm');

        // make the function available to the browser
        window.reverse = reverse;
      }

      run();
    </script>

    <form onSubmit="return false">
        <textarea rows="40" cols="50" name="lipsum">
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.         
Sed ut perspiciatis unde omnis iste natus error sit voluptatem accusantium doloremque laudantium, totam rem aperiam, eaque ipsa quae ab illo inventore veritatis et quasi architecto beatae vitae dicta sunt explicabo. Nemo enim ipsam voluptatem quia voluptas sit aspernatur aut odit aut fugit, sed quia consequuntur magni dolores eos qui ratione voluptatem sequi nesciunt. Neque porro quisquam est, qui dolorem ipsum quia dolor sit amet, consectetur, adipisci velit, sed quia non numquam eius modi tempora incidunt ut labore et dolore magnam aliquam quaerat voluptatem. Ut enim ad minima veniam, quis nostrum exercitationem ullam corporis suscipit laboriosam, nisi ut aliquid ex ea commodi consequatur? Quis autem vel eum iure reprehenderit qui in ea voluptate velit esse quam nihil molestiae consequatur, vel illum qui dolorem eum fugiat quo voluptas nulla pariatur?
        </textarea><br> 
        <input 
            type="submit" 
            value="Reverse string" 
            onClick="lipsum.value = reverse(lipsum.value)">
    </form> 
  </body>

</html> 
```

Enter fullscreen mode Exit fullscreen mode

它将反转文本区域中的整个短语:

[![WASM](img/fd3c89bd700226bd202fd4635e85c0fd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0CiP5tYe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zvd2qrthgszupqtz7l89.png)
[![WASM](img/bba99135433dab5b7f0591888527cdfe.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--Y1ZbtrB2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/83tcg6frew6axmrjz5mk.png)

希望这有所帮助！

> 马库斯·斯皮斯克在 Unsplash 上拍摄的照片
# 使用 bindgen 的 Rust + WASM

> 原文：<https://dev.to/h_ajsf/rust--wasm-using-bindgen-49b4>

这是一个使用`bindgen`的高级主题，是我上一篇[帖子](https://dev.to/h_ajsf/simple-rust--wasm-example-5b1m)的第二步

1.  创建项目文件夹`utils2`
2.  cd 到这个文件夹
3.  从此文件夹创建您的库:

```
// install rust if not yet done
// curl https://sh.rustup.rs -sSf | sh
mkdir utils2
cd utils 2
cargo init --lib

// Or it can be created in one line as:
cargo new utils2 --lib
cd utils2 
```

1.  现在只在夜间支持将默认工具链设为`nightly`为`wasm`:

```
rustup override set nightly
// Add was32 target if not yet installed
rustup target add wasm32-unknown-unknown --toolchain nightly 
```

注:
平台由平台的`Target Triplet`定义，即:`machine-vendor-operatingsystem`:

1.  机器 CPU 家族名称/型号:wasm32，
2.  厂商:没有具体的，所以不知道，
3.  操作系统名称:没有具体的，所以它是未知的。

4.  通过运行
    创建`npm`包文件

```
npm init 
```

以上将创建`package.json`。

1.  安装`webpack` npm 需求，将创建`node_modules`文件夹:

```
// brew install node
npm install -D webpack webpack-cli webpack-dev-server 
```

以上将改变`package.json`并使它看起来像::

```
{  "name":  "utils2",  "version":  "1.0.0",  "description":  "",  "main":  "index.js",  "scripts":  {  "test":  "echo \"Error: no test specified\" && exit 1"  },  "author":  "",  "license":  "ISC",  "devDependencies":  {  "webpack":  "^4.27.1",  "webpack-cli":  "^3.1.2",  "webpack-dev-server":  "^3.1.10"  }  } 
```

1.  创建`domUtils.js`、`index.html`、`index.js`和`webpack.config.js`文件，结构如下:

```
// if you need to install tree, it can be done by
// brew install tree
$ tree .
.
├── Cargo.lock
├── Cargo.toml
├── domUtils.js
├── index.html
├── index.js
├── package.json
├── src
│   └── lib.rs
└── webpack.config.js 
```

1.  打开你的想法中的文件夹文件，并更新它们，如第 11 点所示

```
$ idea . 
```

1.  使用`wasm-pack`构建 rust wasm 库，如下所示:

```
// cargo install wasm-pack
wasm-pack build
// this will create the standard target folder, and additional folder called pkg
// the pkg folder tree is:
├── pkg
│   ├── utils2.d.ts
│   ├── utils2.js
│   ├── utils2_bg.d.ts
│   └── utils2_bg.wasm 
```

1.  以下列身份运行服务器:

```
npx webpack-dev-server 
```

1.  文件:

Cargo.toml

```
[package]
name = "utils2"
version = "0.1.0"
authors = ["Hasan Yousef"]
edition = "2018"

[dependencies]
wasm-bindgen = "0.2.29"

[lib]
crate-type = ["cdylib"] 
```

domutils . js〔t0〕

```
export const appendStringToBody = (value) => {
  const text = document.createTextNode(value);
  document.body.appendChild(text);
} 
```

libs.rs:

```
use wasm_bindgen::prelude::*;

// Define the JS function signature
#[wasm_bindgen(module = "../domUtils")]
extern {
    fn appendStringToBody(s: &str);
}

#[wasm_bindgen]
pub fn run(my_text :&str) {
    // Call the JavaScript function
    #[allow(unused_unsafe)]
    unsafe {
        appendStringToBody(my_text);
    }
} 
```

web pack . config . js

```
const path = require("path");

module.exports = {
  entry: "./index.js",
  output: {
    path: path.resolve(__dirname, "dist"),
    filename: "index.js",
  },
  mode: "development"
}; 
```

index.js

```
import("./pkg/my_app").then(wasmModule => {
  wasmModule.run("hi there");
}); 
```

index.html

```
<!DOCTYPE html>
<html>
<head>
    <script src="./index.js"></script>
    <head>
<body></body>
<html> 
```
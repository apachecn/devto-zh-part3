# 简单的铁锈+ WASM 的例子

> 原文：<https://dev.to/h_ajsf/simple-rust--wasm-example-5b1m>

1.  安装铁锈

```
$ brew install rustup
$ rustup-init 
```

Enter fullscreen mode Exit fullscreen mode

1.  将默认刀具链设置为`nightly`

```
$ rustup default nightly 
```

Enter fullscreen mode Exit fullscreen mode

1.  Ass `wasm`目标

```
$ rustup target add wasm32-unknown-unknown 
```

Enter fullscreen mode Exit fullscreen mode

1.  安装`wasm-gc`工具，从生成的 WebAssembly 模块中删除所有不需要的导出、导入、函数等等。

```
$ cargo instal wasm-gc 
```

Enter fullscreen mode Exit fullscreen mode

1.  install`https`运行一个 web 服务器，提供来自当前目录的静态文件

```
$ cargo instal https 
```

Enter fullscreen mode Exit fullscreen mode

1.  创建 rust app，用你的 IDE 打开它的文件(我用的是 idea)

```
$ cargo new --lib utils
$ cd utils
$ idea . 
```

Enter fullscreen mode Exit fullscreen mode

7.在`cargo.toml` :
中定义 rust CDI 库类型

```
[package]
name = "utils"
version = "0.1.0"
authors = ["Hasan Yousef <hasan.ajsf@gmail.com>"]
edition = "2018"

[dependencies]

[lib]
crate-type =["cdylib"] 
```

Enter fullscreen mode Exit fullscreen mode

1.  定义`extern`功能:

```
#[no_mangle]
pub extern fn add_one(x: u32) -> u32 {
    x + 1
} 
```

Enter fullscreen mode Exit fullscreen mode

创建接口需要使用关键字`extern`,这样就可以从其他语言调用这个函数。
`no-mangle`注释告诉 Rust 编译器不要破坏这个函数的名字。

1.  构建 wasm 文件:

```
$ cargo build --target wasm32-unknown-unknown --release 
```

Enter fullscreen mode Exit fullscreen mode

1.  运行`wasm-gc`来优化 wasm 文件:

```
$ wasm-gc target/wasm32-unknown-unknown/release/utils.wasm -o utils.gc.wasm 
```

Enter fullscreen mode Exit fullscreen mode

1.  创建`index.html`文件，通过 javascript 调用`wasm`模块:

```
<!DOCTYPE html>
<html>
  <head>
    <script> 
      WebAssembly.instantiateStreaming(fetch("utils.gc.wasm"))
        .then(wasmModule => {
          const result = wasmModeult.instance.exports.add_one(3);
          const text = document.createTextNode(result);
          document.body.appendChild(text);
        });
    </script>
  <head>
  <body></body>
<html> 
```

Enter fullscreen mode Exit fullscreen mode

相反，使用 instantiateStreaming，我们可以一次性地对 WebAssembly 模块进行流处理、编译和实例化。

1.  运行静态文件服务器:

```
$ http 
```

Enter fullscreen mode Exit fullscreen mode

1.  在`localhost:8000`打开浏览器

**前进**

如果你想与`JavaScript`功能交互，你需要:

1.  在 rust 文件中定义这些函数签名
2.  在 javascript 文件中定义这些函数之间的桥/包装器

所以，如果想调用 javascript `alert`和另一个函数，比如说 addOne，那么上面的`main.rs`和`index.html`文件将如下所示:

main.rs:

```
// To call a JavaScript function
// 1\. Define the JS functions signatures
extern {
    fn addOne(x: u32);
    fn alert(x: u32);
}
// 2\. Call the JS function using unsafe block
#[no_mangle]
pub extern fn add_one(x: u32) {
    unsafe {
        addOne(x);
        alert(x);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

index.html

```
<!DOCTYPE html>
<html>
  <head>
    <script> 
  const addOne = (number) => {
    const text = document.createTextNode(number + 1);
    document.body.appendChild(text);
  }

  const importObject = {
    env: {
        addOne: addOne,
        alert: alert,
    }
  };

  WebAssembly.instantiateStreaming(fetch("utils.gc.wasm"), importObject)
  .then(wasmModule => {
       const result = wasmModule.instance.exports.add_one(5);
   })
    </script>
  <head>
  <body></body>
<html> 
```

Enter fullscreen mode Exit fullscreen mode

关于使用`bindgen`，请看我的另一篇[帖子](https://dev.to/h_ajsf/rust--wasm-using-bindgen-49b4)
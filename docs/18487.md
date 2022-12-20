# 使用单一/通用 RUST 核心代码构建 WASM、Android 和 iOS 应用

> 原文：<https://dev.to/h_ajsf/building-wasm-android-and-ios-app-with-singlecommon-rust-core-code-3ja4>

我将在下面写，使用 RUST 编写简单的`hello world`代码所需的文件，可以由 WASM/Android/iOS 应用程序调用，编译和调用将在后面的文章中进行..请跟我来获取通知..

我测试了`WASM`和`Android`，它们都很好。

src 文件夹树:

```
Hasans-Air:src h_ajsf$ tree .
.
├── lib.rs
├── android
│   └── mod.rs
├── ios
│   └── mod.rs
└── wasm
    └── mod.rs

3 directories, 4 files 
```

Enter fullscreen mode Exit fullscreen mode

现在的文件是:

主:

```
// lib.rs
pub mod wasm;
pub mod ios;
pub mod android;

#[cfg(not(target_arch = "wasm32"))]
use std::os::raw::{c_char};

#[cfg(not(target_arch = "wasm32"))]
use std::ffi::{CStr};

pub fn rust_greetings(to: &str) -> String {
    format!("Hello {}", to)
}

#[cfg(not(target_arch = "wasm32"))]
fn char_str(pattern: *const c_char) -> &'static str {

    let c_str = unsafe { CStr::from_ptr(pattern) };
    let string_ptr = match c_str.to_str() {
        Err(_) => "there",
        Ok(string) => string,
    };
    string_ptr
} 
```

Enter fullscreen mode Exit fullscreen mode

WASM

```
// src/wasm/mod.rs
#[cfg(target_arch = "wasm32")]
pub mod wasm {
    use crate::rust_greetings;
    use wasm_bindgen::prelude::*;

    #[wasm_bindgen]
    pub fn wasm_greetings(to: &str) -> String {
        rust_greetings(to)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

安卓

```
// src/android/mod.rs
#[cfg(target_os="android")]
#[allow(non_snake_case)]
pub mod android {
    use crate::rust_greetings;
    use crate::char_str;
    extern crate jni;

    use self::jni::JNIEnv;
    use self::jni::objects::{JClass, JString};
    use self::jni::sys::{jstring};

    #[no_mangle]
    pub unsafe extern fn Java_com_hasan_RustGreetings_greetings(env: JNIEnv, _: JClass, java_pattern: JString) -> jstring {

        let jvm_input = env.get_string(java_pattern)
                                        .expect("invalid pattern string").as_ptr();

        let input = rust_greetings(char_str(jvm_input));

        let output = env.new_string(input)
                                        .expect("Couldn't create java string!");
        output.into_inner()
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

iOS

```
// src/ios/mod.rs
#[cfg(target_os="ios")]
#[no_mangle]
pub mod ios {
    use crate::char_str;

    use std::ffi::{CString, CStr};
    use std::os::raw::{c_char};
    use crate::rust_greetings;

    #[no_mangle]
    pub extern fn ios_greetings(to: *const c_char) -> *mut c_char {

        let input = rust_greetings(char_str(to));

        CString::new(input).unwrap().into_raw()
    }

    pub extern fn iso_greeting_free(s: *mut c_char) {
        unsafe {
            if s.is_null() { return }
            CString::from_raw(s)
        };
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

汤姆

```
# Cargo.toml
[package]
name = "greetings"
version = "0.1.0"
authors = ["Hasan Yousef"]
edition = "2018"

[dependencies]

[target.'cfg(target_arch="wasm32")'.dependencies]
wasm-bindgen = "0.2.29"

[target.'cfg(target_os="android")'.dependencies]
jni = { version = "0.5", default-features = false }

[lib]
name = "rust_greetings"
crate-type = ["cdylib", "dylib", "staticlib"] 
# Android: dylib
# iOS: cdylib [armv7s-apple-ios] and staticlib [Others]
# WASM: cdylib 
```

Enter fullscreen mode Exit fullscreen mode

下面是管理编译 Android 目标的链接器

货物配置:

```
# .cargo/config [target.aarch64-linux-android]
ar = "NDK/arm64/bin/aarch64-linux-android-ar"
linker = "NDK/arm64/bin/aarch64-linux-android-clang"

[target.armv7-linux-androideabi]
ar = "NDK/arm/bin/arm-linux-androideabi-ar"
linker = "NDK/arm/bin/arm-linux-androideabi-clang"

[target.i686-linux-android]
ar = "NDK/x86/bin/i686-linux-android-ar"
linker = "NDK/x86/bin/i686-linux-android-clang"

[target.x86_64-linux-android]
ar = "NDK/x86/bin/x86_64-linux-android-ar"
linker = "NDK/x86_64/bin/x86_64-linux-android-clang" 
```

Enter fullscreen mode Exit fullscreen mode

下面是运行 WASM，这已经解释过了(这里)[[https://dev.to/h_ajsf/rust-wasm-using-bindgen-49 B4](https://dev.to/h_ajsf/rust--wasm-using-bindgen-49b4)

web pack . config . js

```
// webpack.config.js
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

Enter fullscreen mode Exit fullscreen mode

package.json

```
//  package.json  {  "name":  "utils2",  "version":  "1.0.0",  "description":  "",  "main":  "index.js",  "scripts":  {  "test":  "echo \"Error: no test specified\" && exit 1"  },  "author":  "",  "license":  "ISC",  "devDependencies":  {  "webpack":  "^4.27.1",  "webpack-cli":  "^3.1.2",  "webpack-dev-server":  "^3.1.10"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

index.js

```
import("./pkg/greetings").then(wasmModule => {
  let gr = wasmModule.wasm_greetings("Karam sweet")
  console.log(gr);
}); 
```

Enter fullscreen mode Exit fullscreen mode

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

Enter fullscreen mode Exit fullscreen mode
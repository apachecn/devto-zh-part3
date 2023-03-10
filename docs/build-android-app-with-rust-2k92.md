# 用 rust 构建 Android 应用

> 原文：<https://dev.to/h_ajsf/build-android-app-with-rust-2k92>

1.  安装铁锈
2.  添加$ANDROID_HOME，$NDK_HOME 和$JAVA_HOME

```
Hasans-Air:~ h_ajsf$ sudo code $HOME/.bash_profile
// Write below 
PATH=$PATH:/usr/local/Cellar/flutter/bin
PATH=$PATH:/Users/$USER/.cargo/bin
PATH=$PATH:${ANDROID_HOME}/tools/

export JAVA_HOME=$(/usr/libexec/java_home)
export ANDROID_HOME=/usr/local/share/android-sdk
export NDK_HOME=$ANDROID_HOME/ndk-bundle
// save and Exit
// Confirm changes
Hasans-Air:~ h_ajsf$ source $HOME/.bash_profile 
```

1.  添加 Android 目标:

```
Hasans-Air:ndk h_ajsf$ rustup target add x86_64-linux-android aarch64-linux-android armv7-linux-androideabi i686-linux-android arm-linux-androideabi 
```

对于监督办来说，需要下列目标:

```
Hasans-MacBook-Air:rust_lib_for_android h_ajsf$ rustup target add aarch64-apple-ios armv7-apple-ios armv7s-apple-ios x86_64-apple-ios i386-apple-ios 
```

1.  创建铁锈库

```
Hasans-Air:greetings h_ajsf$ cargo new rust_lib_for_android --lib
     Created binary (application) `cargo` project
Hasans-Air:greetings h_ajsf$ cd rust_lib_for_android 
```

1.  使用 rustup 创建链工具，例如:

```
Hasans-MacBook-Air:~ h_ajsf$ rustup target add x86_64-linux-android
Hasans-MacBook-Air:~ h_ajsf$ rustup toolchain install stable-x86_64-linux-android
Hasans-MacBook-Air:rust_lib_for_android h_ajsf$ rustup override set stable-x86_64-linux-android 
```

参见(此处)[[https://forge.rust-lang.org/platform-support.html](https://forge.rust-lang.org/platform-support.html)了解支持的平台。
或者，使用安卓 NDK，由于不是所有的二级平台都能与`rustup tolchain`兼容，NDK 可以用作:

```
usage: make_standalone_toolchain.py [-h] --arch {arm,arm64,x86,x86_64}
                                    [--api API] [--stl STL] [--force] [-v]
                                    [--package-dir PACKAGE_DIR | --install-dir INSTALL_DIR] 
```

详细命令如下:

```
Hasans-MacBook-Air:rust_lib_for_android h_ajsf$ ${NDK_HOME}/build/tools/make_standalone_toolchain.py --api 28 --arch x86_64 --install-dir NDK/x86_64
Hasans-Air:rust_lib_for_android h_ajsf$ ${NDK_HOME}/build/tools/make_standalone_toolchain.py --api 28 --arch arm64 --install-dir NDK/arm64
Hasans-Air:rust_lib_for_android h_ajsf$ ${NDK_HOME}/build/tools/make_standalone_toolchain.py --api 28 --arch arm --install-dir NDK/arm
Hasans-Air:rust_lib_for_android h_ajsf$ ${NDK_HOME}/build/tools/make_standalone_toolchain.py --api 28 --arch x86 --install-dir NDK/x86 
```

对于 iOS，`cargo-lipo`创建一个通用库，可以安装如下:

```
Hasans-MacBook-Air:rust_lib_for_android h_ajsf$ cargo install cargo-lipo 
```

1.  创建/。货物/配置文件

```
Hasans-Air:rust_lib_for_android h_ajsf$ mkdir .cargo
Hasans-Air:rust_lib_for_android h_ajsf$ cd .cargo
Hasans-Air:.cargo h_ajsf$ touch config 
```

1.  打开你最喜欢的文件夹中的问候文件夹，我用的是 VS 代码

```
Hasans-Air:.cargo h_ajsf$ cd ..
Hasans-Air:rust_lib_for_android h_ajsf$ cd ..
Hasans-Air:documents h_ajsf$ ls rust_lib_for_android
Hasans-Air:documents h_ajsf$ code rust_app 
```

1.  将以下内容添加到/。货物/配置文件

```
[target.x86_64-linux-android]
ar = "NDK/x86/bin/x86_64-linux-android-ar"
linker = "NDK/x86_64/bin/x86_64-linux-android-clang"

[target.aarch64-linux-android]
ar = "NDK/arm64/bin/aarch64-linux-android-ar"
linker = "NDK/arm64/bin/aarch64-linux-android-clang"

[target.armv7-linux-androideabi]
ar = "NDK/arm/bin/arm-linux-androideabi-ar"
linker = "NDK/arm/bin/arm-linux-androideabi-clang"

[target.i686-linux-android]
ar = "NDK/x86/bin/i686-linux-android-ar"
linker = "NDK/x86/bin/i686-linux-android-clang" 
```

1.  将 src/lib.rs 的内容替换为以下内容:

```
use std::os::raw::{c_char};
use std::ffi::{CString, CStr};

#[no_mangle]
pub extern fn rust_greeting(to: *const c_char) -> *mut c_char {
    let c_str = unsafe { CStr::from_ptr(to) };
    let recipient = match c_str.to_str() {
        Err(_) => "there",
        Ok(string) => string,
    };

    CString::new("Hello ".to_owned() + recipient).unwrap().into_raw()
}

/// Expose the JNI interface for android below
#[cfg(target_os="android")]
#[allow(non_snake_case)]
pub mod android {
    extern crate jni;

    use super::*;
    use self::jni::JNIEnv;
    use self::jni::objects::{JClass, JString};
    use self::jni::sys::{jstring};

    #[no_mangle]
    pub unsafe extern fn Java_com_mozilla_greetings_RustGreetings_greeting(env: JNIEnv, _: JClass, java_pattern: JString) -> jstring {
        // Our Java companion code might pass-in "world" as a string, hence the name.
        let world = rust_greeting(env.get_string(java_pattern).expect("invalid pattern string").as_ptr());
        // Retake pointer so that we can use it below and allow memory to be freed when it goes out of scope.
        let world_ptr = CString::from_raw(world);
        let output = env.new_string(world_ptr.to_str().unwrap()).expect("Couldn't create java string!");

        output.into_inner()
    }
} 
```

1.  将以下内容添加到 Cargo.toml

```
[target.'cfg(target_os="android")'.dependencies]
jni = { version = "0.5", default-features = false }

[lib]
name = "greetings"
crate-type = ["dylib"] 
```

1.  为每个目标构建静态库

```
Hasans-MacBook-Air:rust_lib_for_android h_ajsf$ cargo build --target x86_64-linux-android --release
Hasans-MacBook-Air:rust_lib_for_android h_ajsf$ cargo build --target aarch64-linux-android --release
Hasans-MacBook-Air:rust_lib_for_android h_ajsf$ cargo build --target armv7-linux-androideabi --release
Hasans-MacBook-Air:rust_lib_for_android h_ajsf$ cargo build --target i686-linux-android --release 
```

注`aarch64`可用于`arm64`和`arm64-v8a`

对于 iOS，运行`cargo lipo --release`

```
Hasans-MacBook-Air:rust_lib_for_android h_ajsf$ cargo lipo --release
Due to a known rustc issue, cargo-lipo can only be run on macOS. See https://github.com/rust-lang/rust/issues/36156#issuecomment-373201676 for more info.
   Compiling cargo v0.1.0 (/Users/h_ajsf/Documents/greetings/rust_lib_for_android)
warning: dropping unsupported crate type `dylib` for target `aarch64-apple-ios`                                                                                    

warning: dropping unsupported crate type `cdylib` for target `aarch64-apple-ios`                                                                                   

    Finished release [optimized] target(s) in 5.90s                                                                                                                
   Compiling cargo v0.1.0 (/Users/h_ajsf/Documents/greetings/rust_lib_for_android)
warning: dropping unsupported crate type `dylib` for target `armv7-apple-ios`                                                                                      

warning: dropping unsupported crate type `cdylib` for target `armv7-apple-ios`                                                                                     

    Finished release [optimized] target(s) in 0.98s                                                                                                                
   Compiling cargo v0.1.0 (/Users/h_ajsf/Documents/greetings/rust_lib_for_android)
warning: dropping unsupported crate type `dylib` for target `i386-apple-ios`                                                                                       

warning: dropping unsupported crate type `cdylib` for target `i386-apple-ios`                                                                                      

    Finished release [optimized] target(s) in 1.56s                                                                                                                
   Compiling cargo v0.1.0 (/Users/h_ajsf/Documents/greetings/rust_lib_for_android)
warning: dropping unsupported crate type `dylib` for target `x86_64-apple-ios`                                                                                     

warning: dropping unsupported crate type `cdylib` for target `x86_64-apple-ios`                                                                                    

    Finished release [optimized] target(s) in 0.88s 
```

而通用 iOS 库可以在`cargo/target/universal/release/libgreetings.a`中找到

1.  对于 iOS:
2.  添加`greetings.h`文件，由:`File\Add files to "Greetings"...`
3.  由`General -> Linked Frameworks and Libraries`添加原生文件`libgreetings.a`和原生交互框架`libresolv.tbd`
4.  通过`File\New\File.... Header File`创建桥接头`Greetings-Bridging-Header.h`，并将`greetings.h`文件导入其中，文件如下:

```
#ifndef Greetings_Bridging_Header_h
#define Greetings_Bridging_Header_h

#import "greetings.h"

#endif 
```

*   通过添加`Greetings-Bridging-Header.h`文件的路径来更新`Build Settings -> Objective-C Bridging Header`
*   通过添加导入的`libgreetings.a`文件的路径来更新`Build Settings -> Library Search Paths`
*   通过`File\New\File...`和`iOS\Source\Swift File`创建`RustGreetings` swift 文件，并在其中添加:

```
class RustGreetings {
    func sayHello(to: String) -> String {
        let result = rust_greeting(to)
        let swift_result = String(cString: result!)
        rust_greeting_free(UnsafeMutablePointer(mutating: result))
        return swift_result
    }
} 
```

*   通过添加以下内容来更新`ViewController.swift`文件中的`viewDidLoad`:

```
let rustGreetings = RustGreetings()
print("\(rustGreetings.sayHello(to: "world"))") 
```
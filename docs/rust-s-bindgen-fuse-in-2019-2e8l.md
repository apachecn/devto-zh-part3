# 2019 年 Rust 的 Bindgen + Fuse

> 原文：<https://dev.to/kdrakon/rust-s-bindgen-fuse-in-2019-2e8l>

我将快速展示我是如何让 bindgen([https://rust-lang.github.io/rust-bindgen](https://rust-lang.github.io/rust-bindgen))生成绑定到 Fuse (libfuse)与当前稳定的<sup id="fnref1">[1](#fn1)</sup>Rust 的释放。通过这样做，这个*应该*演示如何在 Rust 中引导编写你自己的 Fuse 文件系统。

我确实意识到已经有一些板条箱在 Rust 中帮助制作熔丝驱动程序，但这或多或少是一个也尝试 bindgen 的借口，我不相信那些现有的库会使用它。

我将使用:

*   锈货 1.33 <sup id="fnref1">[1](#fn1)</sup>
*   bindgen 0.48.1
*   libfuse 源头来自[https://github.com/libfuse/libfuse](https://github.com/libfuse/libfuse)
*   Fuse 动态库的 OSX 安装(通过[https://osxfuse.github.io/](https://osxfuse.github.io/))，但是你应该也能使用 Linux 的库(例如来自 Debian/Ubuntu 的`libfuse-dev`)。

假设你已经有了`cargo`，你可以用`cargo install bindgen`得到 bindgen。

## 步骤

### 新建项目

1.  初始化一个项目，比如`cargo init rust-bindgen-fuse`。
2.  我们需要 libfuse 头文件。与其手动下载或者通过包管理器(例如 Debian/Ubuntu 的`libfuse-dev`)引用它们，我会直接从 Github 下载。在项目中，您可以创建一个 git 子模块，例如`git submodule git@github.com:libfuse/libfuse.git`。

### 生成绑定

为了使用 bindgen 并生成我们想要的代码，我们创建了一个“包装器”C 头文件来封装我们想要的头文件，包括所有可传递的依赖头文件。例如，这是我在`src/fuse_wrapper.h` :
中的内容

```
#define FUSE_USE_VERSION 31 
#include "../libfuse/include/fuse.h" 
```

`FUSE_USE_VERSION`是另一个头文件(我相信是`fuse_common.h`)为了 API 兼容性需要定义的宏常量。`fuse.h`包含了一个实现者需要使用的所有高级 API，所以我从这里开始。

要测试 bindgen out，您可以运行以下命令:

```
bindgen --distrust-clang-mangling src/fuse_wrapper.h 
```

你应该会看到一些铁锈代码飞过。这些是我们稍后将链接到的 libfuse 库的绑定。我使用`--distrust-clang-mangling`的原因是因为 bindgen 生成了一些链接器名称；由于某种原因，链接器无法正确引用生成的名称，但这个标志解决了这个问题。

对于这个项目，我们希望生成的绑定是动态的，所以它们是为当前使用的 Rust 工具链而构建的。为了做到这一点，我们使用了一个货物构建脚本——即项目根目录中一个名为`build.rs`的文件。首先，将 bindgen 添加到您的`Cargo.toml`文件:

```
[build-dependencies]
bindgen = "0.48.1" 
```

**注意**这是一个构建依赖，而不是代码依赖。这允许在构建脚本中使用它。至于脚本，这是我根据在线文档使用的。

```
extern crate bindgen;

use std::env;
use std::path::PathBuf;

fn main() {
    println!("cargo:rustc-link-search=/usr/local/lib");
    println!("cargo:rustc-link-lib=osxfuse");

    let bindings = bindgen::Builder::default()
        .header("src/fuse_wrapper.h")
        .trust_clang_mangling(false) // disabled due to linker name problem
        .generate()
        .expect("Unable to generate bindings");

    // Write the bindings to the $OUT_DIR/bindings.rs file.
    let out_path = PathBuf::from(env::var("OUT_DIR").unwrap());
    bindings
        .write_to_file(out_path.join("bindings.rs"))
        .expect("Couldn't write bindings!");
} 
```

默认情况下，`cargo`会执行`build.rs`。注意，现在，我已经将它硬编码到我当前的本地环境中，这是一台 OSX 机器。`println!`正在向`rustc`发送命令来链接我的本地动态库。在 Ubuntu 上，我在通过`apt`安装了`libfuse-dev`之后，用动态库`fuse`测试了这个。我想根据一些环境变量等让这个脚本变得更智能是微不足道的。这段代码的其余部分以编程方式调用 bindgen，并将 Rust 代码输出到一个文件中，该文件可以在您自己的代码中引用。

### 把所有的东西放在一起

最后，使用生成的代码，我可以调用熔丝绑定。Fuse 的一般工作方式是提供一个名为`fuse_operations`的 C struct 实现。这包含了从 Fuse 内核模块回调的引用函数。换句话说，你把你的文件系统应该表现的行为直接注入到一个*运行时*，如果你愿意这么称呼它的话。

通常，在提供您的操作之后，您的用户空间应用程序结束，Fuse 的本地安装从那里接管。基本上，它接受您注入的代码，挂载一个文件系统，然后您的应用程序退出。下面的代码——在`src/main.rs`中——展示了我第一次调用 Fuse 的初步尝试。

```
#![allow(non_upper_case_globals)]
#![allow(non_camel_case_types)]
#![allow(non_snake_case)]
include!(concat!(env!("OUT_DIR"), "/bindings.rs"));

use std::env::args;
use std::env::args_os;
use std::ffi::CString;
use std::mem::size_of;
use std::os::unix::ffi::OsStrExt;
use std::os::raw::c_int;

fn main() {
    let ops: fuse_operations = fuse_operations {
        getattr: None,
        readlink: None,
        mknod: None,
        mkdir: None,
        unlink: None,
        rmdir: None,
        symlink: None,
        rename: None,
        link: None,
        chmod: None,
        chown: None,
        truncate: None,
        open: None,
        read: None,
        write: None,
        statfs: None,
        flush: None,
        release: None,
        fsync: None,
        setxattr: None,
        getxattr: None,
        listxattr: None,
        removexattr: None,
        opendir: None,
        readdir: None,
        releasedir: None,
        fsyncdir: None,
        init: None,
        destroy: None,
        access: None,
        create: None,
        lock: None,
        utimens: None,
        bmap: None,
        ioctl: None,
        poll: None,
        write_buf: None,
        read_buf: None,
        flock: None,
        fallocate: None,
        copy_file_range: None,
    };

    let argc: i32 = args_os().len() as i32;
    let args: Vec<CString> = args_os()
        .into_iter()
        .map(|arg| {
            arg.to_str()
                .and_then(|s| {
                    CString::new(s)
                        .map(|c_string| {
                            dbg!(&c_string);
                            c_string
                        })
                        .ok()
                })
                .expect("Expected valid arg input")
        })
        .collect();

    let mut argv: Vec<*const ::std::os::raw::c_char> =
        args.iter().map(|arg| arg.as_ptr()).collect();

    unsafe {
        fuse_main_real(
            argc,
            argv.as_mut_ptr() as *mut *mut ::std::os::raw::c_char,
            &ops,
            size_of::<fuse_operations>(),
            std::ptr::null_mut(),
        );
    }
} 
```

正如你所看到的，我的文件系统不需要 *bupkis* 。我差不多做四件事:

1.  创建我的`fuse_operations`什么都不做
2.  从命令行(`argc`)计算参数的数量
3.  将所有 UTF-8 参数转换成 C 字符串(`argv`)
4.  将所有这些东西传递到`fuse_main_real`，这是调用 Fuse 库的入口点，以便在其他东西之间挂载一个路径。

注意所有的*老派* C 指针；bindgen 可以将很多东西翻译成 *nice* Rust(例如`fuse_operations` struct 中的`Option`s)，但是你仍然需要做很多类似 C 的东西，比如提供指向字符串指针的指针。

最开始可以看到一个宏，`include!`；也就是拉入所有生成的绑定。不幸的是，我的 IDE 不知道如何处理这个问题，所以我在查看生成的代码时没有得到多少帮助。作为一个恶作剧，我临时做了如下:

```
bindgen --distrust-clang-mangling src/fuse_wrapper.h > src/delete/mod.rs 
```

然后，我把`main.rs`修改成这样:

```
// include!(concat!(env!("OUT_DIR"), "/bindings.rs"));
mod delete;
use delete::*; 
```

这样，我就可以使用我的 IDE 查看生成的代码**和**引用结构和方法。取消对`include!`的注释并注释`delete`模块允许我验证我调用了正确的东西。

### 试一试

跑步:

```
cargo run -- --help 
```

应该给你保险丝输出的使用信息:

```
usage: target/debug/rust-bindgen-fuse mountpoint [options]

general options:
    -o opt,[opt...]        mount options
    -h   --help            print help
    -V   --version         print version

FUSE options:
    -d   -o debug          enable debug output (implies -f)
    -f                     foreground operation
    -s                     disable multi-threaded operation

fuse: no mount point 
```

您应该能够做到这一点:

```
cargo run -- /tmp/mount 
```

如果你运行`mount`，你应该会看到这样一个条目:

```
rust-bindgen-fuse@osxfuse0 on /private/tmp/mount (osxfuse, nodev, nosuid, synchronous, mounted by kdrakon) 
```

显然，由于我们没有实现任何东西，您将在运行`ls /tmp/mount` :
后看到这个

```
ls: /tmp/mount: Function not implemented 
```

那么我们如何知道什么没有实现呢？幸运的是，Fuse 有一个调试模式:

```
cargo run -- /tmp/mount -d 
```

这将吐出对您的`fuse_operations`的所有回调，特别是那些失败/丢失的回调。

### 下一个？

我将解密更多的熔丝操作，以及对它们的期望。玩得开心！

## 参考文献

以下是我找到的一些有用的链接，可以帮助我克服路上遇到的一些困难。

*   [https://rust-lang.github.io/rust-bindgen](https://rust-lang.github.io/rust-bindgen)
*   [https://www.cs.nmsu.edu/~pfeiffer/fuse-tutorial/](https://www.cs.nmsu.edu/%7Epfeiffer/fuse-tutorial/)
*   [https://github . com/libfuse/libfuse/blob/master/example/hello . c](https://github.com/libfuse/libfuse/blob/master/example/hello.c)
*   [https://codeseekah.com/2015/01/25/rusts-osargs-to-cs-argv/](https://codeseekah.com/2015/01/25/rusts-osargs-to-cs-argv/)

*Roberto Bicchierai 的作品《Rust》获得 CC BY-NC 4.0 的许可*

* * *

1.  *最初作为[要点](https://gist.github.com/kdrakon/0152631129329ab5eee4fee2b6a90a2d)发布于 2019 年 3 月 13 日* [↩](#fnref1)
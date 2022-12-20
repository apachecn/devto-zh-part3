# 货物构建脚本

> 原文：<https://dev.to/x1957/cargo-build-script-19nd>

I tried tower-grpc, but I don't know a lot about it as Mengxin. I used to use this one to generate code. After reading the example, I didn't find any process of generating code. Look carefully at

```
pub mod hello_world {
    include!(concat!(env!("OUT_DIR"), "/helloworld.rs"));
}

use hello_world::{server, HelloReply, HelloRequest}; 
```

Use include in hello_world mod! Get the generated code in.

Ok, let's continue to struggle. Where is the generated code? How was it generated?

I found a build.rs Mengxin in the directory. Although I didn't know this, I knew it was related to compilation by looking at his name, so I Google
[https://doc.rust-lang.org/cargo/reference/build-scripts.html](https://doc.rust-lang.org/cargo/reference/build-scripts.html)

Actually, it is something that is executed during build, and you can also specify build-dependencies and so on. Looking at it simply, here is the compilation of. proto, and then the output to OUT_DIR, so the above include! It will work.
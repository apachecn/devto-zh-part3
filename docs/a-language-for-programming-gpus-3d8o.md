# 一种为 GPU 编程的语言

> 原文：<https://dev.to/calebwin/a-language-for-programming-gpus-3d8o>

大多数语言都是为 CPU(代表中央处理器)编程的。例如，如果你用 Python 或 Rust 编写一个程序，并运行它——它就会在你的计算机的 CPU 上运行。但是 CPU 并不是你电脑中唯一可以进行计算的部分。你的电脑可能也配有图形处理器。GPU 代表图形处理单元，虽然 GPU 通常用于处理图形，但它们通常最擅长处理大量数字。如果您有一个包含一百万个数字的列表，并希望将它们缩放 2 倍。你的程序在 GPU 上运行可能比在 CPU 上运行更快。

但是如果像 Python 或者 Rust 这样的语言一般不在 GPU 上运行，那什么语言会呢？有好几个。OpenCL、CUDA 和 Halide 都是用于编写可以在 GPU 上运行的程序的语言。但所有这些首先是为 C 或 C++设计的。此外，语法可能有点混乱...

```
static const char *kernelSource =
"#pragma OPENCL EXTENSION cl_khr_fp64 : enable \n" \
"__kernel void mult(__global double *v) { \n" \
"    int id, v_re, v_im; \n" \
"    id   = get_global_id(0); \n" \
"    v_re = 2*id; \n" \
"    v_im = v_re + 1; \n" \
" \n" \
"    v[v_re] = 2*v[v_re]; \n" \
"    v[v_im] = 4*v[v_im]; \n" \
"} \n" \
"\n" ; 
```

必须有一个更好的编程 GPU 的方法。嗯，这是过去几个月来我一直兴奋地在做的一件小事。这是一种叫做 Emu 的微型编程语言...

```
emu! {
    // multiplies 2 matrices
    // n is the dimension of the matrices
    // a and b are the matrices to be multiplied, c is the result
    multiply_matrices(n i32, global_a [f32], global_b [f32], global_c [f32]) {
        // indices of cells to multiply
        let i: i32 = get_global_id(0);
        let j: i32 = get_global_id(1);

        // execute step of multiplication
        for k in 0..n {
            global_c[i * n + j] += global_a[i * n + k] * global_b[k * n + j];
        }
    }
} 
```

有几件事你可能马上就注意到了——(1)它是通过过程宏而不是 C 或 C++在 Rust 中使用的，(2)它的语法是 Rust 式的，带有 Go 式的参数声明。但是等等，还有更多！

您可以用单位来注释数字。使用下面的语法，单位的前缀指定应该缩放多少不同的数字，并且所有内容都自动标准化为基本 SI 单位。这里有一个例子...

```
emu! {
    move_particles(global_particles_x [f32], global_particles_y [f32], global_particles_z [f32]) {
        global_particles_x[get_global_id(0)] += 1 as cm;
        global_particles_y[get_global_id(0)] += 1 as cm;
        global_particles_z[get_global_id(0)] += 8.92 as nm;
    }
} 
```

您也可以使用某些内置的特殊号码。这些特殊的数字包括一些我最喜欢的数学常数和物理常数。你甚至可以用 [`PAU`](https://xkcd.com/1292/) ！

```
emu! {
    add_particles(num_particles u32, num_moles u32) u32 {
        return num_particles + num_moles * L;
    }
} 
```

真的希望这个小编程语言能帮助你从 Rust 更轻松的编程 GPU。请检查一下这个资源库——它在**[https://github.com/calebwin/emu](https://github.com/calebwin/emu)**(那里甚至有一本小书！)如果您有任何意见、想法或建设性的批评，请告诉我。🙌

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [卡莱温](https://github.com/calebwin) / [动车组](https://github.com/calebwin/emu)

### Emu 是一个面向底层、跨平台 GPGPU 的现代库，支持基于设备、可再现、注重隐私的计算

<article class="markdown-body entry-content container-lg" itemprop="text">

> 旧版 Emu(用了宏之类的东西)这里是[这里是](https://github.com/calebwin/emu/tree/master/em)。

[![Discord Chat](img/1712d473464f8042736da86e26cf0aac.png)](https://discord.gg/sKf6KCs)[![crates.io](img/b2f376b9532d92e255e13d73be99cac1.png)](https://www.crates.io/crates/emu_core)[![docs.rs](img/0c9629dfb3563fc9f245b3318ae3bf7d.png)](https://calebwin.github.io/emu/)

[![](img/91f9286132a3b154c17cfce3de49e580.png)](https://camo.githubusercontent.com/c6d68e1aaaa2eaa53f6cf7de99dcd27b5cf3bc47/68747470733a2f2f692e696d6775722e636f6d2f435a456b644b312e706e67)

Emu 是一个 GPGPU 库，专注于可移植性、模块化和性能。

它是对 WebGPU 的一种 CUDA 式的特定于计算的抽象，提供特定的功能，使 WebGPU 感觉更像 CUDA。这里是亮点功能的快速运行...

*   **Emu 可以在任何地方运行** - Emu 使用 WebGPU 支持 DirectX、Metal、Vulkan(最终还有 OpenGL 和 browser)作为编译目标。这使得 Emu 可以在几乎任何用户界面上运行，包括桌面、移动和浏览器。通过将繁重的计算转移到用户的设备上，您可以减少系统延迟并提高隐私性。

*   Emu 让计算更简单 - Emu 让 WebGPU 感觉像 CUDA。它通过提供以下功能来实现这一点...

    *   作为驻留在 GPU 上的数据的包装器(从而确保类型安全的数据移动)
    *   `DevicePool`作为无配置自动管理的设备池(类似于 CUDA)
    *   `trait Cache` -…

</article>

[View on GitHub](https://github.com/calebwin/emu)

您可以通过添加`[dependencies] em = "0.1.2"`到您的`Cargo.toml`来尝试一下，或者您可以稍后启动存储库。
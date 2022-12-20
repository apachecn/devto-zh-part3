# 咖啡——一个固执己见的 Rust 2D 游戏引擎

> 原文：<https://dev.to/hecrj/coffee-an-opinionated-2d-game-engine-for-rust-3bbl>

Coffee 是 Rust 的一个固执己见的 2D 游戏引擎，专注于简单、明确和类型安全。

咖啡正处于非常早期的发展阶段。【2019 年期间
计划积极开发(希望在此之后！).很多[基本特性还是
缺失](https://github.com/hecrj/coffee/issues?q=is%3Aissue+is%3Aopen+label%3Afeature)，一些[依赖是实验性的](https://github.com/hecrj/coffee#implementation-details)，大概还有*很多*
bug。[欢迎投稿！](https://github.com/hecrj/coffee#contributing--feedback)

## 特性

*   声明式、类型安全的资产加载
*   加载带有进度跟踪的屏幕
*   带有性能指标的内置[调试视图](https://github.com/hecrj/coffee/blob/mastimg/debug.png)
*   固定时间步长
*   显式、易用、硬件加速的 2D 图形 API
*   利用 OpenGL、Vulkan、Metal、D3D11 和 D3D12 的多平台支持
*   纹理数组支持
*   显式和高效的批量绘制
*   屏幕外渲染
*   TrueType 字体渲染

## 概述

下面是一个打开窗口的最小的例子:

```
use coffee::{Game, Result, Timer};
use coffee::graphics::{Color, Window, WindowSettings};

fn main() -> Result<()> {
    MyGame::run(WindowSettings {
        title: String::from("A caffeinated game"),
        size: (1280, 1024),
        resizable: true,
    })
}

struct MyGame {
    // Your game state goes here...
}

impl Game for MyGame {
    type View = (); // No view data.
    type Input = (); // No input data.

    const TICKS_PER_SECOND: u16 = 60; // Update rate

    fn new(_window: &mut Window) -> Result<(MyGame, Self::View, Self::Input)> {
        // Load your game assets here. Check out the `load` module!
        Ok((MyGame { /* ... */ }, (), ()))
    }

    fn update(&mut self, _view: &Self::View, _window: &Window) {
        // Update your game here
    }

    fn draw(&self, _view: &mut Self::View, window: &mut Window, _timer: &Timer) {
        // Clear the current frame
        let mut frame = window.frame();
        frame.clear(Color::BLACK);

        // Draw your game here. Check out the `graphics` module!
    }
} 
```

## 储存库

如果您想了解更多，请查看 GitHub 上的资源库！

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [赫克日](https://github.com/hecrj) / [咖啡](https://github.com/hecrj/coffee)

### 一个固执己见的 Rust 2D 游戏引擎

<article class="markdown-body entry-content container-lg" itemprop="text">

# 咖啡

[![Integration status](img/a004d8777279a671411234bbc87d5b06.png)](https://github.com/hecrj/coffee/actions)[![Documentation](img/d825bc304c19caa40a546042061d7a9d.png)](https://docs.rs/coffee)[![Crates.io](img/7a960c2675c2df072a8db322a41f2cbc.png)](https://crates.io/crates/coffee)[![License](img/9856484e6ae4cb50f6d9dd570c72e743.png)](https://github.com/hecrj/coffee/blob/master/LICENSE)[![Gitter chat](img/e1804d2bcbb3b6b3d8eb180c032e1c96.png)](https://gitter.im/hecrj/coffee)

一个固执己见的 Rust 游戏引擎专注于简单、明确和类型安全。

咖啡正处于非常早期的发展阶段。许多[的基本特性仍然缺失](https://github.com/hecrj/coffee/issues?q=is%3Aissue+is%3Aopen+label%3Afeature)，一些[的依赖项是实验性的](https://raw.githubusercontent.com/hecrj/coffee/master/#implementation-details)，可能还有*许多*的 bug。[随意投稿！](https://raw.githubusercontent.com/hecrj/coffee/master/#contributing--feedback)

## 特征

*   [反应灵敏、可定制的图形用户界面](https://gfycat.com/gloomyweakhammerheadshark)
*   带有进度跟踪的声明式类型安全加载屏幕
*   带有性能指标的内置[调试视图](https://github.com/hecrj/coffee/blob/mastimg/debug.png)
*   固定的、确定的时间步长
*   显式、易用、硬件加速的 2D 图形 API
*   利用 OpenGL、Vulkan、Metal、D3D11 和 D3D12 的多平台支持
*   [显式高效批量抽奖](https://gfycat.com/beautifulseparatebeetle)
*   [网状支架](https://gfycat.com/academicglossykingfisher)
*   纹理数组支持
*   屏幕外渲染
*   TrueType 字体渲染
*   游戏手柄支持

还有更多！查看[示例](https://github.com/hecrj/coffee/tree/master/examples)以查看它们的运行情况。

## 使用

在您的`Cargo.toml`中添加`coffee`作为依赖项，并启用图形后端功能(`opengl`、`vulkan`、`metal`、`dx11`或`dx12`):

```
coffee = { version = "0.4", features = ["
```

…</article>

[View on GitHub](https://github.com/hecrj/coffee)
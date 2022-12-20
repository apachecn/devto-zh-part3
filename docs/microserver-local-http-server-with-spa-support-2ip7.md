# 微服务器:支持 SPA 的本地 http 服务器

> 原文：<https://dev.to/robertohuertasm/microserver-local-http-server-with-spa-support-2ip7>

前几天，在开发一个`Angular`应用程序时，我需要为这个应用程序的`AoT compiled`版本提供服务，我使用了 [http-server npm 包](https://www.npmjs.com/package/http-server)。令我惊讶的是，它不支持`SPAs`(单页应用)。

就在那时，我决定可以使用一些 Rust 来构建一个工具，模拟 T2 http-server NPM 包 T3 的功能。

## 翘曲救援

我要做的第一件事是决定我将使用哪个 web 服务器框架作为基础，我选择了 [Warp](https://github.com/seanmonstar/warp) ,因为它友好的 API 和它令人惊讶和难以置信的性能。

然后只需要把几行 [Rust](https://www.rust-lang.org/) 放在一起，让它工作就行了！😁

## 发布微服务器

一旦一切就绪，我就准备出版我的第一本书了！🚀

不得不说，发布一个`crate`的体验甚至比发布一个`npm package`还要轻松。而在[几个命令](https://doc.rust-lang.org/cargo/reference/publishing.html)之后，[微服务器](https://crates.io/crates/microserver)就准备被任何人消费掉了！😊

## 如何使用

`crate`的用法非常简单，所以如果你想知道更多，只需浏览 [crates.io](https://crates.io/crates/microserver) 或该项目的 [Github repo](https://github.com/robertohuertasm/microserver) 。

-
最初发布于 2018 年 11 月 1 日[robertohuertas.com](https://robertohuertas.com/2018/11/01/microserver)。
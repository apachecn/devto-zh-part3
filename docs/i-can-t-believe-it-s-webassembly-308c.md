# 居然是 WebAssembly！！！！

> 原文：<https://dev.to/jwulf/i-can-t-believe-it-s-webassembly-308c>

当然，你今天在黑客新闻上看到了 [WebAssembly Package Manager，但是你可能错过了它惊天动地的历史意义。](https://news.ycombinator.com/item?id=19732794)

针对 WASM 的 Rust 应用程序的“编写一次，在任何地方运行”现在是 T2 的事情了。

但是你能用它做什么呢？“我听到你问了。

当然，我可以用它来启动 nginx 服务器，但我可以用 Docker 来完成。

这个世界刚刚改变了，因为有了 lolcat WebAssembly 包，你可以这样做:

[![](img/4f28f13289bfec2194594a4fe2332d60.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--upQ5jU2u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s6bn95vhjolcas2ay5qj.png)

仔细听着，年轻的学徒，我会告诉你如何做到这一点。

使用 [Wasmer](https://wasmer.io/) 你可以运行通用 WebAssembly 二进制文件。使用 [WebAssembly 包管理器](https://wapm.io/help/guide)，你可以从一个中央存储库安装 WebAssembly 二进制文件，类似于 JavaScript 的 NPM。

在为数不多的可用 WebAssembly 包中，现在*是[lol cat](https://wapm.io/package/lolcat)——它给了你无聊的文本彩虹和独角兽！*

 *关于 WebAssembly 包管理器中的`wapm`命令，有一点需要了解:它目前没有像 npm 那样的`-g`标志。这意味着所有 wasm 包的安装都是本地的。

有一个针对 it 的[开放特性请求(可以随意投票赞成)，但在此期间你可以解决它。](https://github.com/wasmerio/wapm-cli/issues/60)

你应该这样做:

1.  安装 [wasmer](https://wasmer.io/) 运行时(包括`wapm`包管理器 CLI)。
2.  做一个目录`~/.wapm`。
3.  将以下内容添加到您的`~/.bashrc`(或者对于 zsh 为`~/.zshrc`)文件中:

```
function wapmg() {
    pushd ~/.wapm
    wapm install $1
    popd
}

function lolcat() {
    cat $1 | wasmer run ~/.wapm/wapm_packages/_/lolcat@0.1.2/lolcat.wasm
} 
```

Enter fullscreen mode Exit fullscreen mode

1.  运行`source ~/.bashrc`(或对于 zsh 运行`source ~/.zshrc`)
2.  运行`wapmg install lolcat`

现在，您可以使用`lolcat`来代替无聊的旧`cat`命令，并通过 WebAssembly 在您的终端中获得彩虹和独角兽。

欢迎来到未来！*
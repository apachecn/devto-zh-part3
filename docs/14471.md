# 浏览目录的新方法

> 原文：<https://dev.to/dystroy/broot-a-new-way-to-browse-directories-779>

我一直喜欢从树状视图中看到的概览。但是`tree`几乎从不可用，因为它通常会生成数页(或数百页)的输出。

也不能交互式搜索，当然也不能充当启动器。

`tree`是反思的起点之一。`fzf`又是一个，我再也离不开模糊搜索了。

因此，我做了一个我一直在使用的[brout](https://github.com/Canop/broot)，它让我只需敲几下键盘就能进入任何目录或文件，比其他任何目录或文件都要快。

[![shortcut](img/3ae00d9726f2e5ab0969c33c70d70578.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ECvk8T2M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nkbjz085ov9drby0pic5.png)

现在，它已经为大众消费做好了准备，但我仍然渴望得到反馈。

**brout**可以通过命令启动，这允许非交互式快捷方式，例如:

[![dcd rulset](img/6c69be7185297f2da2878193ed815c07.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PDDBdrzD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4p9g423vr5q5012ag6dl.png)

如果有 rust 专家在场，我很乐意讨论代码，尤其是任何微优化的想法(开发这样的程序完全是为了速度)。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[【CANopen】](https://github.com/Canop)/[【brookt】](https://github.com/Canop/broot)

### 查看和导航目录树的新方法:https://dystroy.org/broot

<article class="markdown-body entry-content container-lg" itemprop="text">

# 根

[![CI](img/4b79137b8a23956292697579349b6aa8.png)](https://travis-ci.org/Canop/broot)[![MIT](img/a5ee68a95d8f457ed498c54780ce4e61.png)](https://raw.githubusercontent.com/Canop/broot/master/LICENSE)[![Latest Version](img/02e47fcb8b8f51bfbe5c6a110e02e1a0.png)](https://crates.io/crates/broot)[![Chat on Miaou](img/de82e79638bacef20e15596cd980a933.png)T11】](https://miaou.dystroy.org/3490?broot)

导航目录的更好方法

[安装螺钉](https://dystroy.org/broot/install/)

# 获得一个目录的概览，即使是一个大目录

`br -s`

[![overview](img/31cb9642152bc809afb84ce3b74c7b04.png)](https://raw.githubusercontent.com/Canop/broot/master/website/docs/img/20200629-overview.png)

注意到未列出的了吗？

这使得它在旧的`tree`命令会产生多页输出的情况下也是可用的。

对文件进行适当的处理，将不需要的文件放在一边(不过你可以忽略它们，参见[文档](https://raw.githubusercontent.com/Canop/broot/master/../navigation/#toggles))。

# 预览文件

选择文件并出现预览面板时，点击`ctrl` `→`。

[![preview](img/8e2e2d857e1d0aef5e1317be4ec19518.png)](https://raw.githubusercontent.com/Canop/broot/master/website/docs/img/20200716-preview.png)

预览面板与树面板中的选择保持同步。

# 找到一个目录然后`cd`到它

打几封信

[![cd](img/f2dcf7b3f6c18deab8d1dc0d58745e4f.png)](https://raw.githubusercontent.com/Canop/broot/master/website/docs/img/20191112-cd.png)

点击`alt` `enter`就大功告成了。

这样，即使您不记得目录在哪里，也可以用最少的击键次数导航到该目录。

broot 很快，不会阻塞(任何击键都会中断当前搜索以开始下一个搜索)。

对此最有用的键:

*   …

</article>

[View on GitHub](https://github.com/Canop/broot)
# 脸书撒谎:一部脸书电影扼杀了另一部电影

> 原文：<https://dev.to/bauripalash/a-facebook-feature-which-kills-another-feature-3nlg>

脸书、脸书、脸书....对脸书来说，2018 年并不算是快乐的一年..虽然我不在这里谈论这些。

今天我将谈论一个完全扼杀了另一个功能的 facebook 功能。所以不多说了，让我们直接进入主要内容..

我会尽量简短...

早在 2017 年，脸书就为印度用户推出了**个人资料图片保护**，以保护他们的照片不被滥用。
[【来源】](https://www.google.com/amp/s/www.theverge.com/platform/amp/2017/6/22/15851662/facebook-profile-picture-protection-india)

当配置文件图片保护打开时，其他用户将无法从该配置文件下载图像，并且在配置文件图片上显示蓝色边框:

[![](img/907cec042a1bff104899c0a2687eeb01.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iWKVtk2r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qdje9p4mrn7el0ha4jku.png)

在底部你看不到任何**全尺寸视图**选项

[![No Download Options](img/7c89f85a990bbbf6f6f050cff05dab38.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IyjddtOr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nt3ga7l8swrpdtoa5v55.png)

等待...！！我忘了一件事，按照说法，脸书应该阻止用户截图，但功能在哪里？😑

现在，暂且不说这个，已经有一个脸书的功能，我们可以用它来绕过所谓的**个人资料图片保护**下载任何人的个人资料图片

那么，让我们来看看如何在打开个人资料图片保护的情况下下载个人资料图片。

我们首先需要的是受害者的，我的意思是目标的**数字个人资料 ID** (或者不管它叫什么)
如果个人资料 ID 不可见并且有用户名如 *bauripalash* 、 *abcd* 等，我们可以提取目标的**数字个人资料 ID** 和**[https://findmyfbid.in/](https://findmyfbid.in/)**..

现在访问
`https://graph.facebook.com/USERNAME/picture?width=800`，用目标的**数字档案 ID** 替换**用户名**

**现在您会看到目标用户的个人资料图片将会显示出来，并且也可以下载**

**现在！我的问题是，脸书在愚弄我们印度人吗？😡**

我发现这个的那天，我向脸书·怀特哈特项目报告了。起初我想，一定有一些认证或 api 密钥系统，也许它不知何故坏了！几天后，我收到了一位员工的回复，他总结道

> 感谢您的报告，但我们不认为从网上捕捉公共图像有资格获得我们项目的奖金。
> ...
> 请务必记住，个人资料图片始终是公开的。你提到的功能是一个试点测试，看看这些工具如何帮助人们更好地控制其他人如何参与他们在脸书的个人资料图片。
> ...

(解释或更正刚说过的话)我是说😑任何人都可以下载所谓的受保护的个人资料图片。那侧影护图有什么用？只是一个花哨的蓝色边框！😓

**免责声明:我、Palash Bauri 或 Dev.to 对使用此处提及的方法造成的任何损害概不负责。本文仅用于教育和宣传目的**

* * *

如果你喜欢我的作品(我的文章、故事、软件、研究等等),考虑给我买一杯☕咖啡🤗
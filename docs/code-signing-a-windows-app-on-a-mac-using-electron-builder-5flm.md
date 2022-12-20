# 使用电子生成器在 Mac 上对 Windows 应用程序进行代码签名

> 原文：<https://dev.to/eheiberg/code-signing-a-windows-app-on-a-mac-using-electron-builder-5flm>

tldr 跳至末尾查看分步说明。

* * *

我用 Electron(和 Vue)制作了我的第一个桌面应用程序。

如果你不知道电子是什么，高层次的描述是它是一个可爱的 chromium 浏览器的包装。这意味着你可以将一个漂亮的单页 web 应用程序变成一个桌面应用程序，使用你已经知道的 web 技术- javascript，css，html。非常棒。

[如果你不想从头开始，你可以使用 Vue，和令人惊讶的电子 vue-cli builder 插件([https://nklayman.github.io/vue-cli-plugin-electron-builder/](https://nklayman.github.io/vue-cli-plugin-electron-builder/))，这是我做的，因为我是*懒*

### App 已创建！我们完了！对吗？

嗯，不。你已经做了电子应用程序，现在你必须对它进行代码签名，除非你不介意你的用户收到安全警告。

我在苹果电脑上。一旦我设法找到了相关的链接，实际的代码签名过程就变得相当简单了。有了足够的 google-fu，你会发现如何做而不需要太多的麻烦。我将跳过代码签名的 mac 应用程序，因为其他人会告诉你如何做。

### Windows 则相反...

事实证明，试图在 Mac 上对你的应用程序的 Windows 版本进行代码签名是一种黑魔法，或者至少看起来是这样描述的；大量的“brew 安装”这个和“源代码编译”那个。你会发现很多关于它的文章。它们有用吗？谁知道呢？他们似乎都没有说同样的话，如果他们说了，我肯定没有足够的能力弄清楚他们想让我做什么。当我按照这些旧的指示去做的时候，结果是别的东西坏了或者不起作用了。这很艰难。一定有更简单的方法，对吗？

## 电子建造者

原来有不同的插件可以用来将你的源代码编译成一个好的版本。Vue Cli 给我选的那个是`electron-builder`。

他们有一些不错的文档，但截至 2019 年，其中一些很快就变得非常混乱。我不打算链接到有问题的文档，因为我确信在某一点上，它工作得很好。就像这可能会在 2019 年对你很有用，但在 2023 年可能就没那么有用了，等等。

# 已经开始了！

以下是我在 2019 年从 Mac 电脑对 Windows 应用程序进行代码签名的步骤

1.  使用“钥匙串访问”( mac 上的一个程序)发出 CSR(证书签名请求)。使用菜单中的“证书助理”。
2.  确保为 CSR 选择“保存到磁盘”
3.  从证书颁发机构购买代码签名证书([https://docs . Microsoft . com/en-us/windows-hardware/drivers/dashboard/get-a-code-signing-certificate](https://docs.microsoft.com/en-us/windows-hardware/drivers/dashboard/get-a-code-signing-certificate))。当它向您询问时，请使用您之前生成的 CSR。
4.  下载证书。将它安装在您的钥匙串中。(使用钥匙串访问)
5.  在“钥匙串访问”中，找到证书，并将其导出为`.p12`证书。
6.  在导出时，请注意，您可以选择为证书提供密码。你可以，如果你想的话，但是你也可以只点击“回车”而不提供密码。
7.  当您为 windows 构建您的电子应用程序时，指定到`.p12`证书的路径。`WIN_CSC_LINK=/path/to/your/cert.p12 yarn electron:build --win`

*注意*:在第 6 步中，您不需要添加密码，但是如果您添加了密码，那么在第 7 步中，您还需要在命令行中添加`WIN_CSC_KEY_PASSWORD=YourPassword`的 ENV 变量，以便它能够正确地解析证书。

通过查看构建日志，您应该能够看到证书已经过签名。

### 最后一笔——微软 SmartScreen

微软 Smartscreen 不仅仅关心你的应用程序代码签名是否正确；这也是看你的*口碑*。因此，如果你的应用是新的，那么你的用户可能会收到来自微软 SmartScreen 的警告屏幕，直到你的应用证明自己不是某种窃取密码的勒索软件睡眠代理。证书已经发布了，所以这应该是你得到的唯一警告，如果有的话。
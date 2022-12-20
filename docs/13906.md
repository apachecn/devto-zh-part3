# #RoastMySetup

> 原文：<https://dev.to/awwsmm/roastmysetup-3o0j>

有时候，与其试图清理你的电脑，不如干脆把它烧成灰烬。我最近在工厂重置 MacBook Air 时就这么做了。**在确保首先备份了我所有的文件之后**，我在安全模式下重启，[按照这些指示](http://www.tomsguide.com/faq/id-3678832/factory-reset-macbook-pro.html)到*安全地*工厂重置了我的 Mac。一旦我恢复运行，以下是我重新安装所有垃圾的顺序:

### 密码管理器

首先:我首先安装我选择的密码管理器 [Dashlane](https://www.dashlane.com) 。Dashlane 保存了我所有的登录信息，所以在 Dashlane 启动并运行之前，安装任何其他东西都没有多大用处。

### 杀毒

然后，为了安全起见，我安装了杀毒软件。安装一个杀毒软件总是一个好主意，即使你是那种认为你不会感染病毒的人，因为你没有使用 Windows。我使用 Dashlane 登录我的 Avast 帐户。我在安装过程中关闭了 Avast SecureLine VPN 和 Avast 密码，因为我不使用这两个功能。

### 浏览器

当然，没有人能独自在 Safari 上生存，所以下一个必需品是[谷歌浏览器](https://www.google.com/chrome/)。我使用 [Dashlane Chrome 扩展](https://chrome.google.com/webstore/detail/dashlane-password-manager/fdjamakpfbbddfjaooikfcpapjohcfmg)在浏览器中轻松访问我所有的账户密码。Chrome 很棒，因为它可以同步我的搜索历史、打开的标签页等。在我的手机和所有我经常使用的电脑上。注意，由于我已经登录了 Chrome，我不需要安装任何我常用的浏览器扩展...谷歌会自动帮我做。但下面是我用的一些:

*   非常适合快速分享链接，而不会有一百万个字符长
*   自从我开始使用它以来，没有人抱怨过它
*   隧道熊 -当我住在爱尔兰时，偶尔会看美国网飞的节目
*   这个扩展通过移除样式、广告等使网页更容易阅读。如果你只想打印一个页面的*内容*，而不是随之而来的所有额外垃圾，这也很棒
*   Wolfram|Alpha -一个真正强大的计算器，内置在你的 Chrome [omnibox](https://developer.chrome.com/extensions/omnibox) (“搜索栏”)
*   [动力](https://chrome.google.com/webstore/detail/momentum/laookkfknpbbblfpciffpaejjkokdgca) -漂亮的风景可以帮助你忘记你在办公室里一天盯着屏幕 8 个小时
*   [维基百科搜索](https://chrome.google.com/webstore/detail/wikipedia-search/lipakennkogpodadpikgipnogamhklmk)——只需输入“维基……”直接从 omnibox 搜索维基百科
*   [Dashlane](https://chrome.google.com/webstore/detail/dashlane-password-manager/fdjamakpfbbddfjaooikfcpapjohcfmg) -在 Chrome 中轻松访问您的 Dashlane 密码；自动登录等。
*   Avast 杀毒软件 -让 Avast 检查网站是否有信誉，并报告看起来不太好的网站

### JVM 管理(& & Git & &家酿)

接下来，让我们去终端，在那里我做的第一件事就是安装 SDKMAN！。(我经常使用基于 JVM 的语言。)SDKMAN！很棒，因为它管理所有基于 JVM 的软件的所有版本，并让您轻松安装或卸载相同程序的多个版本。我用 SDKMAN！来管理我的...

*   绝妙的
*   Java 语言(一种计算机语言，尤用于创建网站)
*   我的锅
*   专家
*   sbt
*   Scala，以及
*   火花

...版本。不过，在不同版本的 Java 之间轻松切换是另一回事，我使用 [jEnv](http://www.jenv.be) 来处理这个问题。不过，在我安装 jEnv 之前，我需要得到 **git** 。为此，我首先[为 Xcode 安装命令行工具](https://www.howtogeek.com/211541/homebrew-for-os-x-easily-installs-desktop-apps-and-terminal-utilities/):

```
$ xcode-select --install 
```

Enter fullscreen mode Exit fullscreen mode

请注意，**您不需要完整的 Xcode 应用程序**(我上次检查时大约有 10GB)，只需要命令行工具，这些工具使用上面的命令安装在**终端**中。然后我用
安装**自制软件**

```
$ ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)" 
```

Enter fullscreen mode Exit fullscreen mode

...并用它来安装 **git** :

```
$ brew install git 
```

Enter fullscreen mode Exit fullscreen mode

最后，我可以用 git 或 Homebrew 得到 jEnv。这里用 git:

```
$ git clone https://github.com/gcuisinier/jenv.git ~/.jenv 
```

Enter fullscreen mode Exit fullscreen mode

在为我的环境配置了 jEnv 之后...

```
$ echo 'export PATH="$HOME/.jenv/bin:$PATH"' >> ~/.bash_profile
$ echo 'eval "$(jenv init -)"' >> ~/.bash_profile 
```

Enter fullscreen mode Exit fullscreen mode

我可以让它识别我之前用 SDKMAN 安装的 Java 版本！

```
$ source ~/.bash_profile 
$ jenv add ~/.sdkman/candidates/java/11.0.2-open/
openjdk64-11.0.2 added
11.0.2 added
11.0 added 
```

Enter fullscreen mode Exit fullscreen mode

请注意，您的 Java 版本可能与我的不同。[我让 jEnv 通过设置
来管理我的`$JAVA_HOME`变量](https://stackoverflow.com/questions/28615671/set-java-home-to-reflect-jenv-java-version)

```
$ jenv enable-plugin export 
```

Enter fullscreen mode Exit fullscreen mode

...这需要终端重启。现在，jEnv 将展示 Java 的“系统”版本，因为我还没有设置默认版本:

```
$ echo $JAVA_HOME
/Users/andrew/.jenv/versions/system 
```

Enter fullscreen mode Exit fullscreen mode

还是用 SDKMAN 吧！和 jEnv 安装第二个 Java 版本并管理哪个版本是默认版本。使用`sdk list java`查看哪些安装候选可用于 Java(用 SDKMAN 的任何其他部分替换`java`！-管理软件以查看适用于*的版本。我将在默认的`11.0.2-open` :
旁边安装`8.0.201-oracle`*

```
$ sdk install java 8.0.201-oracle
$ jenv add ~/.sdkman/candidates/java/8.0.201-oracle/
oracle64-1.8.0.201 added
1.8.0.201 added
1.8 added 
```

Enter fullscreen mode Exit fullscreen mode

现在我可以看到我安装的所有 java 版本:

```
$ jenv versions
* system (set by /Users/andrew/.jenv/version)
  1.8
  1.8.0.201
  11.0
  11.0.2
  openjdk64-11.0.2
  oracle64-1.8.0.201 
```

Enter fullscreen mode Exit fullscreen mode

并用
选择全局默认值

```
$ jenv global 1.8
$ echo $JAVA_HOME
/Users/andrew/.jenv/versions/1.8
$ java -version
java version "1.8.0_201"
Java(TM) SE Runtime Environment (build 1.8.0_201-b09)
Java HotSpot(TM) 64-Bit Server VM (build 25.201-b09, mixed mode) 
```

Enter fullscreen mode Exit fullscreen mode

更改 Java 版本现在就像运行一个`jenv`命令一样简单:

```
$ jenv global 11.0.2
$ echo $JAVA_HOME
/Users/andrew/.jenv/versions/11.0.2
$ java -version
openjdk version "11.0.2" 2019-01-15
OpenJDK Runtime Environment 18.9 (build 11.0.2+9)
OpenJDK 64-Bit Server VM 18.9 (build 11.0.2+9, mixed mode) 
```

Enter fullscreen mode Exit fullscreen mode

### 降价/明文编辑

接下来，我安装了我选择的 markdown 编辑器 [Simplenote](https://simplenote.com) ，这样我就可以写这篇博文了。当你一直在输入代码时，自动纠正拼写和自动大写单词不是很好，所以我在编辑>拼写和语法>{输入时检查拼写，自动纠正拼写}中禁用了这些功能。

类似地，我有时会使用 Mac 的**文本编辑**来复制和粘贴小代码片段。我打开它，更改了许多默认设置，使它更像 Windows 的记事本(只是一个简单、无装饰的纯文本编辑器):

*   前往“文本编辑”>“偏好设置”
    *   从“富文本”切换到“纯文本”
    *   在底部的“选项”下，取消选中:
        *   "键入时检查拼写"
        *   "用拼写检查语法"
        *   "自动更正拼写"
        *   "显示标尺"
        *   "智能引号"
        *   "智能破折号"
    *   在“打开并保存”页面上，取消选中:
        *   “补充一下。纯文本文件的 txt 扩展名"

关闭“文本编辑”,下次打开它时，它将是一个简单易用的纯文本编辑器。

### 非 JVM 编程语言

macOS 安装了 **Python** v2.7.10，但是如果你想要另一个版本，你将需要一个版本管理器。我不经常使用 Python，所以我对默认设置很满意。Ruby 也是默认安装的(撰写本文时是 v2.3.7p456)，Perl 也是默认安装的(v5.18.2)。

我经常在 **R** 中写代码，所以我用
安装 R

```
$ brew install R 
```

Enter fullscreen mode Exit fullscreen mode

然后，我从[他们的下载页面](https://www.rstudio.com/products/rstudio/download/#download)获取 **RStudio** 作为`*.dmg`文件并安装。

我开始学习 Haskell，所以我通过[从这个页面](https://www.haskell.org/platform/mac.html)下载来安装 **Haskell Platform** for Mac。

### 其他软件

*   Adobe Acrobat Reader -我讨厌在 Mac(或 Chrome)上预览阅读 PDF，所以一个好的 PDF 浏览器是必须的。读者 DC 让你签署和填写表格，这是方便的。
*   Dropbox——我用的是 Dropbox Basic(免费版)，默认只提供 2GB 的空间。但我在 Dropbox 开始时得到了一些宣传，所以我有 5.2GB。(哦，我很喜欢。*)
*   我下载了除 Edge 之外的所有主流浏览器，以便测试、调试前端程序等。一定有更简单的方法来检查一个网站在主流浏览器上的表现，对吗？(建议，有人吗？)
*   [Fitbit Connect](https://www.fitbit.com/ie/setup) -我用 Fitbit 跟踪我的步数、心率和体重，Fitbit Connect 让我可以通过 Mac 同步我的 Fitbit(当不方便通过手机同步时)
*   Google Drive——我用 Dropbox 存放个人文件，用 Google Drive 存放工作文件，因为我在工作中有一个无限量的 Google Drive。我喜欢将个人和专业的基于云的存储解决方案分开
*   Keynote - Keynote 并不神奇，但它是我在 Mac 上制作幻灯片的必备工具。我通常在 Windows 上使用 Powerpoint。我并没有真的和这两种人结婚，我很乐意收到其他选择的建议。
*   微软办公软件(Microsoft Office)——我通过 portal.office.com 的从我任教的大学获得免费的办公产品。Word、Excel、Outlook 和 Powerpoint 是必备软件。
*   Opera &我下载的另一个浏览器，用于调试和检查我在多个浏览器上做的任何前端东西看起来如何
*   [画笔](https://paintbrush.en.softonic.com/mac)——想 MS 画图，但是为了 Mac。如果你只是想快速裁剪或调整某些东西的大小，这个应用程序是很棒的。
*   Skype -与世界各地的朋友和亲戚通话的必备工具。也有助于拨打长途电话，而无需支付荒谬的费率。
*   我是我哥哥家庭付费账户的吸血鬼。
*   这是一个非常酷的夜空应用。我用它来上我教的在线天文学入门课。
*   Virtualbox - Virtualbox 是我创建虚拟机的得力助手。我可以在 Mac 或 Windows 的虚拟机上运行 Ubuntu，而不必进入某个远程服务器。
*   VLC - VLS 是我的媒体播放器选择，没有别的原因，只是因为它似乎兼容最广泛的各种媒体格式。

* * *

...这就是我的基本设置！在接下来的一篇文章中，我希望介绍一下我的定制`bashrc`脚本，以及我如何使用`git`在我的所有机器上维护它。请在下面的评论中告诉我你对我的设置的看法，尤其是如果你对替代软件有任何建议的话！

感谢阅读！

* * *

*我是最真实的。
# 用 Anaconda 在 Windows 上设置 Python

> 原文：<https://dev.to/katiekodes/setting-up-python-on-windows-with-anaconda-3c8n>

我一直警告学生们不要在 Python 中使用真实的、公司的 CSV 文件和在线“ide”，如 [Repl.it](https://repl.it) 或 [CodeBunk](https://codebunk.com) ，而是在你的本地机器上安装 Python。**最后，我写了一个教程来引导你完成它！**

这些截图记录了我所知道的在 Windows 机器上安装 Python 的最简单的方法。

**这就是这么简单。**你只需下载软件，双击安装程序，让它运行，打开代码编辑器，然后像变魔术一样执行你的第一个`print('Hello World')`。

但我在这里会带你走过每一步，并解释在后台发生了什么。

如果事情不工作，你可以处理一些更复杂的事情，参见[我在 Windows](https://katiekodes.com/setup-python-windows-miniconda) 上设置“miniconda”的说明。

* * *

## 安装 Anaconda &运行 Python 程序

### 下载

首先，[为“Anaconda”](https://www.anaconda.com/distribution/)下载一个合适的安装文件。

检测到你在 Windows 上是不明智的，所以直接点击下载上面的“Windows”选项。

[![](img/248154e1788baf7bedbf4abe4071a745.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tIfbcuDM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/screenshot-anaconda-01-download.png)

我建议买 64 位版本的，只要你的电脑是 64 位的。

我能想到的在您的计算机上安装 32 位版本的唯一原因是，比方说，您将使用已经安装在您的计算机上的“Oracle 客户端”软件连接到 Oracle 数据库，该软件是 32 位的*(例如，因为数据库本身需要 32 位客户端)*。

或者，我想，如果出于某种奇怪的原因，你有一台 32 位计算机。但是你可能担心你安装的东西会“膨胀”,并且无论如何都要遵循更难的 miniconda 指令。

如果我刚才说的没有任何意义，做 64 位。

你想要一个“Python 3.something”，而不是“Python 2.something”

### 运行 Anaconda 安装程序

双击“.exe”文件来运行安装程序。点击**下一步**，然后在接下来的屏幕上点击**我同意**。

[![](img/351955c2207805606b05a66f9bb0e00a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Qy2t7Phi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/screenshot-anaconda-02-run.png)

[![](img/3b2336f0fe0bbc8891c296a89ad60a98.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9RCVnu7I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/screenshot-anaconda-03-run.png)

[![](img/a3930048a13633f46a72ebe5205e3252.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OoZZjC08--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/screenshot-anaconda-04-run.png)

### 选择“只有我”或“所有用户”

我将为“只有我自己”安装此软件，因为我没有在公司电脑上安装软件的管理员权限。

我在这里的选择会影响安装程序是否建议在以下位置安装程序:

*   `C:\Users\MY_USERNAME\AppData\Local\Continuum\anaconda3` *(只有我)*，或者
*   `C:\Program Files\Anaconda3` *【所有用户】*

[![](img/462eed87af229a9903dd5f8f611273b7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ouJv7yKm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/screenshot-anaconda-05-justme.png)

### 安装文件夹

忘记软件何时安装在我的`\AppData\Local\`隐藏文件夹中让我很烦恼，所以我将在下一个屏幕上手动将安装路径更改为`C:\Users\MY_USERNAME\Documents\ProgramFilesForSoftwareHatingSpaces\Continuum\anaconda3`。

是的，我把那个文件夹叫做“`ProgramFilesForSoftwareHatingSpaces`”——当 Anaconda 抱怨我在“我的文档”下设置的“程序文件”文件夹时，我感到有点生气。

[![](img/1e9459fb40acdeef81524e27c744a003.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oZtt6WqA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/screenshot-anaconda-06-nospaces.png)

[![](img/c018ce310eeb5238202dd88585745b6a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--w0N85JR2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/screenshot-anaconda-07-choosepath.png)

### 选项

接下来，安装程序会问我是否想做两件事情中的任何一件。

1.  将“Anaconda”添加到我的“PATH”环境变量中。它在安装文本中说不要这样做，所以我要让它不被选中。
2.  将“Anaconda”注册为我的“默认 Python 3.7”环境。这是检查过的，他们推荐它，嘿…让我们做吧。我做到了。

现在我将点击**安装**

[![](img/42f62db838d3faa1b2d171d2964b9486.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WQCLUA6F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/screenshot-anaconda-08-checkboxes.png)

### 一边跑

安装程序会花几分钟时间将数千个小文件转储到您的硬盘上。

问:为什么有这么多文件？

答:你的计算机并不自然理解 Python 命令。

在一个非常低层次上，它理解其“汇编语言”中的命令*(您不希望必须手写)*。

所有的 Windows 电脑都理解一种叫做“C”的语言*(如果你不想成为一名职业程序员，你可能还是不想用手写)*。

许多用“C”编写的支持程序教你的 Windows 电脑如何理解用“Python”*编写的命令(假设你，或者一个代表你的“IDE”之类的程序，告诉 Windows 你在哪里安装了可以帮助解析这些命令的软件——对我来说，那就是 **`C:\Users\MY_USERNAME\Documents\ProgramFilesForSoftwareHatingSpaces\Continuum\anaconda3\python.exe`** )* 。

此外，Anaconda 的完整版本附带了大量 Python“库”/“包”/“模块”*(可以用来编程的 Python 基本命令集的扩展)*，所有这些都必须在您的计算机上有文件，以便您的计算机可以理解它们的意思。

如果你不想要这么多，你可以按照更难的说明在 Windows 上设置“miniconda”。

[![](img/57de7b750c1b1f30bbd5b1e4115abc19.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--exu2NW-d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/screenshot-anaconda-09-extracting.png)

### 整理

当显示“完成”时，点击下一个的

 **[![](img/ca117ab0d6ba9f72e1bc3c66efbe9804.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CS37ugv9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/screenshot-anaconda-10-completed.png)

不要费心去安装 vs code——它是一个“IDE”*(代码编辑器)*，但是你已经通过这次安装获得了另一个名为“Spyder”的软件，这就足够了。点击**跳过**

[![](img/e08584bfc950d0627b88ce054bafbb5f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WY_mOGBo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/screenshot-anaconda-11-vscode.png)

选择“了解更多”复选框，或者取消选择，然后点击“**完成**”

*(第二个复选框把你带到[这里](http://docs.anaconda.com/anaconda/user-guide/getting-started/)；第一个只会把你带到 Anaconda 的网站。)*

[![](img/b9b648c217569c2418b3951f429ca619.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tADPv3hG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/screenshot-anaconda-12-finish-install.png)

* * *

## 编写你的第一个 Python 程序

如果你已经在像 [Repl.it](https://repl.it) 或 [CodeBunk](https://codebunk.com) 这样的在线“IDE”中练习过编码，你就会知道编码可以像每次你输入足够多的文本时点击一个大的“运行”按钮一样简单，以至于你想知道它是干什么的。

你现在硬盘上也有同样的东西！它叫 **Spyder** 。

Anaconda 的安装程序帮你安装的。

如果你看看你的“开始”菜单*(当你点击屏幕左下角的 Windows 图标时弹出的东西)*，你可能还会注意到一个名为“ **Anaconda3 (64 位)**的新文件夹，它下面有几个图标。

点击写着“Spyder”的那个

[![](img/54ac45c6a9f53db6f7ab8ad3ab6a1b2f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JDmZVQHL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/screenshot-anaconda-14-startmenu.png)

启动时的初始屏幕如下所示:

[![](img/4f44d69ee15c61e3482e0816a5bd98d9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--izs1yO-o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/screenshot-miniconda-19-runspyder.png)

在任务栏中，一旦它启动并运行，它的图标看起来像这样:

[![](img/f0fec381491f90e76aaf08ff60c54f0f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YwGM2u7p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/spydericon.png)

当它完全打开时，通常看起来像这样:

*   左边是你编辑代码的地方，有一个名为“untitled . py”*(暂定建议你保存在`C:\users\YOUR_USERNAME\untitled0.py` )* 的新文件，内容是:

```
# -*- coding: utf-8 -*- """
Created on SOME_TIMESTAMP

@author: YOUR_USERNAME
""" 
```

在右边，或者右下角，将会是某种“控制台”，每当你点击屏幕顶部附近的绿色“运行”按钮*(这是一个向右的绿色三角形)*，你就可以在这里看到代码的执行。

[![](img/aa17e5954d02c7fdad24718e8f4e79f3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vDMCjVfA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/screenshot-miniconda-20-spyderstart.png)

在顶部菜单中，点击**文件**->-**另存为**

为你的程序选择一个好的文件夹*(我个人打算把我的保存在`C:\example\`下，但你可能想把它放在你的桌面上)*，并以文件名“ **`hello.py`** ”保存在那里。

如果你在硬盘上给代码一个合适的位置之前试图运行代码，Spyder 的“运行”按钮会报错。你可以只做文件- >保存，但我个人不能忍受在我的`c:\users\MY_USERNAME\`里塞满像“无标题 0”这样的文件)

[![](img/44035384ee2142c404a990ea6d95cd69.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---X5myCEr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/screenshot-miniconda-21-saveas.png)

*(注意照片中我的叫`hello2.py`，因为我复用了一个不同教程的截图，有 2 个步骤！)*

现在删除文件的全部内容，在第 1 行输入:

```
print('Hello world') 
```

然后点击绿色的“运行”按钮。

您可能会看到一个弹出对话框，其中有各种关于执行代码控制台或解释器等的选项。

只需在弹出的对话框中点击“取消”，然后再次点击播放按钮即可。

弹出框让您有机会高度定制您希望在哪里看到运行代码的任何输出。

点击“取消”并再次尝试似乎是在告诉 Spyder，“你弄清楚在哪里显示这段代码的输出，不要用所有这些选项来烦我。”

(不幸的是，我没有用截图捕捉到这个对话框——Spyder 似乎记得我以前在“大蟒蛇”中使用它时的设置)

您的输出应该是这样的——在屏幕右侧的某个地方，可能是在一个名为“控制台 1/A”的控制台中，您会看到“ **`hello.py`** ”的文件路径的确认，“后面是一个新的文本行“`Hello World`”。

*(注意照片中我的说的是`Hello again`)因为我重复使用了一个不同教程的截图，有 2 个步骤！)*

[![](img/883dfaf80c046714560490bed4026c0c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4DokLmto--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/screenshot-miniconda-22-run.png)

🎉🎊🙌

祝贺你——你已经**编写并运行了一个 Python 程序**！

🎈🎆👍🏾

在顶部菜单中，点击**文件**->-**保存**因为为什么不以这个工作代码为荣呢？习惯一喜欢就把作品存起来就好了。

但是在我们编写一个更大的程序之前，让我们稍微转移一下注意力来学习一下“模块”它们在 Python 中非常重要。

* * *

## 模块

### 检查是否安装了“模块”

现在，您需要了解安装了 Python 语言的给定扩展(也称为“库”或“包”，尤其是 Python 中的“模块”)后的情况。

退格删除整个代码，在第 1 行输入:

```
import pandas 
```

*   花絮:代码中行号左边的橙色警告三角形告诉你，你“导入”了一个你的程序实际上在程序的其他地方并不使用的包。这是 Spyder 试图帮助您编写更好、更高效的代码。
*   行号左边带“x”的红圈表示一行甚至不包含合法的 Python 代码。这些是你需要关心的，否则你的程序不会运行。

#### 耶，它存在！

**点击“运行”按钮。**

如果你**确实**安装了“熊猫”模块，没有什么特别的事情发生。

您应该安装它——它附带了 Anaconda 的“大”/主流版本。像熊猫这样的模块是 Anaconda 花这么长时间安装的原因！

如果你安装了“pandas”，当你运行这段代码时，你只是在右边的框中得到另一个命令提示符(“`In[SomeNumber]`”)，方括号中的数字比之前大 1。

[![](img/98bb9ea392c7a09c8f4dc53e3d3cebcc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Uy5jnXQl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/screenshot-miniconda-25-pandas-works.png)

(注意:在上面的截图中，事情看起来略有不同，因为我还是太懒了，重用了不同教程中的截图——抱歉！)

#### 嘘，不存在！

让我们尝试“导入”一个您可能没有的模块，因为它不是 Anaconda 自带的。

退格删除整个代码，在第 1 行输入:

```
import simple_salesforce 
```

**点击“运行”按钮。**

如果您的控制台中出现大量彩色文本，大错误消息的最后几行显示如下内容，那么您现在知道您没有安装名为`simple_salesforce`的模块:

```
 exec(compile(f.read(), filename, 'exec'), namespace)

  File "C:/example/hello.py", line 1, in <module>
    import simple_salesforce

ModuleNotFoundError: No module named 'simple_salesforce' 
```

### 安装一个“模块”

Anaconda 提供了初学者所需的一切，所以现在还不要麻烦在您的计算机上安装额外的“模块”。

然而，如果你需要的话，请参阅我在 Windows 上设置“miniconda”的说明的“模块”部分——安装“miniconda”的人必须手动安装他们自己的所有“模块”,所以我在这些说明中一步一步地讲解了它！

* * *

## 编写你的第二个 Python 程序

退格出你的全部代码。

将以下代码复制并粘贴到您的代码编辑器中，然后运行它:

```
import pandas
pandas.set_option('expand_frame_repr', False)
filepath = 'https://raw.githubusercontent.com/pypancsv/pypancsv/master/docs/_data/sample1.csv'
df = pandas.read_csv(filepath)
print(df) 
```

您应该会在右侧的控制台中看到以下输出:

```
 Id First Last Email Company
0 5829 Jimmy Buffet jb@example.com RCA
1 2894 Shirley Chisholm sc@example.com United States Congress
2 294 Marilyn Monroe mm@example.com Fox
3 30829 Cesar Chavez cc@example.com United Farm Workers
4 827 Vandana Shiva vs@example.com Navdanya
5 9284 Andrea Smith as@example.com University of California
6 724 Albert Howard ah@example.com Imperial College of Science 
```

现在你已经用 Python 打开了一个 CSV 电子表格文件——你开始比赛了！

点击[这里](https://pypancsv.github.io/pypancsv/quickexamples/)学习新技能，并在你的电脑上付诸实践。

* * *

## 快乐编程

您现在有了一个用 Python 编辑 CSV 文件的工作环境！

1.  你有一个“IDE”，或者我喜欢把它想成是一个“带有运行按钮的文本编辑器”，叫做 **Spyder** ，它可以运行你认为合适的代码。
2.  你知道去哪里学习安装和更新你听说可能有用的“模块”。*(小心，不要相信你在网上找到的任何旧模块。它只不过是“你选择在你的计算机上运行的别人的代码。”确保不是来自某个不可靠的人。)***
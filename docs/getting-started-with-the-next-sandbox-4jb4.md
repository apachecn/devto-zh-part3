# 开始使用下一个沙盒

> 原文：<https://dev.to/nexttech/getting-started-with-the-next-sandbox-4jb4>

我很兴奋地宣布 Next Tech 的最新产品发布了:Next Sandbox！

[下一个沙盒](https://next.tech/sandbox/for-devs)提供直接从您的浏览器在 2 秒钟内访问真实世界的计算环境。您不必在计算机上安装想要使用的编程语言或软件，只需单击一个按钮，就可以使用基于浏览器的开发工具立即访问该语言或软件。

我们构建沙盒是为了解决我们作为开发人员几乎每天都要经历的两个关键问题:

1.  安装软件是 haaaaaaard。即使是经验丰富的开发人员也可能在这方面遇到困难，这可能会妨碍尝试新的想法。有现成的东西很好，这样你就可以专注于创新。
2.  当你自己在电脑上安装软件时——尤其是如果你只是想测试它——它会很快让你的环境变得混乱。也许您想尝试最新版本的 Java，但是在您的计算机上安装它可能会导致您的当前版本出现问题。

下一个沙盒通过将安装软件的负担从您身上转移到我们身上来解决这些问题。但是没关系，我们已经做了很多年了！下一个沙盒基于相同的基础设施和界面，为我们用于教学和学习技术技能的产品提供支持，该产品被全球领先的教育公司所使用。事实上，哈佛的 CS50 项目实际上在我们的基础设施上构建了他们自己的沙箱！

这是你第一次启动 it:‌时，Python 编程语言的沙箱的样子

[![img](img/f5ae17cbdf516edf13b8f3d12d6ce5a1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0DSgRbjW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nu7r35h701v0zojqe0o7.png)

在这本入门指南中，我们将介绍如何启动您的第一个沙盒，以及您可以用它做的一些令人兴奋的事情。

让我们开始吧！

# 先做第一件事

首先，如果您还没有注册 Next Tech，我建议您使用此链接进行注册:

[nt.dev？ref=ce315f8c](https://nt.dev/?ref=ce315f8c)

Next Tech 使用基于分钟的定价，你在打开沙盒的页面上花费每分钟 1 美分。当你注册的时候，你通常会得到 10 美元的免费积分(也就是 1000 分钟的编码时间！)，但是如果你使用那个链接，你会得到 15 美元！

每当被推荐的用户激活他们的账户时，该推荐链接的账户也接收 5 美元。**如果您注册了该链接，您将有机会赢得访问该帐户的机会，因为一旦帐户价值达到 250 美元，我们将随机选择一名推荐用户获胜！**

如果你对我们的定价有任何问题，请不要犹豫，直接在这个帖子上发表评论。

好吧，回到正题。一旦你创建了一个新的技术帐户，你有两个选择来启动一个沙盒:

1.  使用 nt.dev URL，比如[nt.dev/python](https://nt.dev/python)。这将立即让你进入一个新的 Python 沙盒。
2.  使用[发射台](https://next.tech/sandbox/launch)选择您想要的环境。该页面包含关于每个环境的许多详细信息。

最初，您的沙箱将显示一个目录树查看器、代码编辑器和终端。要添加一些代码，只需点击工作目录旁边的`+`图标或`...`，然后点击**新建文件**:

[![img](img/62db0f2ec45ef8ec4b5962247c008b28.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sun0FSVv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/om2d7gw2i6vzlusmzwox.png)

您将看到这样一个覆盖图:

[![img](img/560f4a687fd448830e575172608c60d4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_qQ41XhQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bxq4q7py9weha4ukd9e0.png)

继续创建一个名为`main.py`的文件。先不要担心底部的标签——我们很快就会覆盖它们！

或者——如果你想变得有趣——你可以在你的终端输入`touch main.py`。Linux `touch`命令将用指定的名称创建一个新文件。

一旦你创建了一个文件，它将被保存到你的工作目录中(在文件树中显示为`~/workspace`，它的绝对路径是`/home/nt-user/workspace`)。所以这个文件的完整路径是`/home/nt-user/workspace/main.py`。

你可以把任何你喜欢的 Python 代码放在这里，但是如果你手头没有，这里有一个快速计算器可以让你开始:

```
def add(x, y):
    return x + y

def subtract(x, y):
    return x - y

def multiply(x, y):
    return x * y

def divide(x, y):
    return x / y

if __name__ == '__main__':
    x = int(input('Please enter a number: '))
    y = int(input('Please enter another number: '))
    print(add(x, y))
    print(subtract(x, y))
    print(multiply(x, y))
    print(divide(x, y)) 
```

您可以从终端运行该文件，例如`python3 main.py`。您将看到程序的输出，例如:

```
workspace $ python3 main.py
Please enter a number: 123
Please enter another number: 456
579
-333
56088
0.26973684210526316 
```

‌The 终端将您连接到一个实际的计算环境。继续，在终端中试试`ls -l /`,在那里你会看到一个完整的操作系统目录结构！您还拥有完全的管理员权限(`sudo`)，因此您可以随意修改文件系统的任何部分。

你可以阅读更多使用沙盒的方法[这里](https://next.tech/sandbox/uses)！

# 其他计算环境

许多其他环境都有沙盒，包括:

*   常见编程语言，如 Java、C++、Swift 和 C#
*   Web 开发技术，如 HTML、CSS、JavaScript、Ruby on Rails 和 PHP
*   Python 和 R 的数据科学框架
*   像 PostgreSQL 和 MySQL 这样的数据库
*   Elm、Haskell、Golang 和 Rust‌等专业语言

你可以在这里找到环境[的完整列表。](https://next.tech/sandbox/launch)

每个沙箱都预装了某些程序。如果你启动了 Python 沙盒，你可以在终端运行`ipython`来跳转到[那个漂亮的软件](https://en.wikipedia.org/wiki/IPython)。也可以安装新软件，例如:

```
sudo apt install -y gnome-calculator 
```

每个沙箱都可以访问 256MB 的可用磁盘空间，并且可以使用高达 2GB 的 RAM。它还拥有高达 4 个 CPU 的处理能力。

# 标签式界面

运行上述命令后，通过点击`+`并选择桌面打开一个**桌面**标签，然后在终端中运行`gnome-calculator`。你会看到这个:

[![img](img/89204119b24112cfc9eff4890de07461.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cGRGs2rX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dq6rzosfyn7kqk4h7o1h.png)

这是一个在你的沙箱中运行的真正的计算器！

很圆滑，对吧？

您现在可能已经注意到，有许多其他选项卡可供您选择。以下是其他人的快速列表:

*   终端选项卡使您可以访问新的终端，这使得并发运行多个程序或命令变得很容易。
*   浏览器选项卡加载一个 web 浏览器，您可以使用该浏览器连接到在您的沙盒中运行的可访问 web 的服务。
*   Jupyter tabs 加载了一个实际的 Jupyter 笔记本供您使用，这对数据科学或机器学习非常有用。
*   Skulpt 是 Python 的一些基本特性的 JavaScript 实现，经常与`turtle`和`processing` Python 库一起使用。

# 分享你的作品

你可能已经看到页面右上角的**分享**按钮了！如果你向某人发送一个共享 URL，当他们点击该 URL 时，他们将获得一份你的沙盒副本。请注意，您**不会因其使用而付费。**

这是我分享的这个项目的网址，我用它来截取这篇文章的截图:

[next.tech/projects/0295f1cee35f/share](https://next.tech/projects/0295f1cee35f/share)

如果你点击它，你会被直接带到一个 Python 沙箱，里面有计算器代码。注意它不会安装`gnome-calculator`,因为(目前)沙箱不会保存新安装的软件，所以如果你想尝试的话，你必须自己安装。

这里有许多其他预制的沙盒已经准备好发布！

# 下载您的作品

当您在 sandbox 中工作时，您创建的文件(和选项卡)会自动保存。但是，有时您可能希望将它们保存到您的本地机器上，例如，如果您已经测试了一个概念验证，并且现在希望作为您的正常开发过程的一部分继续使用它。

为此，只需点击页面左下方的设置齿轮，然后选择**下载**。

# 总结

咻，要涵盖的内容太多了！让我们回顾一下。上面，我们探讨了:

1.  在几秒钟内启动 Python 的计算环境(特别是如果您使用 nt.dev URL 的话)
2.  用 Python 编写一个基本计算器
3.  沙盒中可供您使用的各种界面元素
4.  在沙箱中安装软件(`gnome-calculator`)
5.  打开实际的桌面环境并使用该软件
6.  下载您的作品以便在您的本地开发环境中使用

这只是一个让你开始的快速指南。如果你愿意，你可以在这里探索沙盒文档的其余部分，了解更多关于你可以在沙盒中做什么！

如果你有任何反馈想要分享，请不要犹豫回复这个帖子。我们希望收到您的来信！
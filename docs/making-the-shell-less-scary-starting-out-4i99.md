# 让外壳不那么可怕——开始

> 原文：<https://dev.to/ballpointcarrot/making-the-shell-less-scary-starting-out-4i99>

我完全承认，我从小就习惯在没有 GUI 的电脑上工作。从我早期开始，我有一台主要用来玩游戏的旧电脑(有些东西永远不会变，但当前的不会出现在 5 1/4”软盘上)；为了运行这些游戏，我从妈妈那里学会了如何访问正确的驱动器，以及运行命令来启动游戏。

今天，即使我们有漂亮的 GUI 应用程序来完成我们每天需要完成的许多工作，我仍然发现快速打开一个终端窗口，输入几个命令，完成我正在做的任何任务对我来说都更容易。并不是所有东西都能从这种方法中受益(想象一下用文本编辑器编辑图像！)，但是涉及到查找文件、在系统中移动文件以及在远程系统上工作的任务是我更喜欢用终端来完成的。

今天，我将深入探讨几个命令，这些命令可以完成我刚才谈到的任务。这些是您将在终端窗口中日常使用的一些命令；希望我能在他们的某些角落里闪耀出一些你意想不到的光芒。

### 目录和文件

假设你是一个狂热的摄影师，你会收集很多照片。为了让你的收藏有意义，你需要整理这些照片。如果它们是实物照片，您会希望通过拍摄它们的活动来收集它们:

*   蒂姆的生日聚会
*   乔的婚礼
*   拉斯维加斯的度假照片
*   食物的随机照片
*   太多猫的照片了
*   等等。

此外，假设这些照片是在几年内拍摄的。

借用物理领域，我们可以将这些照片放入不同的文件夹，或*目录*，以保持它们的组织性。让我们把它们像这样摆放:

*   Two thousand and eighteen
    *   乔的婚礼
    *   I <3 食物
    *   我的猫
*   Two thousand and nineteen
    *   蒂姆的生日聚会
    *   拉斯维加斯之旅
    *   我的猫
    *   I <3 食物

您会注意到这里有重复的名称——没关系，因为它们嵌套在两个不同的文件夹中。

现在，所有这些文件夹名称都是合法的，但是当在命令行中使用一些特殊字符和空格时，就有点难处理了。标准惯例是替换名称中的空格和特殊字符，以便于处理，同时保留原始名称中的大部分含义；大多数替换是破折号或下划线之类的东西。这可能是我的替代品:

*   Two thousand and eighteen
    *   乔-婚礼
    *   I-心脏-食物
    *   我的猫
*   Two thousand and nineteen
    *   蒂姆·戴·巴什
    *   拉斯维加斯之旅
    *   我的猫
    *   I-心脏-食物

语法上非常相似，而且打字时更容易处理。

因此，与其打开文件管理器窗口，不如让我们在终端用命令来构建这个结构。

#### `mkdir`异人 dirs

`mkdir`命令用于创建一个目录。基本的调用非常简单:

```
$ mkdir <folder-name> 
```

Enter fullscreen mode Exit fullscreen mode

此命令将基于您当前所在的目录(称为“当前工作目录”或“当前工作目录”)创建一个具有给定名称的新目录。有时你使用的终端会在提示中告诉你你在哪里；即使没有，您也可以通过运行命令`pwd`来找到该目录。

```
$ pwd
/home/ballpointcarrot 
```

Enter fullscreen mode Exit fullscreen mode

那么让我们制作我们的照片收藏结构:

```
$ mkdir 2018 
```

Enter fullscreen mode Exit fullscreen mode

等待...什么都没回来。它做了什么吗？

#### 将`ls`添加到列表中

是一个多功能的命令——它给你一个 PWD 列表。当您查看您的文件系统时，这变得很方便，并且通常知道某个东西在哪里。如果我们在现在的位置做一个`ls`，你会看到类似于

```
$ ls
2018/   Desktop/        Downloads/      Notes/          Videos/
Books/  Documents/      Music/          Photos/ 
```

Enter fullscreen mode Exit fullscreen mode

哎哟...2018 年不在正确的地点。让我们用`mv` :
来移动它

```
$ mv 2018 Photos 
```

Enter fullscreen mode Exit fullscreen mode

同样，我们没有得到任何有意义的输出。然而，在接下来的`ls` :

```
$ ls
Books/          Documents/      Music/  Photos/
Desktop/        Downloads/      Notes/  Videos/ 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以看到 2018 文件夹已经不在了。我们希望确保它在 Photos 目录中，所以我们也要检查那里:

> 注意:这表明`ls`命令可以用在文件夹上——这意味着你不必是文件夹中的的*才能使用`ls`。当你搜索大量的子文件夹时，这很方便。*

```
$ ls Photos
10262018_0001.JPG  10262018_0035.JPG  10262018_0069.JPG  10262018_0103.JPG  10262018_0137.JPG  10262018_0171.JPG  10262018_0205.JPG  10262018_0239.JPG  10262018_0273.JPG  10262018_0307.JPG
10262018_0002.JPG  10262018_0036.JPG  10262018_0070.JPG  10262018_0104.JPG  10262018_0138.JPG  10262018_0172.JPG  10262018_0206.JPG  10262018_0240.JPG  10262018_0274.JPG  10262018_0308.JPG
10262018_0003.JPG  10262018_0037.JPG  10262018_0071.JPG  10262018_0105.JPG  10262018_0139.JPG  10262018_0173.JPG  10262018_0207.JPG  10262018_0241.JPG  10262018_0275.JPG  10262018_0309.JPG
10262018_0004.JPG  10262018_0038.JPG  10262018_0072.JPG  10262018_0106.JPG  10262018_0140.JPG  10262018_0174.JPG  10262018_0208.JPG  10262018_0242.JPG  10262018_0276.JPG  10262018_0310.JPG
... 
```

Enter fullscreen mode Exit fullscreen mode

哇哦！2018 文件夹呢？这些都是 JPG 的文件(我没有整理到照片文件夹中的照片)。幸运的是，我可以过滤掉从`ls` :
返回的对象类型

```
$ ls -d */
2018// 
```

Enter fullscreen mode Exit fullscreen mode

这是我们的文件夹。现在让我们解释一下这个命令:命令中的`-d`告诉`ls`我们只想要目录，而不是文件。然而，`ls`本身还不够聪明，不能独自抓取目录；我们必须显式地说抓取“所有目录”，这就是`*/`的用武之地——它用星号(也称为星号或通配符)表示“当前目录中的任何内容”,正斜杠表示指定为文件夹的任何内容。

让我们开始处理剩下的那些文件夹。首先，让我们摆正自己的位置。

#### `cd`——不仅仅是为了音乐

`cd`命令改变我们当前的工作目录。通过更改目录，它使我们能够根据我们在文件系统上所做的事情来更改上下文。在我们的例子中，我们将切换到照片目录，因为我们正在组织照片的上下文中工作。有道理。

```
$ cd Photos 
```

Enter fullscreen mode Exit fullscreen mode

同样，该命令现在已经有了输出，但是我们可以执行`pwd`并看到我们已经改变了位置:

```
$ pwd
/home/ballpointcarrot/Photos 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们完成 2018 年的文件夹。我很懒，想一次做完所有的事情——幸运的是，`mkdir`让我们这么做:

```
$ mkdir 2018/joe-wedding 2018/i-heart-food 2018/my-cat 
```

Enter fullscreen mode Exit fullscreen mode

这将创建三个文件夹，它们都整齐地嵌套在 2018 文件夹下。“太好了！”，我们说，我们去做 2019:

```
$ mkdir 2019/tim-bday-bash 2019/vegas-trip 2019/my-cat 2019/i-heart-food
mkdir: cannot create directory ‘2019/tim-bday-bash’: No such file or directory
mkdir: cannot create directory ‘2019/vegas-trip’: No such file or directory
mkdir: cannot create directory ‘2019/my-cat’: No such file or directory
mkdir: cannot create directory ‘2019/i-heart-food’: No such file or directory 
```

Enter fullscreen mode Exit fullscreen mode

哦哦...因为我们从未创建父 2019 文件夹，所以它不允许我们构建其余的目录。尽管我们可以智取它；`mkdir`有一个参数选项`-p`，它让我们能够在同一个调用中创建父目录:

```
$ mkdir -p 2019/tim-bday-bash 2019/vegas-trip 2019/my-cat 2019/i-heart-food 
```

Enter fullscreen mode Exit fullscreen mode

一切都好。再确认一下:

```
$ ls 2019
tim-bday-bash/  vegas-trip/     my-cat/         i-heart-food/ 
```

Enter fullscreen mode Exit fullscreen mode

### 将文件移入

所以现在我们在照片目录中有了我们的目录结构，有一船随机的 JPG 文件。我们如何把他们组织起来？看起来他们中的许多人都在相同的日期左右(看看我们在那里的所有 10262018_xxxx 文件)。让我们看一看其中的一两个来获取样本，以确保它是正确的事件。

> 注意:这一步会因平台的不同而有所不同，所以我将两者都给出。

```
# OSX example
$ open 10262018_0001.JPG

# Linux example
$ xdg-open 10262018_0001.JPG 
```

Enter fullscreen mode Exit fullscreen mode

这两个命令做同样的事情——它们要求当前环境找出谁是打开 JPG 文件的最佳人选，然后执行该程序。理想情况下，您会看到一个加载了您的图片的窗口。

[![miles](img/8a67120c04085fa8732014d08ae9e56d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EHfgM8m8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.ballpointcarrot.nimg/miles.jpg)

哦，看，这是一张猫的照片。我们知道这些应该放在哪里。我准备假设这个时间段前后的 300+照片都是猫照(不要评判)。我们将继续使用`mv`命令。然而，我们将利用我们的朋友通配符，让所有的文件自己移动:
，而不是一个接一个地单独移动文件(这将花费*永远*

```
$ mv 10262018_* 2018/my-cat 
```

Enter fullscreen mode Exit fullscreen mode

同样，我们没有得到任何输出，所以让我们仔细检查:

```
$ ls 2018/my-cat
10262018_0001.JPG  10262018_0035.JPG  10262018_0069.JPG  10262018_0103.JPG  10262018_0137.JPG  10262018_0171.JPG  10262018_0205.JPG  10262018_0239.JPG  10262018_0273.JPG  10262018_0307.JPG
10262018_0002.JPG  10262018_0036.JPG  10262018_0070.JPG  10262018_0104.JPG  10262018_0138.JPG  10262018_0172.JPG  10262018_0206.JPG  10262018_0240.JPG  10262018_0274.JPG  10262018_0308.JPG
10262018_0003.JPG  10262018_0037.JPG  10262018_0071.JPG  10262018_0105.JPG  10262018_0139.JPG  10262018_0173.JPG  10262018_0207.JPG  10262018_0241.JPG  10262018_0275.JPG  10262018_0309.JPG
10262018_0004.JPG  10262018_0038.JPG  10262018_0072.JPG  10262018_0106.JPG  10262018_0140.JPG  10262018_0174.JPG  10262018_0208.JPG  10262018_0242.JPG  10262018_0276.JPG  10262018_0310.JPG
... 
```

Enter fullscreen mode Exit fullscreen mode

太好了——我们所有的猫咪照片现在都整理好了。

我先把我们留在这里。我想让这成为一个连续的系列文章，所以如果你对某个命令感兴趣，请告诉我。我知道这对于许多开发人员来说可能有点太基础了，但是嘿，每个人都必须从某个地方开始，对吗？只要有人利用，我就开心。
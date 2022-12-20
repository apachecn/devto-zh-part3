# 在 Sublime 文本上使用 Anaconda(不安装任何包)

> 原文：<https://dev.to/abdurrahmaanj/using-anaconda-on-sublime-text-39le>

[![Alt text of image](img/35883ece80573cb0ed528f370598b850.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wj-A8tye--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2jgf1hcnqm3xjmfk2yii.png)

在 sublime text 上运行 python 一直是我的梦想。但是一个更大的梦想是在崇高的文本上运行 Anaconda。这里是你如何在 windows 上做到这一点！

## 第一步:理解崇高的文本构建

默认情况下，崇高文本提供构件选项。如果你安装 python，它会尝试获取可执行文件的路径，从这个意义上来说，它是相当智能的。

为了检查 python 如何配置构建系统，让我们尝试构建一个新的构建系统

[![Alt text of image](img/8f26a4d587b3461994a408c42db84812.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aUjfh0R9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pythonmembers.club/wp-content/uploads/2019/02/newbuild.png)

将打开一个包含一些配置的新文件

[![Alt text of image](img/a2a581eccd1a211004e1cd486743a697.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mSKrCh2---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pythonmembers.club/wp-content/uploads/2019/02/newbuild2.png)

但是让我们试着拯救它，看看它如何拯救它，在哪里拯救它

[![Alt text of image](img/c92c63f686c7d5b9fbc64cf850de964f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ON9zzlzM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pythonmembers.club/wp-content/uploads/2019/02/newbuild3.png)

现在让我们打开一个原始的构建文件

[![Alt text of image](img/700b0b0851f91f7d37eeb3a110d4e5f9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_QGt_jti--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pythonmembers.club/wp-content/uploads/2019/02/newbuild5.png)

我们看到它需要可执行文件的路径

## 第二步:查找 Anaconda 可执行文件

我们必须找到蟒蛇的可执行文件，但不能找到任何 python.exe。要发现这一点，我们可以使用 anaconda 命令提示符

[![Alt text of image](img/367b4294d675aaafa82f4befd53e8aca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wIUW9ymj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pythonmembers.club/wp-content/uploads/2019/02/newbuild6.png)

我们键入以下内容

[![Alt text of image](img/da7350f78f7041b7dd9607bedb4a62a8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--S6GWBxyq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pythonmembers.club/wp-content/uploads/2019/02/newbuild8.png)

接下来，我们转到文件夹，找到 python 可执行文件的名称

[![Alt text of image](img/fdc97883370e634c39d4e93690152e1b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1hdCGaEc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pythonmembers.club/wp-content/uploads/2019/02/newbuild9.png)

然后我们构建一个新的包，配置它，然后保存它

[![Alt text of image](img/11843510f48a350281df2fb112aac847.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XqUvXySB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pythonmembers.club/wp-content/uploads/2019/02/newbuild10.png)

下次我们在菜单上选择时

[![Alt text of image](img/5862708a572970db7ac4c59d69b68453.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--b9FJhQj0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pythonmembers.club/wp-content/uploads/2019/02/newbuild11.png)

然后控制+ b 运行

[![Alt text of image](img/a1db1d7870e4592dae354a4d40571780.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OwoqrVb4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pythonmembers.club/wp-content/uploads/2019/02/newbuild12.png)

接下来，我们感觉到力量在我们的血管中流动++ *++* ++__++

注意:这个帖子流量很大。如果你在的话，作者会很感激的！
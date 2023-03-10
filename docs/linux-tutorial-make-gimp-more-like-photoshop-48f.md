# Linux 教程:让 GIMP 更像 Photoshop

> 原文：<https://dev.to/iamtravisw/linux-tutorial-make-gimp-more-like-photoshop-48f>

[![Linux Tutorial: Make GIMP more like Photoshop](img/9975fbbeba0516452b9bbae1aeb134b3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--K19Q2cD9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://iamtravisw.com/conteimg/2019/04/Screenshot-2018-10-23-at-4.11.21-PM.png)

下面的说明很大程度上受到了 Udemy 的 Bernard t Hooft 的启发。我在这里找到了他的 Windows 7、8 和 10 课程的说明:[https://www.udemy.com/make-gimp-work-as-photoshop](https://www.udemy.com/make-gimp-work-as-photoshop/)。我知道许多 Linux 用户喜欢 GIMP，但可能仍然缺少那种“Photoshop 的感觉”,所以我想分享如何在运行 Linux 的机器上使用他的教程。

以下是本教程的最终结果:

[![Linux Tutorial: Make GIMP more like Photoshop](img/87f1fcd3bb70645b514f28237526638f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EWQ_tvMe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://1.bp.blogspot.com/-JostZyyyR-Q/W8-PoPlQSTI/AAAAAAAAI0A/vrgBJr5J7EQSrvXKvhnZCZ8nwNLkBBXTQCLcBGAs/s640/Screenshot%252B2018-10-23%252Bat%252B2.49.56%252BPM.png)

先决条件:

#### 你应该已经安装了 flatpak，你需要它来在 Linux 上安装 GIMP 2.10。

运行以下命令:

sudo apt-get 更新

sudo apt-get 安装 flatpak

### 1。安装 GIMP 2.10.X(撰写本文时是 2.10.6)

**运行以下命令:**

sudo flatpak 安装[https://flathub.org/repo/appstream/org.gimp.GIMP.flatpakref](https://flathub.org/repo/appstream/org.gimp.GIMP.flatpakref)

注:写这篇文章的时候，**sudo apt-get install****GIMP**还在返回 GIMP 2.8(GIMP 的老版本。本教程针对 2.10.6)

### 2。安装插件

我们将安装以下两个插件以及其他一些很酷的东西:

*   再合成器(内容感知填充)

*   通过复制分层

**2a。下载。伯纳德的课程的压缩文件在这里:**

[https://www . udemy . com/make-gimp-work-as-Photoshop/learn/v4/t/lecture/12014 132？start=0\](https://www.udemy.com/make-gimp-work-as-photoshop/learn/v4/t/lecture/12014132?start=0%5C)

注意:我认为您需要注册才能查看这些文件。这是一门很棒的课程，超出了我在这里教的内容。主要区别是我的教程是针对 Linux 用户的。您想要第 1 部分第 3 部分“安装插件”中的 Udemy 文件。

**2b。确保 GIMP 已关闭。**

**2c。导航到您的 GIMP 文件夹。**对我来说，在[克罗斯蒂尼](https://www.reddit.com/r/crostini)是在以下地点:

CD 上。var/app/org . gimp . gimp/config/gimp/

如果您使用的是不同版本的 Linux，或者安装了更加定制化的版本，那么它可能会有所不同。需要指出的是，如果你之前安装了 GIMP，你会有一个 GIMP 2.8 文件夹。这不是您的 2.10 插件的正确文件夹。您可能需要稍微浏览一下，以找到正确的文件夹。你可以使用 **ls -a** 命令来查看你隐藏的文件夹。

**2d。(可选但推荐)备份当前设置。**

**运行以下命令:**

CP-R 2.10 2.10-备份

**2e。将 Udemy 文件夹中的所有内容复制并粘贴到您的 2.10 文件夹中。**您需要知道您的完整目录名，因此打开一个新的终端窗口并键入' **pwd** '。我的是 **/home/iamtravisw/** ，所以你的可能是 **/home/YOURNAME/** 。一旦有了这些，运行以下命令将您的内容复制到 2.10 文件夹:

**运行以下命令:**

cp -a /home/YOURNAME/Udemy/。/home/YOURNAME/。var/app/org . GIMP . GIMP/config/GIMP/2.10

**3。打开 GIMP 并保存首选项。**

如果你像我一样来自 Photoshop，那么现在 GIMP 应该看起来更熟悉了。

**3a。你需要点击最大化图标让 GIMP 全屏显示，然后进入编辑>偏好菜单。点击“窗口管理”，然后点击“立即保存窗口位置”。**

定制 GIMP 还有很多其他方式，这只是冰山一角。我自己也是一个 GIMP 新手，但是我在本教程开始时提到的 Udemy 课程包含了很多额外的信息，比如定制 GIMP 的用户界面，使之更像 Photoshop。我建议观看伯纳德提供的节目，因为它可能很有教育意义，而且正是你所需要的。现在您已经掌握了将他的更改应用到您的 GIMP Linux 版本的技能和知识，您应该能够模仿他在 Windows 上的操作，但是是在 Linux 上。

Bernard 't Hooft 已经慷慨地提出让我博客的读者访问他的新课程，GIMP 2.10 终极指南。[点击这里，你可以免费进入 199 美元的课程。](https://www.udemy.com/the-ultimate-gimp-guide-amazon-book-included/?couponCode=FREEGIMP210)

编辑愉快！

特拉维斯·w。
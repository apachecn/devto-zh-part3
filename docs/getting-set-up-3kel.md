# Win95 病毒开发:设置

> 原文：<https://dev.to/cpu/getting-set-up-3kel>

病毒编写从开发环境开始。下面是我如何设置一个 Windows 95 虚拟机和我的开发工具。如果这是你第一次访问 VeXation，你可能想看看介绍这个项目的[“欢迎”帖子。](https://dev.to/cpu/vexation---lets-write-a-virus-51e0)

在走得太远之前，我认为回到 1995 年的心态是有价值的:库里奥的[黑帮天堂](https://www.youtube.com/watch?v=N6voHeEa3ig)是[排名第一的单曲](https://en.wikipedia.org/wiki/Billboard_Year-End_Hot_100_singles_of_1995)。魁北克[勉强仍然是加拿大的一个省。pog 系列](https://en.wikipedia.org/wiki/1995_Quebec_referendum)[仍然很酷](https://www.complex.com/style/the-rarest-pogs-from-the-90s/)。网景公司最近才发布 SSL，并面临着来自一种叫做 Internet Explorer 的全新网络浏览器的挑战。一台顶级的家用电脑类似于一台 [486，配有 1 GB 硬盘和 8mb 内存](http://www.relativelyinteresting.com/wp-content/uploads/2012/02/falll+1995+computer.jpg)。

相当狂野！有了正确的思维模式，让我们来试试这个新的 Windows 95 吧。

<figure>

[![WINDOWS 95](img/cc668843aa83b76fcbae6536e02b71ff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HaGWzK2J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.amz.mshcdn.com/_iqL72wuOYEk9f2yjF1EqABqtMk%3D/http%253A%252F%252Fa.amz.mshcdn.com%252Fwp-content%252Fuploads%252F2015%252F08%252Fwindows95small-6.jpg)

<figcaption>Get hype, we're installing Windows 95</figcaption>

</figure>

## 软件选择

### 虚拟化

我在 Linux 上写这篇文章，并将使用 [VirtualBox](https://www.virtualbox.org/) 进行虚拟化。有一些[古怪的新方法](https://github.com/felixrieseberg/windows95)将 Windows95 作为电子应用程序运行，但 **VirtualBox** 是我所知道的恶魔。理论上，这种设置大部分可以适用于 macOS 或现代 Windows，但你必须自己尝试。对我来说幸运的是，用 VirtualBox 安装 Windows 95 所涉及的大部分艰苦工作都包含在 Socket3 关于这个主题的博客文章中。为了避免重复这项工作，我将遵从这篇文章的基本设置说明，只指出不同的地方。

### Windows 95 版本

Windows 95 有多种版本可供选择。为了简单起见，我选择使用与 Socket3: **Windows 95 OSR 2.1** 相同的版本。稍后，我将在几个版本上测试代码，以确保补丁级别的差异不会破坏东西。

### 汇编器/连接器/调试器

我不知道这是不是真的，但我得到的印象是，90 年代编写 ASM 恶意软件的流行选择是[微软宏汇编器](https://en.wikipedia.org/wiki/Microsoft_Macro_Assembler)(几乎总是被称为 MASM)。为了增加一点趣味，我决定尝试使用 Borland [Turbo Assembler](https://en.wikipedia.org/wiki/Turbo_Assembler) (几乎总是被称为 TASM)会很有趣。我最终选择了 **Borland Turbo Assembler 5.0** 。它易于安装并支持 32 位 Win32 开发。TASM 有相当广泛的 MASM 兼容性，所以这是一个不错的决定。如果其他方法都失败了，大声说出“Borland”是很有趣的(我建议你试试)。

<figure>

[![Borland Turbo Assembler 5.0](img/d4e523619fe1ebe377cac3834c52d155.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JAghE0X1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://2.bp.blogspot.com/-iJNWKlpby3I/XDTT73eAuRI/AAAAAAAAG9M/-fYns4i0RQUAxkCZUi80BvHxJBR9Eos7wCLcBGAs/s1600/tasm5.jpg)

<figcaption>Are you ready to pinch individual MOV instructions? I sure am!</figcaption>

</figure>

### 文本编辑器

这一部分是真正的斗争。我试了几个我记得的随机的“程序员的文本编辑器”(Notepad++和 UltraEdit 等)，但没有一个能安装在新的 Windows 95 OSR 2.1 系统上。这可能是因为我用的是新版本的 Win98+,但挖掘旧的安装程序来尝试是很乏味的。最终我选择了一个古老的免费软件程序，叫做[程序员文件编辑器](https://www.lancaster.ac.uk/~steveb/cpaap/pfe/) (PFE)。与现代的 IDE 相比，它的功能有些贫乏，但确实如此...“正宗”。使用**程序员的文件编辑器**确实提供了 notepad.exe 缺少的重要功能，比如行号和能够打开 64KB 的文件>。最棒的是 PFE 的[T5 可能 Y2K 安全](https://www.lancaster.ac.uk/~steveb/cpaap/pfe/year2000.htm)。

<figure>

[![PFE logo](img/69c089e842327296bdb255b78be68246.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SzEMcLKI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://www.lancaster.ac.uk/%257Esteveb/cpaap/pfe/pfelogo.gif)

<figcaption>The brushed steel effect is how you know we're going to be working close to the metal.</figcaption>

</figure>

## 安装 Windows 95 OSR 2.1

为了在 VirtualBox 中安装 Win95，我关注了 Socket3 的博客文章。如果您正在跟进，您将需要下载以下内容:

1.  [Windows 95 OSR 2.1 OEM CDROM ISO](https://winworldpc.com/product/windows-95/osr-21)
2.  [Windows 95 启动盘软盘镜像](https://drive.google.com/open?id=0B2BOLB8yC3mnTWtwSVk0X0NpZEE)
3.  [赛特显示医生 7.0 测试版](https://drive.google.com/open?id=0B2BOLB8yC3mnbUg3YTVYd0JlU1U)

您还需要一个有效的 Win95“真品证书”序列号。这些在网上到处都是，但我会给你留一个谷歌并分享我用过的那个(*如果你是警察就别读这个* ): `24796-OEM-0014736-66386`

我选择使用一些不符合那个时代的虚拟机设置，让生活变得更容易忍受一些。我创建了一个带有奔腾处理器、64mb 内存和 2GB 磁盘的虚拟机。不要忘记在 CPU 设置中禁用 VT-x/AMD-V 和嵌套分页。

使用软盘映像来引导 CDROM 驱动程序，以便能够运行 Win95 安装程序光盘，这无疑是怀旧的。与 1995 年相比，VM 的总体安装时间非常快，当时通常需要一个小时。

<figure>

[![BANANA CD DRIVER](img/c757bea69d0cee551fe4764da930b3c0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--G76l3aI1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://binaryparadox.net/d/2b224afd-0349-4712-be3b-6c5ca85d8184.jpg)

<figcaption>BANANA powered CD-ROM</figcaption>

</figure>

Socket3 的指令如描述的那样工作，除了一些我必须适应的小事情:

1.  CDROM 驱动器没有默认为驱动器`R:\` -相反，它是我所期望的驱动器`D:\`。我用`D:\`代替了所有提到`R:\`的地方。我也跳过了编辑`autoexec.bat`来重命名光驱。尽管它是驱动器`D:\`,但在开始安装过程之前，我必须按照描述的过程将`*.cab`文件从 CDROM 复制到硬盘，否则麻烦就会接踵而至。
2.  在 Socket3 的 post 中，将 CDROM 文件复制到驱动器`C:\`的过程在准备驱动器`C:\`的`fdisk`和格式化过程之前描述。我必须颠倒这个顺序，并在复制之前准备好驱动器。记得在分区和格式化后重启虚拟机。

<figure>

[![Win95 installer](img/3f90315d23da10c8ff33d4cdc3d17e6a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ULHi4Ozn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://binaryparadox.net/d/573de904-9958-4c22-b5e0-164228e7ef86.jpg)

<figcaption>I'm having Fun™ already</figcaption>

</figure>

安装和配置有点粗糙的 SciTech Display Doctor 驱动程序有点复杂，但提高屏幕分辨率是值得的。我必须在 SciTech 应用程序界面上拖动一点，才能看到我需要单击的“应用”按钮，以根据 Socket3 的指示更改显示和图形驱动程序。

遵循网络设置说明对我来说很重要，因为我知道我会想要将文件从我的主机复制到 VM(安装程序、山羊文件等)。如果您想这样做，请确保在安装 TCP/IP 协议后不要忘记重新安装“Microsoft 网络客户端”,因为当您第一次删除 NetBEUI 和 IPX/SPX 时，它将被删除。我将虚拟机放在一个没有 DHCP 服务器的网络上，因此我还必须手动配置系统 IP 地址、网关和 DNS 配置。习惯重启你的虚拟机，因为在 Windows 95 中*几乎所有的事情*都需要重启:安装新的驱动程序，改变屏幕分辨率，改变系统 IP 地址，你能想到的。

需要指出的重要一点是，Socket3 的虚拟机联网方法将虚拟机连接到您的主机的网络适配器，这意味着**您可能会有效地将 Windows 95 机器放在巨大的恐怖互联网上，除非您非常小心**。我放弃了使用 VirtualBox NAT，所以最终当我想发送/接收文件时，我通过有策略地连接/断开网络适配器虚拟电缆来“隔离”虚拟机。在试图开发一种病毒的同时却感染了一种病毒，这种讽刺对我来说太难以忍受了。

## 文件共享

Windows95 没有附加的 VirtualBox 来宾操作系统，这意味着我不能使用传统的方法在主机和来宾操作系统之间共享文件。我尝试使用各种技巧将虚拟机的 FAT32 磁盘映像直接挂载到 Linux 中，但发现这不可靠且令人讨厌，因为必须首先关闭 guest 虚拟机。开箱即用的 Win95 OSR 2.1 有 IE 4.0，所以浏览网页下载工具是一场噩梦。几乎没有任何网站可以运行，你几乎肯定会比万豪酒店更容易被黑。我能找到的最好的解决方案是在 VM 中启用 Windows 文件共享，而不会迷失在 yak shaving 中。我在我的主机上使用一个 Linux samba 客户机与 VM 共享文件夹进行交互。

文件共享不是开箱即用的，所以在配置 TCP/IP 并再次连接虚拟电缆(请不要黑我)后，我启用并配置了它。您自己也可以这样做:

1.  右键单击网上邻居，然后选择属性
2.  点击标有“文件和打印共享”的丑陋按钮
3.  单击“我希望允许其他人访问我的文件”
4.  单击确定
5.  如果已经取出，请重新插入 Windows 95 安装光盘
6.  单击“是”重新启动计算机(当然，为此您必须重新启动计算机)
7.  在 C:\驱动器中创建一个名为“portal”的文件夹
8.  右键单击文件夹，然后选择属性
9.  单击“共享”选项卡
10.  为文件夹配置共享名称和访问类型
11.  我发现从我的主机使用命令行 SMB 客户端访问虚拟机上的共享文件夹最容易，不出所料，这个客户端叫做`smbclient`。要下载门户目录中某些文件的 TAR 归档文件，只需运行:

```
smbclient //VMNAME/PORTAL "" -N -Tc backup.$(date +%Y-%m-%d-%s).tar <files> 
```

在互动模式下四处闲逛甚至更容易:

```
smbclient //VMNAME/PORTAL "" 
```

smbclient 手册页有更多的信息。完成后，记得断开虚拟网络电缆。还要注意，上述命令中的`"VMNAME"`是一个占位符，代表我在 Windows 文件共享设置中选择用来标识计算机的名称，而不是 VirtualBox 虚拟机名称。

<figure>

[![Enabling file and print sharing](img/5d64007776b8764454bde5fa221d396f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Gp3R5tlp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://binaryparadox.net/d/fbc50f46-8a27-4403-a37b-49b057bb746e.jpg)

<figcaption>Enabling file and print sharing</figcaption>

</figure>

<figure>

[![Enabling file and print sharing pt2](img/ef73c7ce64efb6551df9322f366f3367.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4Bdo2hmz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://binaryparadox.net/d/86d6321a-d7bf-47b3-a768-1ac0d08d596a.jpg)

<figcaption>Enabling file and print sharing pt2</figcaption>

</figure>

<figure>

[![Configuring a file share](img/c4ee7e1de5b4cc21d4fe0b2da587e3e8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ySrBZePg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://binaryparadox.net/d/56ec70d0-ecd5-413d-9fa7-971dabc476d1.jpg)

<figcaption>Configuring a file share</figcaption>

</figure>

## 安装 Borland Turbo 汇编程序 5.0

总的来说，这是一个直截了当的过程。首先，我必须下载 Borland Turbo Assembler 5.0 安装程序的三个软盘映像。你可以在 Win32World 上找到这些作为 [7z 存档](https://winworldpc.com/product/turbo-assembler/5x)。如果您继续下去，您将需要一种方法来解压缩 7z 文件，以获得可以使用 VirtualBox 挂载为虚拟机的虚拟软盘的单个磁盘映像。

将`disk01.img`安装到虚拟软盘驱动器后，我就可以开始安装了。一种方法是:

1.  打开我的电脑
2.  双击`A:\`驱动
3.  双击“安装”图标
4.  根据需要按 enter 键继续
5.  保留所有默认设置，并选择开始安装
6.  出现提示时，挂载磁盘#2 和磁盘#3
7.  当显示`TSM_RDME.TXT`时，安装完成。按 ESC 键退出。1.在重新启动虚拟机之前，不要忘记弹出软盘，否则下一次启动将尝试从 Borland 软盘启动并失败。

<figure>

[![Turbo Assembler readme](img/3285c1c7e5c2ffeb79bdfecd0ffdb2ca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Hnurli0h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://binaryparadox.net/d/801d681b-955f-435e-a931-f6ed0947f504.jpg)

<figcaption>T-t-t-t-turboooooo</figcaption>

</figure>

接下来，我必须将 TASM 工具添加到`%PATH%`环境变量中，这样我就可以在没有完全限定路径的情况下从命令提示符下轻松使用这些工具。对于 Win95，这意味着编辑`Autoexec.bat`脚本。一种方法是:

1.  打开我的电脑
2.  打开`C:\`驱动
3.  右键单击“Autoexec ”,然后选择“编辑”
4.  该文件可能是空的，因为这是全新的安装。我添加了下面一行:`Set PATH=%PATH%;C:\TASM\BIN`
5.  保存文件并退出
6.  重启(当然)

通过构建 TASM 附带的一个 Win32 示例应用程序，我能够验证我有一个正常工作的安装，并对这些工具有了一些基本的了解。为此:

1.  打开一个命令提示符(我最喜欢的方法是按住 windows 键，按“r”，输入`command`，然后按 enter)
2.  通过运行`cd C:\TASM\EXAMPLES\WAP32`切换到“wap32”示例目录，通过运行`make`构建示例应用程序。如果你得到错误“错误的命令或文件名”，你应该仔细检查你的`%PATH%`设置是否正确。预计会看到两条关于堆和栈保留空间大小的警告消息(向 Borland dev 喊出，它提供了带有警告的示例代码...).
3.  通过在控制台中运行`wap32`来运行构建的应用程序
4.  您应该会看到一个难看的 win32 应用程序窗口出现
5.  您可以通过运行`td32 wap32`来尝试调试应用程序。因为我们构建的示例应用程序没有调试符号，所以调试器会警告我们这个事实，并且只显示反汇编的机器码
6.  你可以通过运行`make -B -DDEBUG`来尝试用符号调试**。即使源代码没有改变,`-B`也会强制进行重建。`-DDEBUG`向`Makefile`传递一个`DEBUG`参数，这样它就可以改变汇编器和链接器命令行标志**
7.  在用调试符号重新构建之后，你可以再次运行`td32 wap32`,它不会再警告你缺少调试符号，而是显示一个源代码列表。好多了！

<figure>

[![build output](img/84f34b20260ae4be080932757c33df71.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6ORjUhZZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://binaryparadox.net/d/29359034-6485-44a1-a90e-367ec98066c0.jpg)

<figcaption>Building wap32 example without debug symbols.</figcaption>

</figure>

<figure>

[![wap32](img/a1688587d5047e24d26849968d1b3fac.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qnlRob7I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://binaryparadox.net/d/c877401e-571f-4793-9564-e7e748e98298.jpg)

<figcaption>wap32 in all its glory.</figcaption>

</figure>

<figure>

[![debugging without symbols](img/9776935b345b735b11369678acc522c0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5af_7TUt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://binaryparadox.net/d/762f8917-126f-45a8-b449-76f38e67ff38.jpg)

<figcaption>Debugging wap32 without debug symbols</figcaption>

</figure>

<figure>

[![debugging with symbols](img/7be66803582e74191bf44cbd33aa0357.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kfKIvkrL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://binaryparadox.net/d/ca93fbda-7c5f-47b0-b8b6-c29e2b41e8e2.jpg)

<figcaption>Debugging wap32 with debug symbols</figcaption>

</figure>

## 包装完毕

酷！一个真实的开发环境。如果您遵循了我的建议，我建议您在此时创建一个 VM 状态的快照，这样无论事情变得多么糟糕，您都可以返回到一个全新的设置。复制这些设置指令来制作更多的 Windows 95 虚拟机也是有用的，您可以使用这些虚拟机来测试您的病毒，而不会感染您的开发机器。

<figure>

[![full dev env](img/b54640016d1019f4455d5059b5dd2950.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Z4WVYpUl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://binaryparadox.net/d/eed52fa2-fb4d-420f-8ead-c538db1a6d00.jpg)

<figcaption>A full development environment in action</figcaption>

</figure>

**额外的事实:**如果你读过[杨奇煜·桑格拉德](https://fabiensanglard.net/)的优秀的[游戏引擎黑皮书:Wolfenstein 3D](http://fabiensanglard.net/gebbwolf3d/index.html) 你可能会注意到 td32 调试器界面看起来就像第三章中显示的那样。约翰·卡马克和 id 软件公司使用 Borland Turbo C++开发 Wolf 3D，它与 Borland Turbo Debugger 捆绑在一起(尽管我们已经安装了一个更新的版本)。

在这一点上，我花了大部分时间阅读 Borland Turbo Assembler 5.0 手册和浏览 WAP32 源代码。这是在 x86 汇编中进行 win32 编程的一个很好的开始示例。一旦我掌握了 WAP32，我就开始尝试一些 [Iczelion 的教程](http://win32assembly.programminghorizon.com/tutorials.html)。这些公司大多设在 MASM，但与 TASM 合作时很少大惊小怪。与 2019 年版的开发教程和 medium dot com 思考片相比，我发现这些旧的“社区风格”教程非常可爱，即使不总是非常清晰。

您可能会惊讶地看到“make”命令出现在 Win95 开发环境中。我承认我是。最初我以为它会是类似 GNU Make 的东西，我肯定会让自己失望。取而代之的是某种专有的 Borland 风格的 Make,它缺少了很多我认为是 GNU Make 的东西。这需要一些时间来适应，尤其是当搭配糟糕的 command.exe 壳牌 Win95 优惠。总的来说，这比编写 BAT 文件要好(至少对我来说)。

## 即将到来

准备好 dev env 之后，我可以继续讨论更有趣的话题了。下次我想谈谈 PE 感染者背后的理论，以及与标准应用程序开发相比，我们将面临的一些挑战。

我很想听到关于这个项目的反馈，特别是如果你是这个时代写汇编代码的人。请随时在推特( [@cpu](https://twitter.com/cpu) )或电子邮件(【daniel@binaryparadox.net】T2)上给我留言。

直到下一次，
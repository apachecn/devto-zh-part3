# 如何在 u 盘上使用 macOS Boot Camp

> 原文：<https://dev.to/fundatillus/how-to-use-macos-boot-camp-on-a-usb-stick-1m8>

> 最初发布于 2016 年 7 月 22 日

### 我的装备:

1.  13 英寸 MacBook Pro，2015 年初。
2.  VMWare Fusion 8 中的 Windows 10 EDU。
3.  128GB SanDisk UltraFit USB 3.0(新兵训练营的最终目的地)。
4.  希捷 750GB 固态硬盘/硬盘混合在一个 USB 外壳。

### 你需要什么:

1.  Windows ISO(我用的是 Windows 10，但应该也能用 7、8、8.1)。您也可以使用 Windows 安装光盘。
2.  [gimagex](https://www.autoitscript.com/site/autoit-tools/gimagex/) 。
3.  现有的 Windows 7、8、8.1 或 10 64 位安装，物理机或虚拟机。
4.  一个 USB 硬盘和一个 u 盘(是的，两个都有)。
5.  当然是你的苹果电脑。

### 步骤总结:

1.  对 USB 硬盘进行分区。
2.  将`install.wim`图像写入 USB 硬盘。
3.  使 USB 硬盘可引导。
4.  使用`dd`将 USB 硬盘内容克隆到 u 盘。

### 1。对 USB 硬盘进行分区

*   将 USB 硬盘插入 Windows 机器并启动`Command Prompt (Admin)`。
*   键入`diskpart`启动 DISKPART 实用程序。
*   键入`list disk`查看连接的磁盘列表。记下与您的 USB 硬盘相关的磁盘号。我把它称为 x。
*   键入`select disk X`，然后键入`list disk`并确认正确磁盘旁边的星号。

    > **警告**以下步骤将擦除 USB 硬盘上的所有数据:

*   类型`clean`。

*   键入`convert mbr`创建主引导记录(MBR)分区表。我没有得到一个可以和 u 盘一起使用的 GPT 磁盘。

*   要创建一个 200MB 的 EFI 分区，请键入`make partition primary size=200`。

*   通过键入`format fs=FAT32 label=EFI quick`格式化 EFI 分区。

*   给分区分配一个驱动器号:`assign`。

*   通过键入`active`使分区可引导。

*   为 Windows 安装创建分区:`create partition primary size=120000`。

> 如果您的能力很强，您可以精确地调整分区大小以匹配目标 u 盘大小。克隆过程是漫长的，如果我搞砸了，我讨厌再次等待，所以我选择保持分区小一点。我用的是 128GB 的 u 盘，但从来没有“满”的容量，所以我把分区大小定为 120GB，即 120000MB。
> 
> 我还尝试使用 64GB 的分区，这样我可以在 u 盘上放一个 Linux 发行版。因为它是一个 u 盘，而且必须是一个 MBR，所以我没有成功地将 Linux 发行版添加到 u 盘中。重点是，节省空间是不值得的；只需将主 Windows 分区设为 u 盘大小即可。同样值得注意的是，在后面的步骤中，将分区方案克隆到 u 盘后，您将无法更改分区方案。

*   格式化主分区:`format fs=ntfs label=Windows quick`。
*   给分区分配一个驱动器号:`assign`。
*   现在，您应该有两个分配了驱动器号的分区。

### 2。将 Windows 映像写入 USB 硬盘

*   将您选择的 Windows 版本下载到您将要使用的 Windows 计算机上。
*   下载 [gimagex](https://www.autoitscript.com/site/autoit-tools/gimagex/) 。也有官方的 Windows 命令行工具，但是这个是 GUI。
*   双击 ISO 来挂载它。Windows 将为其分配一个驱动器号。
*   打开 gimagex 并选择`Apply`选项卡。
*   在源字段中，选择装载的 ISO 并导航至`sources\install.wim`。
*   在目标字段中，选择 USB 硬盘上的 Windows 分区。
*   按下图像字段正对面的选择按钮，选择您想要的图像。

> 根据安装介质的不同，您可以安装多个版本的 Windows:家庭版、专业版、企业版或 32 或 64 位版本的 EDU。请确定您的 Mac 选择的是 64 位版本。

*   按下应用按钮。

> 根据您的计算机，应用过程可能需要 15 分钟到几个小时。我的通常需要 20-30 分钟。

*   在您等待应用过程完成时，通过打开 Mac 上的 Boot Camp 助理并从操作菜单中选择`Download Windows Support Software`来下载 Boot Camp Windows 支持软件。

> 您可以将支持软件复制到单独的 u 盘(不是您用于 Boot Camp 的 u 盘),或者您可以将软件复制到 Windows 计算机，并在应用过程完成后将其放在您的`Public Downloads`文件夹中。如果你正在阅读这篇教程，你可能已经足够聪明，知道如何做到这一点。

### 3。使 USB 硬盘可引导

*   一旦图像被应用到 USB 硬盘，启动`Command Prompt (Admin)`。
*   键入`X:`,其中 X 是刚刚应用 Windows 映像的驱动器。
*   类型`cd Windows\system32`。
*   键入`bcdboot.exe X:\Windows /s Y: /f ALL`其中:
    *   x 是 USB 硬盘上 Windows 分区的驱动器号；
    *   y:是 USB 硬盘上 EFI 分区的驱动器号。

> 我在 bcdboot 将 EFI 文件夹放在 Windows 分区而不是 EFI 分区时遇到了一些问题。一旦 bcdboot 完成了它的工作，您就可以将 EFI 文件夹从 Windows 分区复制到 EFI 分区。由于分配给 EFI 文件夹的权限，您可能无法将其从 Windows 分区中删除。我对它留在那里没有任何意见。

此时，您有了一个外部 USB 硬盘驱动器，可以从中运行 Windows。我建议您通过执行以下操作来测试它，以确保它能够正常工作:

*   将 Windows 支持软件复制到 USB 硬盘的 Windows 分区上的公共下载文件夹(或任何其他所有用户都可以访问的位置)中。
*   从 Windows 机器中弹出驱动器，并将其插入 Mac。
*   按住 Option 键重新启动 Mac。看到`Macintosh HD`和`EFI Boot`就放开。
*   选择`EFI Boot`。
*   允许 Windows 安装。当它重新启动时，确保您按住 Option 键，以便您可以选择`EFI Boot`。
*   Windows 完成初始设置后，安装 Windows 支持软件(可选)。
*   重启以确保一切正常。

### 4。使用 dd 将 USB 硬盘内容克隆到 u 盘

*   重启你的 Mac 进入 OS X/macOS。
*   插入 u 盘。USB 硬盘和记忆棒都应该插在 Mac 上。
*   开放终端。类型`diskutil list`。
*   记下 USB 硬盘和 u 盘的磁盘编号。我将把硬盘称为 disk2，把记忆棒称为 disk3。
*   通过键入:`sudo diskutil unmountDisk /dev/disk2`和`sudo diskutil unmountDisk /dev/disk3`卸载这两个磁盘。
*   通过键入`sudo dd if=/dev/rdisk2 of=/dev/rdisk3 bs=1m count=121000`开始克隆过程。

> **解释** : dd 通常使用 512 字节的块。使用`bs=1m`将其更改为 1 兆字节。`count=`告诉 dd 要写多少块。对于我的 128GB 驱动器，我创建了一个 200MB 的 EFI 分区和一个 120000MB 的 Windows 分区，因此我已经告诉 dd 编写 121，000 个块。这确保它捕获 EFI 和 Windows 分区，以及分区表和偏移量所占用的空间。根据您的需求进行调整。
> 
> 这可能需要很长时间，具体取决于您正在写入的数据块数量。当我用 64GB 的分区做实验时，花了大约 2 个小时；对于 120GB 的，我只是设置了一下就去了海边。

dd 完成后，弹出 USB 硬盘并重启 Mac，按住 Option 键并选择`EFI Boot`。

### 结论

USB 3.0 记忆棒并不是在 Mac 上运行 Windows 的最快方式。如果您打开性能监视器，您会看到您的主 Windows 分区经常达到 100%的最大值。但是如果你的 Mac 空间有限，这将会帮你度过难关。

### 有用的资源

如今，没有人是白手起家的天才。我发现在这个过程中，这些文章特别有帮助:

*   [如何从 USB 驱动器运行 Windows 便携版](http://lifehacker.com/how-to-run-a-portable-version-of-windows-from-a-usb-dri-1565509124)
*   [把固态硬盘放在口袋里，你就能顺利通过新兵训练营](http://www.cultofmac.com/311348/visiontek-usb-pocket-ssd-review/)
*   [如何在没有企业版的情况下创建 Windows To Go USB 驱动器](http://www.howtogeek.com/196817/how-to-create-a-windows-to-go-usb-drive-without-the-enterprise-edition/)
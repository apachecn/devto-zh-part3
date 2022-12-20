# macOS 加密文件夹

> 原文：<https://dev.to/glennmen/macos-encrypted-folder-152m>

我最近发现了如何在 macOS 上创建一个加密的受密码保护的文件夹，我想这可能不是一个众所周知的功能，但与开发社区分享会很有用。

这是 macOS 已经内置的功能，所以不需要第三方工具。苹果在 macOS Mountain Lion 中首次引入了这一功能。

所以，如果你想对你的同事/家人隐藏你的“绝密文件”或你的猫咪照片，请往下读，因为这非常简单。

## 制作加密文件夹

*   您将需要使用“磁盘工具”，所以请使用 Spotlight 打开它，或者您可以在“应用程序”文件夹中找到它。
*   在“文件”菜单中，转到“新建图像”并选择“文件夹中的图像…”。![Disk Utility File menu](img/3eeaf6216533961578f27cd70d32ab7c.png)
*   您的 Finder 将会打开，在这里您可以导航到您想要加密的“绝密文件”文件夹，按 Finder 底部的“选择”。![Disk Utility File menu](img/1fd3627c1b3079e09f460114bad57202.png)
*   在以下屏幕中，您可以更改加密文件夹的名称(另存为)和位置(位置)。![Disk Utility File menu](img/80efd0a780c324b1faf5ca97087a255d.png)
*   接下来，我们要将“加密”更改为“128 位 AES 加密(推荐)”，这将打开一个新窗口，您可以在其中设置加密文件夹的密码。![Disk Utility File menu](img/4347253be13d03d658a67220fcc2f5b1.png)
*   我还建议将“图像格式”设置为“读/写”，这将使您能够在加密文件夹(一个工作目录)创建后添加新文件。![Disk Utility File menu](img/3eecd002f843ee7d6a66e31346f5128a.png)
*   如果一切顺利，您应该会看到这个屏幕。![Disk Utility File menu](img/d4e299f272e9ccf20e3c32e2be29945d.png)

**这会给你一个`.dmg`文件，可以在设备之间移动或共享，只是不要忘记密码。**

## 打开加密文件夹

打开加密文件夹甚至比创建它更容易！

*   转到您保存加密文件夹的位置。![Disk Utility File menu](img/c951efc446ec87463f3aab98f5f5a874.png)
*   双击`.dmg`文件，它会要求您输入之前创建的密码。![Disk Utility File menu](img/a2d2aa702e2276be20bcf76840337c93.png)
*   之后，它会将加密的文件夹作为挂载的磁盘添加到 Finder 的“Locations”下。![Disk Utility File menu](img/6b3694620dd9552c595332036837e4ef.png)
*   您现在可以查看、编辑、移动、添加或删除任何文件。![Disk Utility File menu](img/103ad698102dea26ccd69d67fcb70b55.png)
*   当您完成加密文件夹中的工作，并且不希望其他人能够访问它时，只需按下已安装磁盘后面的“弹出”按钮。![Disk Utility File menu](img/d1adbec4c0b6ecaf156b7dcfd67877b6.png)

**`.dmg`将作为挂载的磁盘映像工作，因此您在那里所做的任何更改都将保留在加密的文件夹中，而无需创建新的文件夹。**
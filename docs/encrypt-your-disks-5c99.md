# 加密您的磁盘

> 原文：<https://dev.to/gerbrandvd/encrypt-your-disks-5c99>

如果你有笔记本电脑，你应该*总是*加密你的硬盘。如果你还没有这样做，把它作为你的新年决心——更好的是，今天就做。

丢失笔记本电脑很糟糕，但如果你不得不担心其他人会以某种方式访问你的所有数据，那就更糟糕了。即使你有一个登录密码，并且你的所有数据都在云中，你的数据仍然可以存放在临时文件夹中。一个潜在的小偷可以访问你所有的“保存的密码”。

微软的 Windows 和苹果的 OS X，以及大多数像 Ubuntu 这样的 Linux 发行版使得磁盘加密变得非常容易——希望这是任何新笔记本电脑的默认加密。如果你安装后想加密你的磁盘，我会查阅你的操作系统文件: *[视窗](https://www.howtogeek.com/234826/how-to-enable-full-disk-encryption-on-windows-10/)* 、 *[OS X](https://support.apple.com/en-us/HT204837) 、*搜索*、T9】网页、或询问同事或家人。*

在我使用的操作系统上， [Kubuntu Linux](https://kubuntu.org) ，可以在安装时选择磁盘加密，这与 Linux 的名声相反，非常容易。我的笔记本电脑有一个 SSD 作为主磁盘，包含根目录，还有一个 2TB 的附加磁盘，用于存储所有其他不适合我的 SSD 的数据。当然也加密了。起初，我的配置要求我输入两次密码，一次是 SSD，第二次是附加磁盘。有点讨厌。多亏了这个奇妙的 howto，我有了一个几乎不安全，但更容易使用的设置: [HOWTO:用密钥文件自动解锁 LUKS 加密的驱动器](https://www.howtoforge.com/automatically-unlock-luks-encrypted-drives-with-a-keyfile)

加密密钥存储在我的主 SSD 上，因此我只需输入该 SSD 的密码。之后，存储在 SSD 上的密钥用于解密第二个磁盘。正如在 howto 中提到的，如果人们访问了你的根目录，那么一切都会丢失。
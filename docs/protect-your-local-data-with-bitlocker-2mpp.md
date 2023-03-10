# 使用 BitLocker 保护您的本地数据

> 原文：<https://dev.to/christophweigert/protect-your-local-data-with-bitlocker-2mpp>

[![Protect your local data with BitLocker](img/964fcdfe8e0bddeb9702b9be05782614.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_Z0ww5i6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://christophweigert.com/conteimg/2019/01/night-computer-hdd-hard-drive.jpg)

BitLocker 是自 Windows Vista 以来嵌入的一项功能，允许您加密硬盘。如果您的笔记本电脑或计算机被盗，您应该启用此功能来防止对您的私人数据的未授权访问。另一个优势是与 TPM(可信平台模块)的集成，它在解密数据之前验证引导和系统文件的完整性。

## 使用 BitLocker 的要求

BitLocker 仅适用于:

*   装有 TPM 1.2 或更高版本的计算机(或使用外部驱动器存储启动密钥)
*   支持的 BIOS / UEFI
*   使用 NTFS 文件系统的启动驱动器
*   带有 FAT32 (UEFI)或 NTFS (BIOS)文件系统的系统驱动器

## 设置 BitLocker

<figure>[![Protect your local data with BitLocker](img/ac5f667014c16fdc0e030f6d2b28802a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4ZGdCyRr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://christophweigert.com/conteimg/2018/12/image.png) 

<figcaption>打开 Windows 开始菜单，开始输入 BitLocker</figcaption>

</figure>

<figure>[![Protect your local data with BitLocker](img/eba789ea7961117d2bc9601810ce544e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9-qv_bSZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://christophweigert.com/conteimg/2018/12/image-1.png) 

<figcaption>点击 UAC 图标【开启 BitLocker】</figcaption>

</figure>

如果您的设备不受支持，您将看到以下消息。

[![Protect your local data with BitLocker](img/b9ca67abf7c1ce0e708df20862ea1ea7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8PcsGrxf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://christophweigert.com/conteimg/2018/12/image-2.png)

如果是这样的话，你可以买一个 TPM 模块 I，比如用下面的华硕 TPM 模块，但是要注意你的主板一定要有 TPM 头。

<figure>[![Protect your local data with BitLocker](img/57a2c2894471c53a0833ad24cf730372.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LR8-RA4j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://christophweigert.com/conteimg/2019/01/asus_tpm-m_r2.0.jpg) 

<figcaption>[华硕电脑 TPM-M R2.0](https://amzn.to/2BawYXK) 来自亚马逊</figcaption>

</figure>

安装模块后，再次打开设置对话框，并按照指示点击“打开 BitLocker”。

如果你有任何问题，请在评论区给我打电话。
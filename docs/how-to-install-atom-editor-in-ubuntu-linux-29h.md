# 如何在 Ubuntu Linux 中安装 Atom 编辑器？

> 原文：<https://dev.to/xeroxism/how-to-install-atom-editor-in-ubuntu-linux-29h>

## *21 世纪的可破解文本编辑器！*

Atom 是一个[开源](https://fossnaija.com/open-source-really-matter-control-not-code/)文本编辑器，可以满足你所有的文本需求。但是它最常用于编程和开发(编码)。一个非常实用且功能丰富的文本编辑器是开发人员工具箱中一个非常有价值的组件。

[![atom welcome page fossnaija](img/cdd9c4bd846612f9962a329283861904.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4xeawKFc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/fossnaija.com/wp-content/uploads/2019/03/atom-home-fossnaija.png%3Fw%3D665%26ssl%3D1)

Atomis 是开源的(如果你有技能，你可以在 github 上帮助改进它),而且是免费的，你可以下载，安装，然后马上开始工作。

### **特色**

*   _ **跨平台:**_ 适用于 Linux、Mac OS、Windows 操作系统。

*   _ **美学:** _atom 本身就很美，但是您可以使用不同的可用主题来修改外观和感觉，以满足您的个人品味或偏好。

*   _ **速度:** _atom 是基于 JavaScript 构建的轻量级应用程序，速度相对较快。它还可以通过内置的智能自动完成功能让你打字更快。

*   _ **可扩展:** _ 您可以使用成千上万个免费提供的包来扩展 atom 的特性，以添加额外的功能。

[![atom_editor_code_fossnaija](img/f7c3eddd3b6cc1911181fa41488eb80c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--c9WwfPVZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/fossnaija.com/wp-content/uploads/2019/03/atom_editor_code_fossnaija.png%3Fresize%3D665%252C353%26ssl%3D1)

在这篇文章中，我们将看到如何在 [Ubuntu](https://dev.to/xeroxism/new-release-ubuntu-linux-1804-long-term-support--bionic-beaver-14j8-temp-slug-9633755) [Linux](https://fossnaija.com/linux-installation-dual-booting-with-windows-7/) 中安装这个流行的现代文本编辑器。

### **方法 1:使用 PPA:**

将 PPA 添加到您的系统:

 `sudo add-apt-repository ppa:webupd8team/atom` 

更新系统软件包列表:

`sudo apt update`

现在您可以安装 atom 了:

`sudo apt install atom`

### **方法 2:–下载 Atom 包。**

从他们的网站下载 atom 安装包。

[![atom download page fossnaija](img/0481f203374c5c1f63e69ad79ea7bb61.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--X6uvrOtj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/fossnaija.com/wp-content/uploads/2019/03/atom-download-page-fossnaija.png%3Fw%3D665%26ssl%3D1)

## **使用** _ **dpkg** _ **命令:**

 `sudo dpkg -i /path/to/atom.deb` 

然后安装；

 `sudo apt-get install -f` 

或者你可以用 Ubuntu 软件中心打开这个包

要从系统中删除 atom，请使用以下命令:

 `sudo apt-remove --purge atom` 

快乐的 Linux！

**Followus on** [**脸书**](https://www.facebook.com/fossnaija)**[**Twitter**](https://twitter.com/fossnaija)**，以及**[**insta gram**](https://www.instagram.com/fossnaija/)**。****

 **帖子[如何在 Ubuntu Linux 中安装 Atom 编辑器。](https://fossnaija.com/install-atom-editor-ubuntu-linux/)最早出现在[福斯奈亚](https://fossnaija.com)上。**
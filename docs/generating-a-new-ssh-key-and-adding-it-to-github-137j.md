# 生成新的 SSH 密钥并将其添加到 Github

> 原文：<https://dev.to/maedahbatool/generating-a-new-ssh-key-and-adding-it-to-github-137j>

有时候我表现得像个疯狂怪异的书呆子。开发人员的本能总是让我学习新的东西，无论是浏览一些随机的代码，一个提示或一些开发工作流。有些概念我只是不停地往脑子里灌输，没有尝试过。但唯一的动力是，如果需要，我总有一天会去做。

每次克隆 GitHub repo 时，我都会遇到通过 SSH 克隆它的选项。我有一个关于 SSH 密钥的想法，但从未遇到过必须生成一个并将其添加到我的 GitHub 帐户中的情况。

今天，我遇到了一个场景，由于我的 GitHub repo 不能在本地克隆，我必须用 SSH 密钥连接我的 GitHub 帐户。弄清楚流程后，终于做到了，想着把自己的经历写给各位乡亲。

[![](img/a0b2348c9ebdc490ea52afcc022807cf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fVq_qTow--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://maedahbatool.com/wp-content/uploads/2019/04/SSH-Keys.jpg)

因此，如果您发现创建一个 SSH 密钥并将其与您的 GitHub 帐户连接很困难，那么这篇文章绝对适合您。

让我们从一些关于 SSH 密钥的基本概念开始。

## 什么是 SSH 密钥？

**SSH(安全外壳)**，是一种与服务器通信的加密协议，允许用户使用多种网络服务。反过来，SSH 密钥提供了一种安全的机制来抵御暴力攻击，这种攻击可能在使用密码登录服务器时发生。

在克隆 GitHub repo 时，您有时会遇到这样一个错误:

`Permission denied (publickey).
fatal: Could not read from remote repository.
Please make sure you have the correct access rights
and the repository exists.`

这需要创建一个 SSH 密钥。生成一个 SSH 密钥会导致*两个长字符串*，其中包括一个**公钥**和一个**私钥**。公钥用于解锁任何服务器，然后将其与 SSH 密钥的私有部分所在的客户机(通常是您的计算机)连接。

## 找出任何现有的 SSH 密钥

在生成 SSH 密钥之前，您必须检查您的计算机中是否存储了任何现有的 SSH 密钥。为此，请打开您的终端，键入以下命令并按回车键。

`ls -al ~/.ssh`

它显示您的中的文件列表。ssh 目录。浏览这个列表，查找是否存在任何公共 SSH 密钥。默认情况下，公钥存储在。pub 文件扩展名。

通常，公钥出现在下列名称旁边:

*   id_rsa.pub
*   id_dsa.pub
*   id_ecdsa.pub
*   id_ed25519.pub

如果没有任何公共密钥，那就该创建一个新的 SSh 密钥了，我很快会教你这个。相反，如果您找到一个现有的公钥/私钥对(例如`id_rsa.pub`和`id_rsa`，您可以使用它来添加到 GitHub 帐户并开始使用。

我检查了 SSH 密钥的存在，下面是 GIF:

[![](img/fdff70258cf5f435cc493aa123140af7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JIDdosAF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://maedahbatool.com/wp-content/uploads/2019/04/Screen-Recording-2019-04-01-at-12.22-PM.gif)

## 生成新的 SSH 密钥

如果不存在公钥，或者您想为 GitHub 创建一个新的密钥，请在终端中键入以下命令，然后按 Enter 键。

`ssh-keygen`

密钥生成过程将开始。它会询问保存密钥和其他相关信息的文件。填写这些细节。但是，您可以将其留空，它会生成您的第一个密匙，默认名称为`id_rsa.pub`。请记住，我没有在这里改变任何默认值，只是按下回车键或所有选项，如密码和密钥路径。

下面是 GIF:

[![](img/cb5b60a79c29b4eb3b0a6cdd8d2f5a1f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NpOPpwOB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://maedahbatool.com/wp-content/uploads/2019/04/ezgif.com-gif-maker.gif)

### 复制 SSH 密钥

现在是时候复制 SSH 密钥的内容并添加到 GitHub 帐户中了。键入以下内容:

`cat ~/.ssh/id_rsa.pub`

SSH 密钥如下所示。复制就好！

[![](img/01c9b3a5e929e9db5afafafba869ff24.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0HqWLy0g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://maedahbatool.com/wp-content/uploads/2019/04/Screen-Recording-2019-04-02-at-01.35-PM.gif)

### 向 GitHub 添加 SSH 密钥

现在进入最后一部分，我将把 SSH 密钥添加到我的 GitHub 帐户中。在你的 GitHub **配置文件**中打开**设置**。转到**宋承宪和 GPG 键**部分。点击**新建 SSH 密钥**按钮，编写合适的**标题**，粘贴复制的**密钥**，点击**添加 SSH 密钥**按钮。

🤜嘣！给你。您已经成功地将您的第一个 SSH 添加到 GitHub。现在可以很容易地克隆 GitHub repo。此外，您还会收到一封确认电子邮件，通知您添加了这个新密钥。这是为了确保不会添加未经授权的密钥。

我上传 GIF 来解释前面提到的过程。

[![](img/9779f9799c0a6240ebd14e1ea3890bfe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jqpVhCbx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://maedahbatool.com/wp-content/uploads/2019/04/ezgif.com-gif-maker-2.gif)

## 👩‍🏫轮到你了！

看看创建一个 SSH 密钥是多么简单和容易。您可以生成多个 SSH 密钥并添加到您的 GitHub 帐户。但是我会建议你尽量少用，以支持简单性。现在轮到你重复这个过程，并在下面的评论部分分享你的反馈。

此外，你可以[在 Twitter](https://twitter.com/intent/tweet?text=%23TIL%20%E2%80%94%20How%20to%20generate%20an%20SSH%20key%20and%20add%20to%20a%20GitHub%20account.%0A%0AGive%20this%20%23GirlDevMinute%20a%20try!%20%0A%0Avia%20%40MaedahBatool%20%0A%0ARead%20here%20%0Ahttps%3A//maedahbatool.com/generating-a-new-ssh-key/%0A) 上用标签 **#GirlDevMinute** 分享，或者通过我的 Twitter 账户([@ maedabatool](https://twitter.com/MaedahBatool))联系我。
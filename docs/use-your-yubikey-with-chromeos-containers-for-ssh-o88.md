# 使用带有 ChromeOS 容器的 Yubikey 进行 SSH

> 原文：<https://dev.to/niklasmerz/use-your-yubikey-with-chromeos-containers-for-ssh-o88>

在 ChromeOS 上设置我的终端虚拟机时，我想知道如何在 ChromeOS 的 Linux 上使用我的 Yubikey for SSH。目前，Crostini 项目还没有对 Linux 容器的 USB 支持。

但是我发现你可以通过`penguin.linux.test`SSH 进入你的容器。当您设置 Open-SSH 服务器和 SSH 密钥进行认证时，您可以使用支持 USB 智能卡的 ChromeOS 安全外壳应用程序。现在，我可以 SSH 到我的容器中，并在我的 Linux 终端中对 Git 克隆和其他服务器使用代理转发。

为了让 Linux 容器中的 SSH 服务器在 ChromeOS 上运行，我需要重命名`/etc/ssh/sshd_not_to_be_run`。像往常一样将您的密钥放入`~/.ssh/authorized_keys`中，您可以使用安全 Shell 应用程序连接到您的 Linux 终端:

[![Secure Shell Setup](img/7b3a7c4232b6bf1db65aede4d7f90406.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5_ZvCZxr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.merzlabs.com/static/crostini/ssh.png)

当我进入我的本地 Linux 容器时，我可以使用我的 Yubikey 进行 Git 和 VSCode 进行编辑。我的 Chromebook 现在完全能够像普通 Linux 机器一样执行开发任务。

我在 ChromeOS Dev 频道上运行了一段时间。您可能需要在 Linux 容器中安装和配置不同的 SSH。

这篇文章是在一个 Chromebook 上用 VSCode 编辑的，并通过 SSH 推送，如下所示:)
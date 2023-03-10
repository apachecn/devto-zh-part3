# 让 Homestead 与 Hyper-V 友好相处

> 原文：<https://dev.to/nicolus/getting-homestead-to-play-nice-with-hyper-v-4202>

家园是地方发展的一个极好的工具。它基本上是一个预配置的 Ubuntu 流浪者盒子，已经有了 PHP 开发所需要的一切，甚至更多。最直接的使用方法是安装 Virtualbox。因此，如果你不需要 Hyper-V，你可以安全地停止阅读这篇文章，继续你的快乐之路。

然而，如果你像我一样，你可能需要它运行在 Hyper-V 而不是 Virtualbox 上，因为你不能在 Windows 上同时运行两者。因此，如果你想使用 Docker(它使用 Hyper-V)或 Hyper-V 机器做其他事情，这意味着每次你想使用其中一个或另一个时，你都必须重启你的机器，这真的不切实际。
好消息是，Homestead 对 Hyper-V 的支持在过去几年里取得了很大进展，但仍然有很多问题，我将在这里尝试解决。

这将是一个完整的指南，但我认为[官方文件](https://laravel.com/docs/5.7/homestead#configuring-homestead)应该足以让你开始，所以我将只涵盖我在使用 hyper-V 运行 Homestead 时遇到的具体问题。

# 以管理员身份使用一切

第一个恼人的事情是，为了让 Homestead(和底层的流浪者)能够与 SMB 共享您的工作文件夹，您需要以管理员的身份使用它们。这反过来意味着您的 IDE 需要以管理员身份运行才能与 Homestead 通信。

因此，您可能希望创建快捷方式，以管理员身份启动 Powershell(或 CMD)和 IDE，并养成使用它们的习惯。

# 设置网络

这个设置的主要问题是流浪者不是(还不是？)能够管理 Hyper-V 的虚拟交换机，所以你必须自己创建一个，否则你将无法从 homestead 访问互联网和你自己的主机。
为此，打开 hyper-V 管理器，在*动作*窗格中选择*虚拟交换机管理器*，点击*新虚拟网络交换机*，然后点击创建按钮。给它一个有用的名称(如“外部交换机”)，确保选中了*外部*网络，并选择了连接到您的网络和互联网的物理以太网控制器，然后单击确定。
[![](img/c9af8883dd48bcef74a57b67cb7295e3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--b7BKsAWv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jnt0k7gara8solwtge83.png)

Homestead 第一次启动时，它会询问您使用哪个网络，您会告诉它使用这个外部交换机。
[![](img/26e995131ec560bcedc6c9655226b391.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--0FBvnO5v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o9aa7p1nqico3sr4qoc5.png)

# 自动管理主机文件

通过[流浪者主机管理器](https://github.com/devopsgroup-io/vagrant-hostmanager)插件，homestead 能够自动更新你的*主机*文件，使你在 Homestead.yaml 中声明的域名可访问。
这对于 Hyper-V 特别有用，因为每当你重启你的机器时，它的 IP 可能会改变，所以如果没有这个插件，你每次都需要手动编辑你的 *hosts* 文件。假设安装了 vagger，你可以在命令行的任何地方运行`vagrant plugin install vagrant-hostmanager`来安装插件。

从现在开始，每次 homestead 启动时，您的*主机*文件将自动更新，这样 Homestead.yaml 文件中声明的所有域都将指向您的虚拟机。有两件事要记住:

1.  该插件只在`vagrant up`触发，不在`vagrant reload`触发，所以当你重启你的 homestead 机器时，你应该总是运行`vagrant halt`然后`vagrant up`来确保你的主机保持最新
2.  这仅仅是因为在这种情况下，我们必须以管理员身份运行 vagger，这样它才能对 hosts 文件进行写访问。

# 允许 Homestead 访问您的工作文件夹

Homestead 在使用 Hyper-V 时使用 SMB 在您的机器和虚拟机之间共享文件，默认情况下，它每次启动时都会要求您输入登录名和密码，这很快就变得很烦人。幸运的是，您现在可以直接在 Homestead.yaml 文件中指定用于 SMB 共享的凭据，但我不建议将您自己的凭据放在您计算机上的文件中。我发现一个可接受的解决方案是:

1.  创建一个新的 windows 用户名“流浪者”，密码为“流浪者”。
2.  与该用户共享我的代码文件夹(以及 Homestead.yaml 文件中声明的每个文件夹),并授予其读/写权限
3.  对 Homestead.yaml 文件中的每个文件夹使用“流浪者”用户凭据，如下所示:

```
folders:
    - map: ~/code
      to: /home/vagrant/code
      smb_username: vagrant
      smb_password: vagrant 
```

Enter fullscreen mode Exit fullscreen mode

# 从命令行让 x-debug 工作

X-debug 应该可以开箱即用来调试网页，但是它不能从命令行使用所提供的`xphp`命令。原因是该命令假设您的主机来自 VM 的 IP 是 *192.168.10.1* ，这确实是 virtualbox 的情况，但在运行 Hyper-v 时完全不是这样。
我找到了一个解决方案，即修改 xphp 别名，以便它将找到到 VM 的最后一个 SSH 连接，并使用该 IP 作为 xdebug 的 remote_host。这可能不是 100%可靠，但在大多数情况下，IP 应该是您自己的，因为您将是唯一一个通过 ssh 连接到 VM 的人。
所以你需要做的是编辑 Homestead 目录根目录下的*别名*文件，并用这个函数替换 xphp()函数:

```
function xphp() {
    (php -m | grep -q xdebug)
    if [[ $? -eq 0 ]]
    then XDEBUG_ENABLED=true else XDEBUG_ENABLED=false fi

    if ! $XDEBUG_ENABLED; then xon; fi

    #Find the IP of the latest ssh connection :
    HOST_IP=$(last --limit=1 | grep -oP '\d+(\.\d+){3}')

    php \
        -dxdebug.remote_host=${HOST_IP} \
        -dxdebug.remote_autostart=1 \
        "$@"

    if ! $XDEBUG_ENABLED; then xoff; fi
} 
```

Enter fullscreen mode Exit fullscreen mode

今天就到这里，希望这能对某人有所帮助！
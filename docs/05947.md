# Linux 强化简介

> 原文：<https://dev.to/terceranexus6/introduction-to-linux-hardening-5aj1>

有段时间了！我最近一直在做这么多项目，但其中最重要的一个是与 **Linux 硬化**相关的。本周我将做一个关于它的演讲，我想先分享一些。

首先，Linux 加固是为了增强系统的安全级别，大多是通过低级命令来检查和编辑基本/默认 OS 值。

[![](img/62f2b9045bb84579e4a2149082a0c517.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--idLTKQKu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://img.webme.com/pic/i/ilja-spb/tux_the_warrior_1.png)

## 加密

我们可以将 LUKS 用于磁盘和卷加密。另一方面，我们可以依靠`openssl`或`gpg`来加密不同类型的文件。让我们来看看这两个选项:

```
$ gpg --output nombre_salida --passphrase nuestra_contraseña --batch --no-tty --symmetric archivo_a_cifrar
$ openssl rsautl -encrypt -pubin -inkey miclave.pub -ssl -in archivo_a_cifrar -out salida 
```

Enter fullscreen mode Exit fullscreen mode

第一个让我们使用密码加密大文件，而第二个使用公钥，但不能加密大文件。

## 许可

命令`ls -ahl /home/ 2>/dev/null`让我们检查`home`许可。我们可以查看所有的书面许可，除非在一个目录中(姑且称之为“示例”)，并且使用`find / -perm -222 -type d -not -path "/ejemplo/*"`查看其内容，使用`find / -executable -type d`查看执行许可。`find`工具基本上允许我们寻找目录和文件，`-type d`选项搜索目录，有很多选项:

*   b 块(缓冲)特殊
*   c 字符(无缓冲)特殊
*   目录
*   管道(先进先出)
*   f 常规文件
*   l 符号链接
*   (电源)插座
*   d 门(Solaris)

检查帐户中的空密码非常重要。我们可以在`/etc/shadows`中查看这些信息。这是一个用户帐户的例子:

```
ejemplousuario:$6$XOivHvJZ$DthDIzmVnzMigsByXQ2diHJZ9LFbROkyGyXnZ.98t5vpECl96Jmk621hquET/z8fbS9L5n4sFvTsvMtkBSWJM/:17911:0:99999:7::: 
```

Enter fullscreen mode Exit fullscreen mode

基本上，我们按顺序找到:用户名、密码算法、密码、最后一次更改时间(自 1970 年 1 月 1 日)、密码更改前必须经过的最短天数、密码有效的最长天数、用户被告知更改密码前的天数、密码到期(不在示例中)和绝对到期日期(不在示例中)。虽然，我们可以使用
`grep "^PASS_MAX_DAYS\|^PASS_MIN_DAYS\|^PASS_WARN_AGE\|^ENCRYPT_METHOD" /etc/login.defs 2>/dev/null`更容易地检查相关的密码策略。

```
awk -F: '($2 == "") {print}' /etc/shadow 
```

Enter fullscreen mode Exit fullscreen mode

这个命令让我们检查一个帐户是否有空密码，检查结构的第二个值。我们也可以使用:

```
awk -F: '($3 == "0") {print}' /etc/passwd 
```

Enter fullscreen mode Exit fullscreen mode

检查是否只有`root`的 UID 为 0，如果不是，我们应该处理它。

## 物理安全

我们不应忘记，系统也必须受到物理保护！让我们为 BIOS 和 GRUB 引导加载程序使用一个密码。也禁用 USB 启动。Fedora、CentOS 等可以使用 l 键交互启动，我们可以禁用它编辑`/etc/sysconfig/init`并将`PROMPT=no`行的提示改为“否”。如果我们禁用 usb 大容量存储驱动程序，我们就限制了系统中的 USB 设备，避免了通过橡皮鸭、坏 USB 等的攻击。

```
$ ls -l /lib/modules/$(uname -r)/kernel/drivers/usb/storage/usb-storage.ko 
```

Enter fullscreen mode Exit fullscreen mode

我们还可以在`/etc/modprobe.d/`中配置一个`blacklist.conf`的设备黑名单。

## SSH

我们可以使用
来检查 root 是否允许通过 SSH 登录

```
$ grep "PermitRootLogin " /etc/ssh/sshd_config 2>/dev/null | grep -v "#" | awk '{print  $2}' 
```

Enter fullscreen mode Exit fullscreen mode

说到这里，建议使用公钥来执行安全登录。

```
$ ssh-keygen -t key_type -b bits -C "comentario"
$ ssh-keygen -t ed25519 -C "Login al cluster de produccion"
$ ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa_aws_$(date +%Y-%m-%d) -C "AWS para clientes" 
```

Enter fullscreen mode Exit fullscreen mode

要安装一个键，我们可以使用`ssh-copy-id`。我们还可以使用`AllowUsers`和`DenyUsers`来限制访问，以及使用`PermitEmptyPasswords no`来禁用空密码。

## 克朗乔布斯

使用`ls -la /etc/cron*`,我们可以快速检查每小时、每天、每月和每周编程的任务。对于 crontab 文件的模块化扫描，我们可以看到`cron.d`，但是如果我们想要检查单个活动的 crontab，我们可以使用`ls -la /var/spool/cron/crontabs`。

## 其他

我们还可以使用`w 2>/dev/null`来检查当时还有谁在连接。我们还可以使用`grep -v -e '^$' /etc/sudoers |grep -v "#"`检查拥有`root`权限的用户和组。我们使用它是为了避免注释行(`#`)。另外，`/etc/login.defs`包含一般用户信息的有趣信息，`useradd`和`groupadd`例如使用这个文件中的值。我们也可以尝试使用`umask -S & umask`(符号`-S`和八进制值)来检查 umask 值。为了避免 SNMP 反射攻击，我们可以使用`cat /etc/services | grep -i snmp`检查 SNMP 的默认端口。

我鼓励你们所有人试验、编写和使用这些信息，为了安全的动摇！:)
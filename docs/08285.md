# 如何在 linux 上创建 sudo 用户(Debian/Ubuntu)

> 原文：<https://dev.to/shadowlik/como-criar-um-usuario-sudo-no-linux-debian-ubuntu-l9f>

sudo 命令可让一般使用者存取管理功能，这些功能通常只能由 root 使用者使用。考虑到这一点，请非常小心地向哪个用户授予这些权限……如果要为现有用户添加权限，请跳至步骤 2。

## TL；DR；

```
$ sudo adduser nomedousuario
$ sudo usermod -aG sudo nomedousuario 
```

## 创建用户

1 在终端中键入以下命令，并确保将用户名更改为您要创建的名称。

```
$ sudo adduser nomedousuario 
```

然后，您需要设置密码，因为您是具有 root 权限的用户，所以请使用非常强的密码。然后，您需要填写一些可选的用户数据，因为这些数据不是必需的，您可以将其留空。

2 我们现在使用 usermod 命令将用户添加到 sudo 组中。

```
$ sudo usermod -aG sudo nomedousuario 
```

3 现在我们将测试新创建的用户及其权限，如 sudo。

```
$ su - nomedousuario
nomedousuario$ sudo ls -ls /root 
```

每次启动新的终端机阶段作业时，您都需要输入 sudo 命令的密码。

## 额外:删除用户

### TL；DR；

```
$ sudo su -
$ userdel -r nomedousuario 
```

1 切换到 root 用户:

```
$ sudo su - 
```

2 使用 userdel 命令删除旧用户:

```
$ userdel nomedousuario 
```

3 也可以删除此用户及其主目录(/home/username):

```
$ userdel -r nomedousuario 
```
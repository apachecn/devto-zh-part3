# 从远程位置获取东西的专用反向 SSH

> 原文：<https://dev.to/chilcutt/ad-hoc-reverse-ssh-for-getting-things-from-remote-locations-2ej8>

作为一名 web 开发人员，我经常通过 SSH 使用远程服务器。有时我在远程服务器上处理一个文件，并希望将它恢复到我的本地机器上(例如，脚本输出、CSV 或日志文件)。我可以很容易地恢复它们的一种方法是在我与服务器的连接上打开一个反向的 SSH 端口。

**TL；博士**打开一个反向连接到你的本地电脑和`scp`一个文件首页

```
# from the remote shell, get into the SSH prompt
$ <enter> ~C

ssh> -R 127.0.0.1:2222:127.0.0.1:22

$ scp -P2222 move_this_file.txt kyle@127.0.0.1:~/Downloads 
```

让我们来看看这是怎么回事。

```
$ <enter> ~C 
```

那是“回车”键，后面跟着一个“波浪号”，后面跟着一个大写的“C”。这串击键会让你进入`ssh`命令行，并给出一个新的提示。

```
ssh> -R 127.0.0.1:2222:127.0.0.1:22

```

根据新提示，我们将请求一个远程转发端口…

```
ssh> -R 127.0.0.1:2222 :127.0.0.1:22

```

从端口 2222 上的远程机器…

```
ssh> -R 127.0.0.1:2222: 127.0.0.1:22

```

本地机器的 22 号端口。

设置完成后，我们可以看到在端口 2222 上打开了一个转发端口:

```
$ netstat -nap | grep 2222 tcp 0 0 127.0.0.1: 2222 0.0.0.0:*

```

然后，我们可以使用这个端口连接到本地机器上的 SSH 服务器，并使用 scp 命令(或任何可以使用 SSH 连接的命令)移动任何文件。

```
$ scp -P2222 move_this_file.txt kyle@127.0.0.1:~/Downloads 
```

在上面的例子中，我将名为`move_this_file.txt`的文件发送到本地机器上我主目录下的下载文件夹中。

当您关闭到远程服务器的 SSH 连接时，端口转发也将被关闭。

* * *

如果你使用的是 MacOS，你的机器上已经预装了一个 SSH 服务器，但它没有被激活，你可以从命令行打开它:

```
$ sudo systemsetup -setremotelogin on 
```

* * *

*如果你有足够的前瞻性，你可以在第一次连接远程服务器时打开这个 SSH 远程端口:*

```
$ ssh -R 127.0.0.1:2222:127.0.0.1:22 remote_user@remote_server 
```

*我通常不会预见那么远…*
# 以非超级用户身份管理 Docker

> 原文：<https://dev.to/serhatteker/manage-docker-as-a-non-root-user-3ha8>

默认情况下，当您在 Linux 上安装 Docker 时，您只能作为`root`用户或者通过使用`sudo`来访问 Docker 守护进程。因为 Docker 守护程序绑定到一个`Unix socket`而不是一个`TCP port`。默认情况下，这个 Unix 套接字属于用户`root`。

是的，一直输入`sudo`可能会令人恼火。当然，这个问题有一个解决方案，但是你在使用它的时候应该非常小心。另一个警告:**永远不要。永远不会。**在生产服务器中使用这个`shortcut`方法。

如果你不想在 docker 命令前面加上 sudo，那么创建一个名为`docker`的`Unix group`，并在其中添加`users`。当 docker 守护进程启动时，它会创建一个可由 Docker 组成员访问的 Unix 套接字。

然而，这个`docker`组授予等同于`root`用户的特权。所以这是警告的主要原因。有关这如何影响您系统安全性的更多细节，请参见 Docker 官方网站上的 [Docker 守护进程攻击面](https://docs.docker.com/engine/security/security/#docker-daemon-attack-surface)。

好了，如果你确定风险并确认了，我们继续:

1.  添加码头工人组:

    ```
    $   sudo groupadd docker 
    ```

2.  将连接的用户`$USER`添加到 docker 组。如果不想使用当前用户，请更改用户名以匹配您的首选用户:

    ```
    $   sudo gpasswd -a $USER docker 
    ```

3.  要么做

    ```
    $ newgrp docker 
    ```

    或`log out`和`log in`激活对组的更改。

4.  您现在可以运行 docker deamon，而无需使用`sudo`前缀。来测试一下:

```
 $    docker ps -a 
```

Enter fullscreen mode Exit fullscreen mode
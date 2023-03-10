# Docker for development:使用卷映射使本地文件对容器可见

> 原文：<https://dev.to/flaviabastos/docker-for-development-make-local-files-visible-to-a-container-with-volume-mapping-119h>

> 如果你是 Docker 的新手，我写了一篇非常简单明了的 Docker 介绍文章。

如果您希望看到您的代码更改立即应用到正在运行的 Docker 容器，您需要确保使用 share **volumes 标签** : `-v`(或`--volume`)来**运行容器**。

-v 标签期望将主机路径作为参数，后跟冒号(:)，再后跟容器内的目标路径:

```
-v /home/projects/my-app:/src 
```

上面的参数将把您的本地`/home/projects/my-app`目录绑定到容器的`/src`目录。这意味着您对`/home/projects/my-app`下的文件所做的所有更改都可以在容器的`/src`文件夹中访问，允许您在`/src`中执行或检查代码。

当第一次**运行**容器:
时，使用`-v`标签

```
$ docker run -v /home/projects/my-app:/src -it ubuntu bash 
```

上面的代码行启动了一个运行 ubuntu 的容器，让您可以访问命令行(`-it`和`bash`部分),并将您的本地路径`/home/projects/my-app`绑定到容器的`/src`

这非常有用，但您可能会遇到一个问题，在启动容器后，您试图在文本编辑器中对代码进行更改，当试图保存更改时，会出现一个**权限错误消息**。

[这篇文章详细解释了为什么会发生这种情况](https://medium.com/redbubble/running-a-docker-container-as-a-non-root-user-7d2e00f8ee15),但要点是 Docker 以 root 用户身份运行容器，因此它的所有文件都属于 root 用户，而您在本地主机中的用户不是同一个用户(即使您的用户拥有 root 权限！).因此，当你试图在本地保存更改时，容器会发出“哇，哇，哇，你在做什么？"

[![](img/298870458da6276f7431fc2aef8c404d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--g9OS9kii--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://testfbdotblog.files.wordpress.com/2018/12/whoa.gif%3Fw%3D620)

下面是如何**修复 docker** 中的卷共享权限:你告诉 docker 以主机当前用户的身份运行容器。为此，使用`-u`或`--user`标签:

```
--user $(id -u):$(id -g) 
```

这里发生了一些事情。这个参数告诉用户标签:

1.  获取当前用户的 UID(用户 ID) `id -u`
2.  包裹在外壳中的解析器`$()`
3.  告诉容器`:`
4.  获取当前用户的 GUID(组 ID) `id -g`
5.  包裹在外壳中的解析器`$()`

最终结果是，现在 Docker 容器将以当前主机用户的身份运行，因此在容器内外拥有相同的权限。瞧啊。

[![](img/a7b5d52da9a659d3c53e25aac735011c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GBwkCfbt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://testfbdotblog.files.wordpress.com/2018/12/sunglasses.gif%3Fw%3D620)

参考:

[Docker 运行正式文档](https://docs.docker.com/engine/reference/commandline/run/#options)

> 帖子 [Docker for development:让本地文件对具有卷映射的容器可见](https://wp.me/pa0b0y-1U)最初发表在 [flaviabastos.ca](https://flaviabastos.ca)
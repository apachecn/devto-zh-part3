# 本地服务器中的 Git

> 原文：<https://dev.to/halivert/git-in-local-server-4216>

使用 [git](https://git-scm.com) 进行版本控制，这本身就是一个很大的帮助，然而，当它必须用于将代码或项目上传到本地服务器时，这个工具变得更加有用。

首先，您必须验证您的服务器已经安装了 git，然后您必须使用下面的命令创建一个空的存储库:

```
git --bare init <Repo name>
# By convention, empty repos have the .git extension.
# Example:
git --bare init glowing-computing-machine.git 
```

Enter fullscreen mode Exit fullscreen mode

一旦创建了空回购，您就可以访问它并查看类似的结构。

[![bare-repo](img/6e3bf2253f9b828c20670e88f560859b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UItCOv1m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://halivert.github.io/img/git-repo/ls-bare-repo.png)

现在，为了让您的回购自动更新，您必须添加一个挂钩...例如，在 hooks 文件夹中，默认情况下你会找到一些，但是你将使用的那个叫做不同的: **post-receive** 。创建文件并添加下一行:

```
git --work-tree=<destination path> --git-dir=<origin path> checkout -f

# The origin path will be the path where your empty repo will be placed
# And destination path will be the path where your files will be inserted
# By instance:
git --work-tree=/home/hali/Documentos/Git/_site \
--git-dir=/home/hali/Documentos/Git/glowing-computing-machine.git \
checkout -f 
```

Enter fullscreen mode Exit fullscreen mode

最后，添加执行创建的文件
的权限

```
chmod +x post-receive 
```

Enter fullscreen mode Exit fullscreen mode

只有你必须审查，你的目的地目录是完全空的

[![ls site](img/113581e79fde815e0866a3de0c498b88.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nmjil-lQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://halivert.github.io/img/git-repo/ls-site.png)

然后，执行 git 克隆

[![ls-glowing](img/8a7b74fd3d84abb65d9e2633ad9019e8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gOT2TIMt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://halivert.github.io/img/git-repo/ls-glowing-local.png)

准备好了。，我们可以上传内容...但是每次我们拉或推的时候都必须写密码，这变得很烦人，所以在 ssh-copy-id 命令的帮助下，我们可以将我们的公共 ssh 密钥添加到服务器上接受的密钥列表中

```
ssh-copy-id <server IP> 
```

Enter fullscreen mode Exit fullscreen mode

[![ssh-copy-id](img/00ed7b99ed5617d89ddbbdcbd965ca60.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Kwhh_QDv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://halivert.github.io/img/git-repo/ssh-copy-id.png)

这就是我们如何将代码上传到我们的 repo，它将被自动复制到我们选择的文件夹中。

[![commit-inicial](img/cb61fa333f8a4b7b68bb48afc3c67960.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6-tLfw5l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://halivert.github.io/img/git-repo/commit-inicial.png)
[![ls-site-commit](img/7f13a62534542871577da474f14db1e0.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ja_-Nw0r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://halivert.github.io/img/git-repo/ls-site-commit.png)
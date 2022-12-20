# 用 mdbook 创建文档

> 原文：<https://dev.to/sinacodes/create-a-documentation-with-mdbook-4120>

mdbook 是 gitbook 的一个很酷的替代品，用 [rust](https://www.rust-lang.org/) 打造。您不需要管理任何前端工作，除了开始时的配置之外，没有后端需要维护。大多数时候这只是一个肤浅的承诺，但通过 mdbook 我学到了:如果你知道如何做，你可以在 20-30 分钟内将一台 [mdbook](https://rust-lang-nursery.github.io/mdBook/) 投入生产——我认为这真的很棒。这就是为什么我会分享我获得的知识。

作为一个完全的 rust 初学者，我不知道如何用这个静态站点生成器建立一个 rust 网站。但是不要害怕，你不需要编写 rust 代码来使用这个 SSG，你甚至不需要安装 rust 或 cargo 或任何相关的东西，因为你可以通过 homebrew 或 github 安装 mdbook，因为它是一个预编译的二进制文件，可以在你的 OS - linux，mac 和 windows 上本地运行。

因此，我们的路线图如下所示:

1.  在我们的本地机器上启动并运行 mdbook
2.  将我们的本地存储库与我们的远程服务器相连
3.  配置远程服务器(ubuntu 数字海洋水滴)

# 1。mdbook 开发环境

首先，我们想在本地机器上安装二进制文件。如果你运行的是 macOS(假设安装了 homebrew ),运行:

```
$ brew install mdbook 
```

否则你可以从 mdbook github 页面得到合适的二进制文件:

[mdbook 发布](https://github.com/rust-lang-nursery/mdBook/releases)

例如，使用 curl，运行(截至 2019 年 3 月)右边文件夹/usr/local/bin/:
中的这段代码

```
$ curl -sL https://github.com/rust-lang/mdBook/releases/download/v0.3.5/mdbook-v0.3.5-x86_64-unknown-linux-gnu.tar.gz | tar xz 
```

现在，如果您在终端中运行`$ mdbook -V`，您应该会看到当前安装的版本。

* * *

接下来我们想要初始化我们的项目，只需运行:

```
$ mdbook init myfirstmdbook && cd myfirstmdbook 
```

然后(在新创建的目录中)运行:

```
$ mdbook serve 
```

然后打开 [http://localhost:3000](http://localhost:3000) 你就已经看到我们新创作的书了——酷吧？

现在，要向您的图书添加新的站点，请自定义文件`src/summary.md` -当 mdbook serve 运行时，它将自动创建新的 md 文件，准备好供您编写。

# 2。将图书连接到远程服务器

现在我们想把这个项目连接到我们的远程服务器上！打开`master`时，输入终端

```
$ git remote add production ssh://YOUR_USER@YOUR_SERVER_IP/var/repo/myfirstmdbook.git 
```

现在他的命令行指令部署到你的服务器上(但首先我们需要设置服务器，见下文):

```
$ git push production master 
```

* * *

# 3。调整服务器

我不会太深入地去配置服务器的基础，我会假设您找到了设置基本 apache/nginx 的方法，以便在 var/www/中创建存储库。更多信息，数字海洋有非常有用的教程。

在远程主机上安装 mdbook 的方式与在本地机器上的方式相同，因为您需要安装二进制文件。

例如，在 Linux 机器上使用 usr/bin
中的 cURL 代码

```
$ curl -sL https://github.com/rust-lang/mdBook/releases/download/v0.3.5/mdbook-v0.3.5-x86_64-unknown-linux-gnu.tar.gz | tar xz 
```

好了，完成之后——在我们的远程服务器上，我们还需要安装 git，(例如在文件夹`/var/repo/`中)。然后，在 repo/中运行:

```
$ mkdir myfirstmdbook.git && cd myfirstmdbook.git
$ git init --bare 
```

很高兴知道:
` - bare`:这个只在服务器上用。它将创建一个回购协议，其唯一目的是接收推盘。

* * *

在 myfirstmdbook.git 中找到`/hooks`文件夹并创建一个名为`pre-receive`的文件。在该文件中，添加:

```
#!/bin/sh
echo "removing old deployment"
targetdir=/var/www/pathToTargetDir/html
cd $targetdir
rm -rf * 
```

每次推送时，这将删除目录中的所有内容——这样，您可以在新部署之前删除旧部署。

现在也在这里创建一个名为`post-receive`
的文件，类型:

```
#!/bin/sh
echo "running deployment"
targetdir=/var/www/pathToTargetDir/html
git --work-tree=$targetdir --git-dir=/var/repo/myfirstmdbook.git checkout -f
cd $targetdir
#!/usr/bin/env mdbook
mdbook build
rm *
rm -rf src/
cp -r book/* .
rm -rf book/
echo "new mdbook build and deployed" 
```

解释:每当发生“git 推送生产主机”时，就会执行该脚本。然后，这个脚本将移动到您的项目文件夹中，并执行一个 mdbook 构建。之后，它将删除所有文件(不是目录),并将子目录/book/(静态站点所在的位置)中的所有内容向上移动一级，以便只留下静态站点。这两个文件现在也需要能够在您的服务器上执行。/挂钩运行的内部:

```
$ sudo chmod +x post-receive
$ sudo chmod +x pre-receive 
```

* * *

就这样。现在，您应该能够在本地机器上运行
`$ git push production master`将您的 mdbook 部署到生产环境中。:)
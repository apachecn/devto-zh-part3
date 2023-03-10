# 哲基尔，rvm，bundler 和 Fedora

> 原文：<https://dev.to/robbinespu/jekyll-rvm-bundler-and-fedora-40lb>

Jekyll 是一个简单的 Ruby 静态站点生成器。它需要 html 模板和用 Markdown 编写的帖子来生成静态网站，该网站可以部署在您最喜欢的服务器上。

我用 Jekyll 写博客，不知为什么我更喜欢用 RVM (Ruby 版本管理器)而不是直接安装 Ruby。如果出现宝石或红宝石不兼容的情况，维护和拍摄就容易多了。

好的，你需要安装一些软件包，因为一些版本的 ruby 二进制文件不能通过 RVM 的 Fedora 使用，例如:

```
$ rvm install ruby
Searching for binary rubies, this might take some time.
No binary rubies available for: fedora/30/x86_64/ruby-2.6.3.
Continuing with compilation. Please read 'rvm help mount' to get more information on binary rubies. 
```

所以，rvm 将帮助你在你的系统上编译这个包，但是不幸的是 RVM 不能正确地自动下载这个包。所以让我们手动安装这个软件包。

```
$ sudo dnf install autoconf automake bison gcc-c++ libffi-devel libtool libyaml-devel readline-devel sqlite-devel zlib-devel openssl-devel 
```

我们将安装 RVM

```
$ gpg2 --keyserver hkp://pool.sks-keyservers.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB
$ \curl -sSL https://get.rvm.io | bash -s stable
$ rvm install ruby 
```

要开始使用 RVM，你需要在所有打开的 shell 窗口中运行`source $HOME/.rvm/scripts/rvm`，在极少数情况下，你需要重新打开所有的 shell 窗口。

```
$ rvm -v
rvm 1.29.8 (latest) by Michal Papis, Piotr Kuczynski, Wayne E. Seguin [https://rvm.io] 
```

接下来，为哲基尔和邦德勒安装宝石

```
$ gem install jekyll
$ gem install bundler 
```

搞定了。您可以安装新的 Jekyll 站点并在本地测试:)

额外:在 Jekyll 项目目录中，运行`bundler update --bundler && bundler install`来更新和安装所需的 gems，然后使用`bundle exec jekyll serve -d public --incremental --verbose --watch`在 localhost 上运行 Jekyll。
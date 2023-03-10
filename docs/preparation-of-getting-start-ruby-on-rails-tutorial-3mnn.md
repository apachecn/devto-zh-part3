# 准备入门“Ruby on Rails 教程”

> 原文：<https://dev.to/windupbird/preparation-of-getting-start-ruby-on-rails-tutorial-3mnn>

# 背景

这几天开始用日语的 [Ruby on Rails 教程](https://www.railstutorial.org/book)学习 Rails。
(这是[日文版](https://railstutorial.jp/))

我用流浪者和 VirtualBox 设置了本地环境，因为我不想使用 AWS 和 Heroku 等...(主要原因是钱。)

所以我写下这篇文章(或者笔记？)开始 Ruby on Rails 教程。

# 我的环境

*   操作系统:macOS High Sierra(版本 10.13.6)
*   流浪者:2.2.0
*   VirtualBox: 5.2.18 版

如果你还没有安装的话，请看下面如何安装。
[下载——Oracle VM VirtualBox](https://www.virtualbox.org/wiki/Downloads)
[安装 HashiCorp](https://www.vagrantup.com/docs/installation/) 的游民-游民

# 流浪档案

我用的是 Ubuntu 18.04 作为来宾虚拟机。我将端口转发从 3000(主机)设置为 3000(来宾)。

*例题*

```
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"
  config.vm.network "forwarded_port", guest: 3000, host: 3000
  config.vm.provider "virtualbox" do |vb|
     vb.memory = "2048"
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

在我的例子中，错误的发生是因为在 guest 虚拟机上安装 rails 时内存不足。所以，我改变了内存的大小(默认是 1024 我猜)如上。

# 安装 rbenv

启动虚拟机后，

```
$ vagrant up
$ vagrant ssh 
```

Enter fullscreen mode Exit fullscreen mode

我先安装了 rbenv。

```
$ git clone https://github.com/sstephenson/rbenv.git ~/.rbenv
$ echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bash_profile
$ echo 'eval "$(rbenv init -)"' >> ~/.bash_profile
$ source ~/.bash_profile
$ rbenv -v
rbenv 1.1.1-39-g59785f6 
```

Enter fullscreen mode Exit fullscreen mode

第二，我安装了 rbenv 插件。

```
$ git clone https://github.com/sstephenson/ruby-build.git ~/.rbenv/plugins/ruby-build 
```

Enter fullscreen mode Exit fullscreen mode

# 安装 Ruby

然后，我查了一下 Ruby 的版本，安装了`2.5.0`。

```
$ rbenv install --list
$ rbenv install 2.5.0
$ rbenv rehash
$ rbenv global 2.5.0
$ ruby -v
ruby 2.5.0p0 (2017-12-25 revision 61468) [x86_64-linux] 
```

Enter fullscreen mode Exit fullscreen mode

顺便说一下，我在这个过程中发现了一些错误，因为没有安装必要的软件包。所以，我做了开发工具等等。

```
$ sudo apt update
$ sudo apt install build-essential
$ sudo apt install libssl-dev libreadline-dev zlib1g-dev libsqlite3-dev nodejs 
```

Enter fullscreen mode Exit fullscreen mode

# 安装导轨

在 [Ruby on Rails 教程](https://www.railstutorial.org/book/beginning#sec-introduction)中，Rails 版本是`5.1.6`，所以我运行这个命令；

```
$ gem install rails -v 5.1.6 
```

Enter fullscreen mode Exit fullscreen mode

```
$ rails -v
Rails 5.1.6 
```

Enter fullscreen mode Exit fullscreen mode

似乎安装成功。

# 第一个 Rails 应用

我想检查安装是否正确，所以运行以下命令；

```
$ mkdir environment
$ cd environment/
$ rails _5.1.6_ new hello_app
$ cd hello_app/
$ rails server 
```

Enter fullscreen mode Exit fullscreen mode

之后，我访问了这个网址；

[http://localhost:3000](http://localhost:3000)

[![Screenshot.png](img/88970b6a8d8fca3c4123df4c376fad85.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dN9oNVSi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7qnp4964ku6e0h8mnx45.png)

；)
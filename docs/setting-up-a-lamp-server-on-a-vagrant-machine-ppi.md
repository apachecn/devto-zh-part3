# 在流浪机器上设置 LAMP 服务器

> 原文：<https://dev.to/untitledone/setting-up-a-lamp-server-on-a-vagrant-machine-ppi>

当我第一次开始用 PHP 开发时，我在 Windows 机器上使用 XAMPP。几年后，我找到了第一份工作，并开始使用 Mac 电脑。在 Mac 上使用 XAMPP 感觉不太对，所以我找到了流浪者。从那以后，我公司的所有新开发人员都得到了一台配置了流浪者机器的 MacBook，当我设置他们的机器时，我总是必须参考我的笔记，所以我想我可以将这些笔记分享给全世界，作为在流浪者机器上设置 LAMP 的指南。

在本指南中，我们将使用 Mac OSX、流浪者、虚拟盒子、Ubuntu 和 [Sequel Pro](https://www.sequelpro.com/) 。在我们的流浪机器上，我们将安装，Apache，PHP 7，MySQL & PDO。我们还将配置一个虚拟主机和一个 SSH 连接。

写这篇文章之前，我不知道有[https://box.scotch.io/](https://box.scotch.io/)，这可能符合你的需要，并使设置更快。

## 安装

> 在撰写本文时，VirtualBox 的 SSL 似乎没有正确配置，并且无法通过下面的链接访问。 [Reddit 贴出关于它的帖子](https://www.reddit.com/r/virtualbox/comments/bhu0w6/why_cant_i_download_virtualbox/)，作为一个变通办法，你可以去 https://download.oracle.com/virtualbox/6.0.6/点击`VirtualBox-6.0.6-130049-OSX.dmg`它会带你到一个页面，上面写着“一个未经授权的请求”只需从 URL 中删除`https://`,就可以开始下载了。

首先，我们需要安装 [VirtualBox](https://www.virtualbox.org/wiki/Downloads)

接下来，我们需要为 MacOS 安装[流浪者](https://www.vagrantup.com/)。

在本指南中，我们将使用 **ubuntu/xenial64** 你可以从他们的目录中找到其他的流浪者盒子[这里](https://app.vagrantup.com/boxes/search)。

现在让我们创建一个目录，包含我们的配置文件和机器。我喜欢把我的放在我的桌面上。

```
cd ~/Desktop
mkdir our_vagrant_machine 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们在这个目录中设置一个漫游机。

```
cd ~/Desktop/our_vagrant_machine
vagrant init ubuntu/xenial64 
```

Enter fullscreen mode Exit fullscreen mode

一旦机器设置完成，我们就可以打开它，在里面进行 SSH。

```
vagrant up
vagrant ssh 
```

Enter fullscreen mode Exit fullscreen mode

`vagrant up` - *这个命令根据你的流浪文件创建和配置访客机器。*
`vagrant ssh` - *这会把 SSH 变成一个正在运行的无业游民机器，让你访问一个 shell。*

一旦你在里面，我们可以开始安装基本的，Apache，MySQL，PHP 和 PDO 扩展。

```
sudo apt-get install apache2
sudo apt-get install mysql-server # This will ask for a root password, I leave it blank. 
sudo apt-get install php
sudo apt-get install php7.0-mysql 
```

Enter fullscreen mode Exit fullscreen mode

## 本地主机

现在我们已经在那里安装了 Apache，我们现在可以配置一个本地服务器来连接我们的虚拟机。

> 该文件描述了项目所需的机器类型，以及如何配置和供应这些机器。

在你的*our _ vagger _ machine/*目录里面，有一个文件叫做 Vagrantfile。在文本编辑器中打开这个文件，里面有两行我们要取消注释的内容。

第一个是

```
config.vm.network "forwarded_port", guest: 80, host: 8080 
```

Enter fullscreen mode Exit fullscreen mode

第二个是

```
config.vm.network "private_network", ip: "192.168.33.10" 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们重新启动我们的机器，回到我们的终端，用`exit`退出 shell，然后重新启动机器，SSH 重新进入，并重新启动 Apache。

```
vagrant reload
vagrant ssh
sudo /etc/init.d/apache2 start 
```

Enter fullscreen mode Exit fullscreen mode

在您的浏览器中，导航到`http://192.168.33.10/`，您应该会看到 Apache 的欢迎页面。

太棒了，我们的流浪机器人开始运行了！

## 同步文件夹

但是我们需要显示 Apaches 默认欢迎页面之外的内容。那我们怎么把自己的文件放到流浪机器上呢？在我们的流浪文件中有一个名为`config.vm.synced_folder`的配置行。非常好。

让我们在桌面上创建一个名为**vagger _ site**
的新目录

`index.php`

```
<?php echo "Yo world"; ?> 
```

Enter fullscreen mode Exit fullscreen mode

让我们回到我们的文本编辑器中的流浪者文件，取消注释

```
config.vm.synced_folder "../data", "/vagrant_data" 
```

Enter fullscreen mode Exit fullscreen mode

我们现在需要将`"../data"`更改为我们的 hello world 文件夹，将`"/vagrant_data"`更改为我们的文件在流浪者机器中的路径。

```
config.vm.synced_folder "../vagrant_site/", "/var/www/html/" 
```

Enter fullscreen mode Exit fullscreen mode

保存更改，`vagrant reload`机器，刷新`http://192.168.33.10/`你应该看到“哟世界”。

## MySQL 和 Sequel Pro

现在让我们用 MySQL 配置数据库连接，并将 Sequel Pro 设置为数据库的 GUI。

SSH 进入机器(如果还没有)并启动 MySQL。

```
sudo mysql 
```

Enter fullscreen mode Exit fullscreen mode

创建一个新用户并授予所有特权

```
 CREATE USER 'ubuntu';
    GRANT ALL PRIVILEGES ON *.* TO ubuntu;` 
```

Enter fullscreen mode Exit fullscreen mode

酷毙了。现在`quit` MySQL、`exit`shell 并输入命令`vagrant ssh-config`。在这里你可以看到你的 SSH 配置信息，你可以用它从 Sequel Pro 登录到你的数据库。

打开 Sequel Pro，导航到 SSH。
**姓名**随便输入
**MySQL 主机**输入`127.0.0.1`
**用户名**输入`ubuntu`
**密码**空白
**数据库**空白
**端口**空白

**SSH 主机**输入`127.0.0.1`
**SSH 用户**输入`vagrant`
**SSH 按键**点击字段中的小按键图标，导航到`our_vagrant_machine/.vagrant/machines/default/virtualbox/`，选择`private_key`。
**SSH 端口**进入`2222`

现在按下**连接**，我们现在应该登录到我们的数据库。

## 虚拟主机

在这一部分中，我们将设置一个虚拟主机，因此我们可以导航到您选择的 URL，而不是导航到`http://192.168.33.10/`。我个人喜欢在本地机器上使用`hello.world`。

为此，我们必须找到我们的`hosts`文件。我通常使用我的**搜索器**，在搜索器菜单中按**转到**，**转到文件夹**，输入`/etc/hosts`，然后按“转到”。现在我们已经找到了它，让我们在文本编辑器中打开它，并在底部添加一个新行。

无业游民专网 IP + **空间** +你选择的网址。
`192.168.33.10 hello.world`

保存主机文件。

现在我们需要为虚拟主机创建一个配置文件。

再次进入无业机器，导航到 Apache 的“sites-available”目录。

```
cd /etc/apache2/sites-available/ 
```

Enter fullscreen mode Exit fullscreen mode

创建新的配置文件。

```
sudo vim hello.world.conf 
```

Enter fullscreen mode Exit fullscreen mode

并将以下内容添加到新文件中。

```
<VirtualHost *:80>
  ServerAdmin webmaster@localhost
  ServerName hello.world
  DocumentRoot /var/www/html/
</VirtualHost> 
```

Enter fullscreen mode Exit fullscreen mode

省省吧。 *( `:wq!` )*

运行:

```
sudo a2ensite hello.world.conf
sudo service apache2 reload 
```

Enter fullscreen mode Exit fullscreen mode

这就对了。

一台运行着的流浪汉机器，有 Ubuntu，Apache，PHP，MySQL，一个 Sequel Pro 连接，一个虚拟主机。

我希望这有所帮助，这是我的第一篇帖子，所以任何建议，建设性的批评和/或批评都非常感谢。谢了。
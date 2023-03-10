# 如何为 Linux 配置全栈开发环境

> 原文：<https://dev.to/nielsenjared/how-to-configure-full-stack-development-environment-for-linux-420l>

[![How to Configure Full Stack Development Environment for Linux: Penguins](img/b15a042834940b4dc26767dbf8890a7a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zIZlM6su--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t8r95zn6ugpcqduu4qva.png)

*本文原载于[jarednielsen.com](http://jarednielsen.com/full-stack-development-linux/)T3】*

我首选的开发环境是 Linux，我首选的升级发行版的方法是全新安装。这意味着每次升级我都需要重新安装所有的工具。这篇文章主要是为我自己做参考，因为这是我以前做过的事情，而且肯定会再做一次。希望你也觉得有用。

## 去吧

也许最重要的是版本控制。作为 Linux，它是一行程序。

```
sudo apt install git-all 
```

然后生成你的密钥并添加到 [GitHub](https://github.com/settings/keys) :

```
ssh-keygen -t rsa -b 4096 -C "your_email@example.com" 
```

按照提示，然后运行:

```
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa 
```

## [这里](#ide)

### Atom

所有酷小孩都在用 VSCode，但 Atom 还是我的最爱。你不能教一个老开发者新把戏。

*   [https://atom.io/](https://atom.io/)
*   下载。黛比

或者，您可以按照这里的说明从命令行安装它

### VSCode

[https://code.visualstudio.com/Download](https://code.visualstudio.com/Download)

## Node.js

您需要[下载节点](https://nodejs.org/en/download/package-manager/#debian-and-ubuntu-based-linux-distributions)的最新版本。您随后运行的命令将类似于以下内容:

```
curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash - 
```

终端将提示您运行以下命令:

```
sudo apt-get install -y nodejs 
```

可选地，安装

```
sudo apt-get install -y build-essential 
```

## [MySQL](#mysql)

在 Linux 上安装 MySQL 轻而易举，但我总是得到同样的权限错误，所以我添加了下面的解决方案。

```
sudo apt-get install mysql-server
service mysql start 
```

如果你得到`ERROR 1698 (28000): Access denied for user 'root'@'localhost'` :

```
sudo mysql -u root

mysql> USE mysql;
mysql> UPDATE user SET plugin='mysql_native_password' WHERE User='root';
mysql> FLUSH PRIVILEGES;
mysql> exit;

service mysql restart 
```

或者创建一个新用户:

```
sudo mysql -u root

mysql> USE mysql;
mysql> CREATE USER 'YOUR_SYSTEM_USER'@'localhost' IDENTIFIED BY '';
mysql> GRANT ALL PRIVILEGES ON *.* TO 'YOUR_SYSTEM_USER'@'localhost';
mysql> UPDATE user SET plugin='auth_socket' WHERE User='YOUR_SYSTEM_USER';
mysql> FLUSH PRIVILEGES;
mysql> exit;

service mysql restart 
```

### MySQL GUI

在处理数据库时，我通常更喜欢命令提示符，但有时 GUI 也很有帮助。

#### 工作台

它在每个平台上都有不同的缺陷，但仍然被广泛使用。[在此下载工作台。](https://www.mysql.com/products/workbench/)

系统会提示您安装 Linux 发行版。要查看它，运行以下命令:

```
lsb_release -a 
```

如果你使用的是 Mint 这样的衍生发行版，这可能没什么帮助，所以试试:

```
cat /etc/*release 
```

#### DBeaver

或者，尝试 [DBeaver](https://dbeaver.io/)

## MongoDB

通过安装 MongoDB。deb 打包也很容易。在撰写本文时(2019/01)，以下命令是当前的，但是您可能希望[查阅文档以了解针对您的特定发行版的更改或说明](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/#install-mongodb-community-edition-using-deb-packages) :

```
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 9DA31620334BD75D9DCB49F368818C72E52529D4

echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu bionic/mongodb-org/4.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.0.list

sudo apt-get update

sudo apt-get install -y mongodb-org 
```

然后运行:

```
sudo service mongod start 
```

### 蒙哥 GUI

#### 机器人 3T

[在这里下载 Robo3T。](https://robomongo.org/download)

## 奖金

我认为有用且必要的其他工具，但*不一定是* web 开发:

### 懈怠

[https://slack.com/downloads/linux](https://slack.com/downloads/linux)

### 铬

我更喜欢火狐的薄荷，但也得有这个
[https://www.google.com/chrome/](https://www.google.com/chrome/)

### 收存箱

[https://www.dropbox.com/install-linux](https://www.dropbox.com/install-linux)

我经常发现自己需要做一些小的照片或图片工作。Inkscape 和 GIMP 是很好的(免费的)Adobe 替代品。

### 喷墨打印纸

`sudo apt-get install inkscape`

### GIMP

附带 Mint，但是如果你的发行版不包含它:

```
sudo add-apt-repository ppa:otto-kesselgulasch/gimp
sudo apt update
sudo apt install gimp 
```

## (再)来源

*   [生成新的 SSH 密钥并将其添加到 ssh-agent](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)

*   [错误 1698 (28000):拒绝用户“root”@“localhost”](https://stackoverflow.com/questions/39281594/error-1698-28000-access-denied-for-user-rootlocalhost) 的访问

* * *

我每周写一份时事通讯，分享关于编程、解决问题和终身学习的文章。[立即加入](http://eepurl.com/cP8CMn)
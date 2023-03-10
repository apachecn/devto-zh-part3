# OS X MySQL 安装完整教程

> 原文：<https://dev.to/vitalipom/os-x-mysql-install-complete-tutorial-gdj>

# MySQL OS X:使用自制软件快速安装

有时你只是想执行一些通过快速管道和 bash 命令不可用的额外的普通操作，并且你不想使用 Excel 或任何不可用的脚本。所以您更喜欢安装 MySQL，并使用查询来执行额外的操作和输出一些。csv 文件，但通常您不会在日常工作中浏览它，因为您不想浏览所有的过程。 **这是一个 mysql os x 安装的快速教程，完全工作，使用 2018 软件。**

我们假设你已经有自制软件了！

### 第一部分——杀人狂

如果你有一个较旧的系统，或者你有可能有一个较旧的或任何其他不稳定的 mysql，你可以像这样停止/终止进程:

```
sudo killall mysqld
sudo killall mysql 
```

### 第二部分——清除旧 mysql

如果您试图在安装后删除 mysql，您可能会破坏 mysql 安装，要完全删除它，请运行以下命令:

[鸣谢:https://coder wall . com/p/OS 6 woq/uninstall-all-than-broken-versions-of-MySQL-and-re-install-it-with-brew-on-MAC-Mavericks](https://coderwall.com/p/os6woq/uninstall-all-those-broken-versions-of-mysql-and-re-install-it-with-brew-on-mac-mavericks)

```
brew remove mysql
brew cleanup
sudo rm /usr/local/mysql 
sudo rm -rf /usr/local/var/mysql 
sudo rm -rf /usr/local/mysql* 
sudo rm ~/Library/LaunchAgents/homebrew.mxcl.mysql.plist 
sudo rm -rf /Library/StartupItems/MySQLCOM 
sudo rm -rf /Library/PreferencePanes/My* 
launchctl unload -w ~/Library/LaunchAgents/homebrew.mxcl.mysql.plist 
edit /etc/hostconfig and remove the line MYSQLCOM=-YES- 
rm -rf ~/Library/PreferencePanes/My* 
sudo rm -rf /Library/Receipts/mysql* 
sudo rm -rf /Library/Receipts/MySQL* 
sudo rm -rf /private/var/db/receipts/*mysql* 
```

现在你的电脑上没有 MySQL 了，至少除了路径变量和别名。

### 第 3 部分-快速安装

```
brew install mysql 
```

### 第 4 部分-快速设置

这是 mac 用户安装 mysql 的地方，教程指出了不寻常的和不起作用的安装命令。

*【标签:使用家酿安装在 os x 上快速设置 mysql 服务器，面向 mac 和 os x 用户的真正工作教程 2018】*

第一行是必须的。我们打开从 brew 安装的 mysql，**使用** brew。一旦我们运行它，建议的设置也将运行(它没有)。

```
brew services start mysql
mysql_secure_installation 
```

### 第 4 部分-快速运行

```
mysql is already running using the `brew services start mysql` command 
```

设置 [fish shell](https://fishshell.com/) 允许完全支持自动完成建议，Iterm2 完全支持 *fish* 自动完成，并允许使用苹果的神奇键盘使用 fish shell 进行完整导航。

* * *

伙计们
亲爱的朋友和伙伴们，
如果你喜欢我的教程，请从[这里下载](https://play.google.com/store/apps/details?id=com.menny.android.effectedkeyboard)[安卓版有效键盘](https://play.google.com/store/apps/details?id=com.menny.android.effectedkeyboard)我保证有一天你会成为安卓版最舒适、最有活力的键盘。今天我在 OnePlus 5 上使用它，我看到它在其他一些设备上运行得非常好。到今天为止，它没有任何错误，而且它显然有一些你可能会喜欢的有益的未来。请下载并在谷歌应用商店评价它。
提前感谢，
Pomanitski Vitali
Vitali . POM . dot com

* * *
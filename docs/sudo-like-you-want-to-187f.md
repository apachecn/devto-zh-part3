# Sudo 如你所愿

> 原文：<https://dev.to/moopet/sudo-like-you-want-to-187f>

[![The Goblin King](img/7699a4df5ad2f84932704021c264186d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qlHw2SoG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rmdp45r60w3s5vrqh47t.jpg)

> 你让我想起了宝贝。
> *什么贝比？*
> 拥有力量的可人儿。
> *什么力量？*T7**须藤**的力量。
> *谁做的？*
> 你行。
> *做什么？*
> 让我想起了贝比。
> ——大卫“鲁特”鲍伊。

## 背景故事

`su`是“ **s** 替代 **u** ser”的简称。一般读作“ess-you”。它几乎总是用于切换到*根*用户(如果你没有指定任何人，这是默认的)，但它不是必须的。如果我和你在同一个系统上，并且我有必要的访问权限，我可以切换到你。在这里，我演示了尝试`su`给其他人，但没有他们的密码，并得到推送:

```
$ whoami sarah
$ ls /home
sarah/    toby/   goblinking/
$ su toby
Password: ********
su: Sorry
$ whoami sarah
$ # nuts. 
```

Enter fullscreen mode Exit fullscreen mode

当然，如果我是 root，那么什么都可以:

```
$ su
Password: ********
# whoami
root
# su toby
$ whoami toby
$ # Rock on. 
```

Enter fullscreen mode Exit fullscreen mode

实际上是一种替代方法，允许用户以其他人的身份运行东西——通常是 root——而不需要知道 root 密码，也不需要以其他人的身份启动整个交互式 shell。
读作“苏斗”。不喜欢“伪”，虽然那种适合的目的，如果你倾斜你的头，眯着眼睛看世界我猜。

```
$ cd /var
$ touch this
touch: this: Permission denied
$ # U can't touch this.
$ # Now that song is in your head. My, my, my.
$ # You are welcome.
$ sudo touch this
$ ls this
-rw-r--r--  1 root  wheel  0  9 Apr 12:42 this
$ # Rock on. 
```

Enter fullscreen mode Exit fullscreen mode

就像我说的，大部分是用来变成 root 的。

## 为什么被 root 是个问题？

人们倾向于将自己作为 root 用户离开太久，而不注销。成为 root 用户很容易执行一个操作，然后作为 root 用户继续运行所有的命令。它们可能是完全无害的命令，但总有可能...

*   您的命令接触到您的主目录中的文件，从那时起，任何需要对它们进行静默写入的应用程序都会失败
*   一个软件包的安装突然淹没了您的环境，因为您的系统依赖于一个版本的软件包，而现在您用另一个版本覆盖了它
*   一个错误的`rm -rf`从轨道上用核武器攻击你的系统

## 现代 UNIX

老实说，我认为成为 root 的许多问题都应归咎于 MacOS。这是一个多用户系统(UNIX)，被称为单用户系统(每个人都有自己的笔记本电脑，只有一个帐户)。这意味着人们习惯于没有可怕的副作用。

举个例子，关于修复自制软件的问题，来自马嘴的话是:

```
sudo chown -R $USER:admin /usr/local 
```

Enter fullscreen mode Exit fullscreen mode

好吧，如果没有人分享你的电脑(比如你的 MacBook ),这没什么，但是如果你在多用户系统(比如你的网络服务器)上运行，这种黑客行为绝对是可怕的。

我也向前跳，所以让我们后退一点...

## 为什么`su`是个问题？

没有 root 的密码不会让你成为 root。这意味着每个需要 root 访问权限的人都必须知道密码，也就是说*共享*，这意味着如果密码改变了，所有这些用户都必须被告知密码被改变了什么，这通常意味着一大堆不安全因素，因为人们有发送垃圾消息的习惯，

> Yo tobes，我的保险箱的根密码现在是“333 n0 clock”

根据您所处的环境，行为会有所不同。如果您运行`su - goblinking`，那么您将有效地以该用户的身份登录，并可以访问他们所有的别名以及他们在 shell 配置中设置的内容。如果你漏掉了负号，你就不会了。意外的事情会发生，意外的事情是危险的事情。

## 为什么说`sudo`是个好主意？

### 问责

如果你的名字没写下来，你就不能进来

```
$ whoami goblinking
$ sudo run-malicious-script
Password: ********
goblinking is not in the sudoers file. This incident will be reported. 
```

Enter fullscreen mode Exit fullscreen mode

每一个`sudo`命令都在你的历史中，并且被记录为由你运行。
如果*托比*和*gobling*都以 root 用户身份登录，我们如何判断哪个用户运行了恶意脚本？我们不能。嗯，不容易。好吧，好吧，我们可以检查一下 ttys，看看门垫下面，摆弄一下监视车里的把手。嘘。为了这篇文章的目的，我们不能告诉。
`sudo`给我们一个审计线索。这也意味着每个用户可以检查他们自己的历史，并记住他们上周做了什么，而不会与其他分享根体验的人混淆。

### 你不必分享你的 root 密码

`sudo`的存在是为了用`su`解决这个问题。

## `sudo`有哪些好的做法？

`sudo`将使用配置的编辑器编辑 ( `-e`)文件。看看环境变量`EDITOR`、`SUDO_EDITOR`和`VISUAL`，反正配置起来很有趣。

```
$ # Instead of this...
$ sudo nano /etc/nginx/sites-available/default
$ # Do this...
$ sudo -e /etc/nginx/sites-available/default 
```

Enter fullscreen mode Exit fullscreen mode

`sudo`可以给你一个根 shell，而不必运行额外的进程。

```
$ # Instead of this...
$ sudo su
$ # Do this...
$ sudo -i 
```

Enter fullscreen mode Exit fullscreen mode

`sudo`允许您以另一个用户的身份运行单个命令。

```
$ # Instead of this...
$ su
Password: ********
# su goblinking
$ ./my-hilarious-jape.sh
$ exit
$ # Do this...
$ sudo -u goblinking ./my-hilarious-jape.sh
Password: ******** 
```

Enter fullscreen mode Exit fullscreen mode

## 没什么锤子。

我知道我之前说了什么。

[![I've come to fix the server](img/1834efb1797b6272d097f148b5f67178.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7mZtkran--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5mw0ixyhj8rdbqe8mkpi.jpg)

如果你从互联网上复制并粘贴的某个命令不起作用，不要只是把`sudo`推到它前面，然后寄希望于它；搜索程序的名称和您看到的错误信息。
当然，你可能应该以 root 用户身份运行它，但也有很大的可能你不需要这样做，而且你正试图让你的问题屈服，而不是修复它。

## 亦见

[![elabftw](img/88f40972017717829facc61497b0ba7f.png)](/elabftw) [## 停止使用 sudo pip 安装

### eLabFTW Oct 17 ' 182 分钟读取

#python #security #goodpractices #dependencies](/elabftw/stop-using-sudo-pip-install-52mn)[![brylie](img/009333b87cbc9a74cd2dd7c53ce65a2c.png)](/brylie) [## 不要将“sudo”与 NPM“install”一起使用

### bry lie Christopher Oxley 1919 年 4 月 9 日 1 分钟阅读

#javascript #npm #security](/brylie/don-t-use-sudo-with-npm-install-56p9)

[来自 sudo 官方网站](https://www.sudo.ws/intro.html)的介绍。

Jimi Filipovski 在 Unsplash 上拍摄的封面图片。
由我锤炼时间形象。
鲍伊图片由...环境资源管理（Environmental Resources Management 的缩写）...我在某个地方找到的。
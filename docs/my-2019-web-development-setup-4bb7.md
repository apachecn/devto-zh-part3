# 我的 2019 网络开发设置

> 原文：<https://dev.to/jonathanbossenger/my-2019-web-development-setup-4bb7>

我觉得像这篇文章这样的文章非常有趣，一部分是因为看到其他开发人员每天使用的东西总是很有趣，另一部分是因为我看到越来越多的开发人员使用我最喜欢的操作系统 Ubuntu 进行开发。

随着最近 Ubuntu 19.04(又名 Disco Dingo)T1 的发布，我抓住了升级我的笔记本电脑并试用它的机会。这让我有机会记录我需要重新安装的应用程序和工具。希望你能在这里找到有用的东西。

作者指出，这个列表只包括我必须在基本的 Ubuntu 系统上安装的软件，或者我经常使用的云服务。如果你想知道我用什么来播放音乐、浏览图片或编辑电子表格，我会用 Ubuntu 安装的任何东西。

### 浏览器

1.  Chrome 浏览器——最近我读了越多关于谷歌的书，我就越想把火狐浏览器作为我的主要浏览器。
2.  Firefox -这实际上是预装在 Ubuntu 上的，但值得提醒大家它有多棒。在 Chrome 出现之前，Firefox 是网络开发者的首选浏览器。

### 发展环境

1.  数字海洋已经成为基于 Linux 的设置教程的一个很好的资源，所以我通常只需要[按照他们提供的最新版本的 Ubuntu 设置教程](https://www.digitalocean.com/community/tutorials/how-to-install-mysql-on-ubuntu-18-04)中的步骤操作。
2.  如果我需要任何特定的东西(nginx，或者 PHP/MySQL 的旧版本)，我会旋转一个流浪者[便当盒](https://app.vagrantup.com/bento)，里面有任何 Linux 需要的味道。
3.  [Homestead](https://laravel.com/docs/5.8/homestead)——我现在不怎么用这个了，但它仍然是一个很棒的工具。

### 编辑

1.  虽然我大部分时间都在使用 PHP 代码，但是 PHPStorm 不仅仅是一个 PHP IDE，所以我很少不能用它编辑文件。
2.  [Notepadqq](https://notepadqq.com/s/) - Notepadqq 是 Notepad++的 Linux 版本，我在 Windows 上用它作为记事本的替代品。

开发工具(GUI)

1.  PHPMyAdmin -从我开发 PHP 的第一天起，我就一直在使用 PHPMyAdmin。这是对数据库做小改动的最快方法
2.  [MySQL Workbench](https://www.mysql.com/products/workbench/) -一个更健壮的数据库建模工具
3.  捕捉所有通过本地网络服务器发送的邮件。我发现了一个很棒的 [Ubuntu 设置文章](https://www.lullabot.com/articles/installing-mailhog-for-ubuntu-1604)，4 年后仍然有效。

开发工具(CLI)

1.  我有一个用于公共项目的 [GitHub](https://github.com/) 账户和一个用于私人项目的 [GitLab](https://gitlab.com/) 账户。
2.  [颠覆](https://subversion.apache.org/)——因为 WordPress.org。
3.  [Composer](https://getcomposer.org/)-PHP 的包管理器。
4.  PHP unit-PHP 的测试框架。
5.  PHP 代码嗅探器(code sniffer)-我已经变得固执于确保我的代码符合编码标准。我使用 [WordPress 编码标准](https://github.com/WordPress-Coding-Standards/WordPress-Coding-Standards)开发 WordPress，使用 [PSR-1](https://www.php-fig.org/psr/psr-1/) 开发其他 PHP 项目。
6.  WP-CLI-WordPress 的命令行界面。我最近发现了由 [Alain Schlesser](https://github.com/schlessera) 编写的 wp-cli-psysh，这使得 wp shell 命令看起来很有趣。
7.  我不倾向于使用任务运行器来缩小 JavaScript 和/或 CSS(我知道，我知道)，PHPStorm 已经内置了对通过 YuiCompressor 来做这件事的支持。
8.  [NodeJS](https://nodejs.org/en/) -安装 YuiCompressor(大部分)
9.  这是一个伟大的 CLI 应用程序，用于生成本地可信的 SSL 证书。通过安装...
10.  [自制](https://docs.brew.sh/Homebrew-on-Linux)——纯粹为了安装 mkcert
11.  [Pipe Viewer](http://www.ivarch.com/programs/pv.shtml)——一个监控数据通过管道的进度的工具。我用它来查看 mysqldump 命令的进度。

### API 工具

1.  [Postman](https://www.getpostman.com/) -用于测试 API 请求
2.  SOAP UI &我第一次使用 SOAP API 时，我使用了 SOAP UI，从那以后我一直坚持使用它

### 沟通

1.  [Slack](https://slack.com/)—[cas tos](https://castos.com/)团队、 [WPSouthAfrica](http://wpsouthafrica.org/) 制作 WordPress 社区、[可编码](https://codeable.io/)专家社区都通过 Slack 聊天。

### 生产力/跟踪

1.  [WakaTime](https://wakatime.com/) -记录编写代码所花费的时间。当我忘记我在做什么的时候有用
2.  [Toggl](https://toggl.com/) -时间追踪工具。使用这个工具，我在时间追踪方面肯定有所进步，如果你是按小时收费的话，这是至关重要的。

### 多媒体

1.  我还没有在 Ubuntu 上找到一个更好的应用来录制截屏视频
2.  [Audacity](https://www.audacityteam.org/) -音频编辑器，主要用于编辑 WPHackerCast 剧集。
3.  video 我想我已经有 10 多年没用过别的视频播放器了
4.  [awesome 截图](https://www.awesomescreenshot.com/) - Chrome 浏览器扩展，用于快速抓取浏览器截图。

### 密码管理

1.  如果我忘记了我的主密码，我不知道该怎么办！
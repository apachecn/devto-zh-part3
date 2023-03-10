# logrotate 初学者指南。

> 原文：<https://dev.to/rshiva/what-the-hell-is-logrotate-hcl>

现在你已经为你的 web 应用程序安装了[和](https://dev.to/rshiva/welcome-to-nginx-part-i-58mn)[设置](https://dev.to/rshiva/welcome-to-nginx-part-ii-32km) Nginx。你的网站流量很大，所有的日志都写在一个文件里。这对于在文件上执行特定任务(如搜索和归档日志)可能很麻烦。如果能把每天的日志写在一个单独的文件上，压缩旧的日志，并把它单独归档，那就不容易了。这将有助于我们优化硬盘空间。

## 欢迎来到 logrotate。

Logrotate 是 ubuntu 内置的。使用 Logrotate，我们可以自动旋转、压缩、删除和邮寄文件。每个文件可以每天、每周或每月轮换一次，或者在文件变得过大时轮换。

主 logrotate 配置在 **/etc/logrotate.conf** 中。如果你查看配置了许多默认配置文件。在我们深入配置之前，您应该在文件中看到下面一行。

```
include /etc/logrotate.d/ 
```

Enter fullscreen mode Exit fullscreen mode

让我们创建一个单独的配置文件，以便于维护，而不是将所有的进程日志写入单个文件。首先，我们需要在 **/etc/logrotate.d/** 中创建一个 nginx conf 文件

```
cd /etc/logrotate.d/
sudo touch nginx 
```

Enter fullscreen mode Exit fullscreen mode

让我们将下面的基本配置写入该文件并保存它。我将向您介绍每一行的功能。

```
/etc/nginx/log/*.log {
  daily
  rotate 15
  missingok
  compress
  delaycompress
  notifempty
  dateext
  dateformat .%Y-%m-%d
  create 664 www-data www-data
  sharedscripts
  postrotate
    [ ! -f /var/run/nginx.pid ] || kill -USR1 `cat /var/run/nginx.pid`
  endscript
} 
```

Enter fullscreen mode Exit fullscreen mode

**/etc/nginx/log/*。log** :您想要旋转的日志文件的路径。
**每日**:每日轮换。
**size**:(100K/100m/100g)每当文件的大小按照配置增加时，这将对日志进行日志旋转。
**旋转 15** :只保留最后 15 个日志文件，其他将被删除。如果旋转计数为 0，则删除旧版本而不是旋转
**missingok** :如果日志文件丢失，则移动到下一个文件而不抛出错误。
**nomissingok** :如果日志文件不存在，抛出错误。
**压缩**:默认情况下压缩旋转后的文件，使用 gzip 和以. gz 结尾的文件格式。
**延迟压缩**:延迟压缩顾名思义，延迟压缩会延迟日志文件的压缩，这样在重启或重新加载后，以前的进程仍会写入旧的日志文件，这样我们就不会错过任何日志。只有在配置中有**压缩**时，这才会起作用。
**notifempty** :如果文件是空的，不要旋转。
每日分机的格式为 YYYYMMDD。当您需要在特定日期搜索任何内容时，这非常方便。
**创建模式所有者组**:这将在轮换后立即创建一个包含模式、所有者和组的新日志文件。
**dateformat** :指定日期格式扩展为 dateext。
**shared scripts**:shared scripts 与 postrotate 或 prerotate 一起使用，任何添加到 postscript 的脚本只运行一次。
**postrotate** :旋转日志文件后，执行 postrotate 和 endscript 里面的脚本。这个脚本不会终止 nginx 进程，而是发送一个信号来重新加载日志文件，以便在新文件中记录新的请求。
**prerotate** :该块内的脚本将在 logrotate 之前执行。

本教程只是让你开始使用 logrotate。logrotate 中有许多配置，比如将日志邮寄到特定的电子邮件地址等等。我鼓励检查内部文档(人工日志)
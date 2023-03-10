# 自动提取电子邮件附件

> 原文：<https://dev.to/j6s/automatic-e-mail-attachement-extraction-2ja7>

我在圣诞节得到了一个可重复使用的笔记本电脑，它附带了一个简单的应用程序，让浏览你的笔记变得非常容易。笔记的扫描件被转换成 PDF 文件，您可以通过电子邮件发送给自己。

所有这些都很近——但我更喜欢将它们放在一个特殊的文件夹中，并在我的设备之间同步，因为该文件夹是我每周回顾的一部分。

因此，一个想法突然出现在我的脑海中:我能否配置一个邮件客户端，简单地将发送到特殊邮件地址的附件自动保存到一个文件夹中——类似于 Kindle 和 Evernote 的特殊电子邮件地址将内容保存到各自的服务中。事实证明，是有的。

## 你需要什么

*   始终在线的 Linux 计算机，如 raspberry pi、服务器或 NAS
*   `getmail`
    *   Debian 上名为`getmail4`的包。
*   `procmail`
*   `munpack`
    *   Debian 上名为`mpack`的包。

电子邮件是一个定义非常明确的系统，在服务器上非常 unix 化:涉及到多个工具，它们都做一件事情，但是它们都做得很好。在这种情况下，我们的堆栈使用`getmail`通过 IMAP 或 SMTP 从服务器获取邮件，`procmail`过滤这些邮件，`munpack`提取这些邮件的附件。

## 设置`getmail`

为了使用 getmail，我们将在`~/.getmail/getmailrc`中设置一个配置文件，内容如下:

```
[retriever]
type=SimpleIMAPSSLRetriever
server=imap.myserver.com
username=my_username
password=my_password

[destination]
type=MDA_external
path=/usr/bin/procmail

[options]
verbose=0
read_all=false
delete=false
delete_after=0
delete_bigger_than=0
max_bytes_per_session=0
max_message_size=0
max_messages_per_session=0
delivered=false
received=false
message_log=~/getmail.log
message_log_syslog=false
message_log_verbose=true 
```

Enter fullscreen mode Exit fullscreen mode

`[retriever]`部分定义了从哪里获取邮件:在本例中使用 IMAP over SSL 从`imap.myserver.com`使用`mys username`和`my_password`。`getmail`为所有主要的电子邮件协议提供了检索器，可以在[文档](https://www.systutorials.com/docs/linux/packages/getmail-4.54.0/configuration.html#conf-retriever)中找到。

然后,`[destination]`部分定义了什么叫做 MDA——一个**M**ail**D**delivery**A**agent:一个不同的应用程序，它将交付/处理邮件。`getmail`支持几个其他不同的目的地，但是`MDA_external`是我们所需要的，以便将获取的邮件传递给`procmail`。

至此，我们已经成功地配置了`getmail`,以便连接到 IMAP 服务器并从中获取电子邮件。

## 分拣邮件使用`procmail`

`procmail`是一个简单的应用程序，可以用作 MDA 来过滤和分类邮件到不同的邮箱，或者如果它们符合某些标准，就将它们传递给其他进程。它在`.procmailrc`中用作配置文件，看起来像这样:

```
PATH=/usr/bin:/bin:/usr/local/bin:$HOME/bin:$PATH

# Process all mails that arrive for save-notes@mydomain.com
:0
* ^TOsave-notes@mydomain\.com
| munpack -q -t -C $HOME/dropping_area 
```

Enter fullscreen mode Exit fullscreen mode

`procmailrc`格式需要一些时间来适应。- `:0`表示新规则的开始- `* ^TOsave-notes@mydomain\.com`定义必须匹配的条件。在这种情况下，发送到`save-notes@mydomain.com`的所有邮件都被处理。- `| munpack -q -t -C $HOME/dropping_area`定义对该邮件采取的操作。在这种情况下，邮件通过管道传输到`munpack`，它将所有附件提取到`~/dropping_area`

## 完成了

现在，每次执行`getmail`时，都会从服务器获取新邮件，过滤并提取附件。为了定期执行`getmail`，可以为当前用户添加一个简单的 cron job:

```
*/5 * * * * getmail 
```

Enter fullscreen mode Exit fullscreen mode

## 为什么不用 fetchmail？

在研究这个话题时，你会发现很多使用`fetchmail`而不是`getmail`的解决方案。

但是，使用`fetchmail`有一个很大的缺点:`fetchmail`从服务器获取所有未读的消息，并将它们标记为已读。这种行为是不希望的情况下，与'包罗万象'的邮件地址，其中只有一小部分的电子邮件实际上是发送到这个特殊的邮件地址。

`getmail`通过使用消息 id 而不是依赖于服务器上的“已读”状态来跟踪哪些邮件已经被处理，从而不修改服务器本身的任何状态。
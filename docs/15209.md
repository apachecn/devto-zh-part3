# 用屏幕处理服务器任务

> 原文：<https://dev.to/fceruti/juggling-server-tasks-with-screen-4id6>

假设您有一台服务器，您需要在其中运行一个脚本来创建一个备份文件，这将需要 40 分钟，安装一些软件需要 15 分钟，最后，当所有这一切发生时，您需要调查日志文件。

第一种方法是为每个任务打开一个新的终端窗口，并在每个窗口上创建一个新的 ssh 连接。虽然这样做很好，但一个大问题是，如果连接中断，进程就会终止。
第二种方法可能是在后台运行前两种方法，然后浏览您的日志

```
nohup sh backup_db.sh &>/dev/null &
nohup sh install.sh &>/dev/null &
tail /var/logs/my_logs 
```

Enter fullscreen mode Exit fullscreen mode

这将确保即使您的连接中断，进程仍将继续运行，但您要么没有输出数据，要么必须检查某处的一些日志以查看进程是如何完成的。

屏幕来拯救。Screen 基本上是在终端中创建一个终端。当你输入一个空提示符`screen`时，你会看到另一个空提示符，就像你之前看到的一样，但是这个新提示符是不同的:它与你的会话和连接无关，而是与操作系统中的屏幕进程有关。

这意味着，如果您现在可以键入`sh backup_db.sh`，开始看到来自脚本的消息，告诉您备份的进度，然后按下`ctrl + a ctrl + d`(分离)，一切将再次安静下来。您将返回到最初键入 screen 的提示符处。

你关闭你的电脑，回家，吃晚餐，喝啤酒，从你的笔记本电脑上 ssh，用`screen -r`重新连接到屏幕，你将回到备份脚本发送的日志消息，它一直在为你工作。

回到最初的问题。我处理手头任务的方式如下:

```
# Start screen
> screen

# Run backup scrip
> sh backup_db.sh

# Create a new screen
# ctrl + a ctrl + c

# Install software
> sh install.sh

# Detach from screen
# ctrl + a ctrl + d 
```

Enter fullscreen mode Exit fullscreen mode

我终于可以自由地调查日志中的那个奇怪的错误了。过一会儿，当我想检查进程时，我只需键入`screen -r`来重新连接，键入`ctrl + a ctrl + n`来循环显示打开的屏幕。
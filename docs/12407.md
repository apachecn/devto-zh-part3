# Crontab 命令删除自身

> 原文：<https://dev.to/dcblog/crontab-command-to-delete-itself-1bph>

今天有人问我，你能不能只运行一次 cronjob。我最初的回答是不可以，因为你至少可以每年运行一次 cronjob。

但是使用 cron 命令，您可以在 cron 被执行后删除它，这样 cronjob 运行，然后删除自己，导致它只运行一次，完美！

假设我想调用一个脚本:

```
somescript.sh 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我希望这个命令一完成就被删除:

首先，运行命令，如上所示。

然后使用 **crontab -l** 打开 crontab。

grep -v 搜索文件，即寻找命令。

用 **| crontab -** 找到它关闭它。

# 综合起来看:

```
somescript.sh; crontab -l | grep -v 'somescript.sh' | crontab - 
```

Enter fullscreen mode Exit fullscreen mode
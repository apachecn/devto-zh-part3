# 第 11 天-快速提醒通知- 100 天 100 个 python 脚本

> 原文：<https://dev.to/ganesh/day-11-quick-reminder-notification-100-days-100-python-scripts-1heo>

#### [链接到该系列以前的帖子](https://dev.to/ganesh/100-days-100-python-scripts-challenge-3fc)

## 第 11 天:通知 _ 用户

用给定的标题和消息通知用户的单行脚本。借助 cron 或 python 的日程模块
来提醒你的日常任务可能会有所帮助

```
import os,constants

# if you want the notification to be triggered at a specific time, user cron or schedule from python
# Though I prefer using Node-red for scheduling I am not gonna use other methods here. 
os.system('notify-send "'+constants.NOTIFY_TITLE+'" "'+constants.NOTIFY_MESSAGE+'"') 
```

Enter fullscreen mode Exit fullscreen mode

请访问我的 Git Repo 查看所有前一天的挑战。

[https://github.com/ganeshraja10/automated-python-scripts](https://github.com/ganeshraja10/automated-python-scripts)
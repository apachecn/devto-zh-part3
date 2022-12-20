# 第 12 天-自动恢复 vlc 中上次播放的视频- 100 天 100 个 python 脚本

> 原文：<https://dev.to/ganesh/day-12-automatically-resume-last-played-video-in-vlc-100-days-100-python-scripts-fo6>

## 第 12 天:简历 _vlc

如果你对一次又一次地打开最近播放的视频感到厌烦，这是给你的剧本。你可以设置这个脚本，这样每当你打开你的系统时，它会自动恢复上一个暂停的 vlc 视频。

```
import configparser
from constants import configFilePath #Vlc configuarion file path import os, urllib.parse

configParser = configparser.RawConfigParser()   

configParser.read([configFilePath])
recent=configParser["RecentsMRL"]

last_watched=recent["list"].split(",")
time_list=recent["times"].split(",")

if len(last_watched) >0 and len(time_list):
    p=(urllib.parse.urlparse(last_watched[0]))
    finalPath = urllib.parse.unquote(p.path)
    last_stopped_seconds=int(time_list[0])/1000
    if last_stopped_seconds >5:
        last_stopped_seconds=last_stopped_seconds-5 # Go back 5 secs
    os.system("vlc --start-time="+str(last_stopped_seconds)+" '"+finalPath+"'") 
```

Enter fullscreen mode Exit fullscreen mode

请访问我的 Git Repo 查看所有前一天的挑战。

[https://github.com/ganeshraja10/automated-python-scripts](https://github.com/ganeshraja10/automated-python-scripts)
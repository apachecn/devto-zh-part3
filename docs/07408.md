# 第 9 天——检查网站是否正常运行——100 天 100 个 python 脚本

> 原文：<https://dev.to/ganesh/day-9-check-a-site-is-up-or-not-100-days-100-python-scripts-41c7>

## 第九天:检查 _ 现场 _ 直播

该脚本检查站点在给定的时间间隔内是否处于活动状态。如果停机，它会发送通知。

```
import requests,os,time

SITE_URL=""
SLEEP_SEC=300 #5 mins while True:
    response=requests.get(SITE_URL)
    print(response)
    if response.status_code!=200:
          os.system('notify-send "Website Down" "'+SITE_URL+'is down"')
          break
    time.sleep(SLEEP_SEC) 
```

Enter fullscreen mode Exit fullscreen mode

请访问我的 Git Repo 查看所有前一天的挑战。

[https://github.com/ganeshraja10/automated-python-scripts](https://github.com/ganeshraja10/automated-python-scripts)
# 第 15 天-显示随机 Reddit 帖子- 100 天 100 个 python 脚本

> 原文：<https://dev.to/ganesh/day-15-display-random-reddit-posts-100-days-100-python-scripts-43cd>

## 第十五天:notify_random_reddit

这个脚本将从你的收藏列表中随机选择一个 sub_reddit，并随机选择一个热门帖子并通知它。你可以把它添加到 cron，每小时随机获取一次帖子。

```
import json,requests
from random import randint,choice
import os,constants

subreddit_list= constants.fav_subreddit_list # List of favorite sub reddit name 
subreddit=choice(subreddit_list)

url=r"https://json.reddit.com/r/"+subreddit+r"/top/?sort=top&t=day"

headers = {'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/39.0.2171.95 Safari/537.36'}
data=json.loads(requests.get(url,headers=headers).text)

response_data=data["data"]
count= int(response_data["dist"])

random_post=(response_data["children"][randint(0,count)]["data"])

text=random_post["selftext"]
subreddit=random_post["subreddit"].title()

os.system('notify-send "'+subreddit+'" "'+text+'"') 
```

Enter fullscreen mode Exit fullscreen mode

请访问我的 Git Repo 查看所有前一天的挑战。

[https://github.com/ganeshraja10/automated-python-scripts](https://github.com/ganeshraja10/automated-python-scripts)
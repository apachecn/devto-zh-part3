# 公开网络意向以发布#100DaysOfCode 进度

> 原文：<https://dev.to/cferreirasuazo/open-web-intent-to-tweet-100daysofcode-progress-3hob>

[![](img/5df702aaf48bdd6285abe4cd20fb0456.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--O40BANW9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tcf7v4vrz7ki59urwkjl.jpeg) 
我和一个朋友在谈论他在做#100DaysOfCode 挑战赛时遇到的一个问题。几天后，他会忘记在推特上发布自己的进展。他问我有没有办法打开 twitter，在特定时间发布自己的进度。我有了使用 NodeJs 的想法，并在 Linux 中创建一个脚本和一个 cron 作业来在特定时间打开 tweeter，但我们忘记了这个项目。

3 个月后，我用 python 写了一个脚本。下面是它的作用:

1 -它加载一个. json 文件，其中包含代表你在挑战中天数的计数器，如果它不退出，它将创建一个新的，并从 1 开始。

2-使用日期时间模块获取当前日期

3-创建一个包含实际日期的 URL，#100DaysOfCode hashtag，挑战中的当前日期和第二天的增加日期。使用 webbrowser 模块，在默认浏览器中打开一个 web intent。

[![It will opens something like this](img/3ad60dd997844679e4b33595cf344561.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OAn8dDDu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yajadix8zi2pqonjw0jm.png)T3】

```
 """Opens a new Web Intent from Twitter to tweet your #100Daysofcode progress"""

import webbrowser 
from datetime import datetime
import os
import json

def save_day_count(file,count):
#Saves count for the next day
    try:
        with open(file,"w") as file_json:
            json.dump(count,file_json)
    except Exception as e:
        print(e)

def getting_day_count(file):
        #Check if file with current day exist
    if os.path.isfile(file):
        try:
            with open(file) as f_obj:
                #loads current day
                days = json.load(f_obj)
        except FileNotFoundError as ex :
                print(ex)
        else:   
        #Incriases one and saves for the next day
            days += 1
            save_day_count(file,days)
            return days    
    else:
        #Creates a new file if it doesn't exist and starts the counter in 1
        days = 1
        save_day_count(file,days)
        return days

days = getting_day_count("counter.json")
date = str(datetime.now().date()) #Gets current date
url = "https://twitter.com/intent/tweet?hashtags=100DaysofCode%0A" + date + "[Day " +  str(days) +"]"
webbrowser.open_new_tab(url) 
```

如果你和我一样，你不必再忘记发推特来记录你的进步

喜欢到[码](https://github.com/cferreirasuazo/tweet-100days)

如果你喜欢它，分享它，或者没有留下反馈，我会很感激。

编码快乐！！！
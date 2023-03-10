# 统计您的 Github 回购视图

> 原文：<https://dev.to/lbonanomi/tallying-your-github-repo-views-3j56>

你:是一名数据科学家，收集 Github 访客流量统计数据进行分析。我:又渴又没有安全感。
我们都对统计 Github 所有回购的独立访客日志感兴趣。

### 采集交通数据

Github 提供了一个 [REST 端点](https://developer.github.com/v3/repos/traffic/)来查询引用者、最受欢迎的文件、视图(全部或唯一的)和克隆流量，大多数配置良好的 Linux 主机提供了`curl`和`jq`实用程序来轮询和解析返回的 JSON。

```
#!/bin/bash

BUFF=$(mktemp)

# Query the Github `user` endpoint to get a list of the user's repos. 
# This is neater than hard-coding a username or URL into this script.
#
MYREPOS=$(curl -sn https://api.github.com/user | jq .repos_url | tr -d '"')

# Query $MYREPOS for a list of repository URLs and mutate them into 
# `https://api.github.com/$user/$repo/traffic/views` 
#
curl -sn $MYREPOS | jq .[].url | tr -d '"' | while read URL
do curl -sn "$URL/traffic/views" | jq -c .views[] | while read view;
    do
        # Query the repo's `views` URL and parse returned JSON 
        # for unique-visitor counts by date

        VIEWDATE=$(echo "$view" | jq .timestamp | awk -F"T" '{ print $1 }')
        VIEWERS=$(echo "$view" | jq .uniques)

        echo "$VIEWDATE, $VIEWERS, $URL" | tr -d '"'
    done
done > $BUFF

# Now we should have a temp file that looks like:
#
# 2019-05-11, 1, https://api.github.com/repos/lbonanomi/go
# 2019-05-12, 1, https://api.github.com/repos/lbonanomi/go
# 2019-05-13, 1, https://api.github.com/repos/lbonanomi/go
#

# Let's leverage `seq` and GNU `date` to make a list of dates for the 
# last 10 days and build a CSV

seq 0 9 | tac | while read SINCE
do
    # datestamp
    date -d "$SINCE days ago" +%Y-%m-%d | tr "\n" "\t"

    # If there's no-data report a '0'
    grep -q $(date -d "$SINCE days ago" +%Y-%m-%d) $BUFF || printf "0"

    # Normally grep-before-awk makes me psychotic, but I think this is clearer.
    grep $(date -d "$SINCE days ago" +%Y-%m-%d) $BUFF|awk -F"," '{t=t+$2} END{print t}'
done

# Give a hoot and delete temp files when they're done.
rm $BUFF 
```

运行我们的脚本应该会给我们一个 tsv 格式的在过去 10 天内访问我们个人 Github 的独立访问者的计数:

```
2019-05-16  5
2019-05-17  9
2019-05-18  1
2019-05-19  2
2019-05-20  0
2019-05-21  2
2019-05-22  2
2019-05-23  8
2019-05-24  3
2019-05-25  4 
```

用[火花](https://github.com/holman/spark)图或者[线条画](https://github.com/lbonanomi/scripts/blob/master/braille_chart.sh)看起来不会很帅吗？
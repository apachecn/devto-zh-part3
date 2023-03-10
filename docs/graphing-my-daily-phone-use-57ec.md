# 绘制我的日常电话使用情况

> 原文：<https://dev.to/rmhogervorst/graphing-my-daily-phone-use-57ec>

我看手机多少次？我在手机上设置了一个[小程序来统计屏幕激活次数，并记录到一个文件中。在这篇文章中，我展示了哪里出错了，以及如何绘制结果。](https://dev.to/rmhogervorst/logging-my-phone-use-with-tasker-33h9)

## 数据

我在手机上设置了一个小程序，每天计算我使用手机的次数(具体来说，就是计算屏幕被激活的次数)。

我的数据是这样的:

```
"screen_log";"1-19-19";"17.30";"7"
"screen_log";"1-19-19";"17.33";"8"
"screen_log";"1-19-19";"17.36";"9"
"screen_log";"1-19-19";"17.38";"10" 
```

Enter fullscreen mode Exit fullscreen mode

为了解决逗号的使用和可能出现的问题，我在手机上设置了一个程序来写一个“；”-记录 screen_log、日期、时间和 screen_count 当前值的独立文件。每天大约 12 点钟，它把计数器归零。我觉得比较不同的日子会很酷。

## 出现了问题

我在 1 月 19 日 17:00 左右开始收集数据，所以第一天已经过去一半了。出于我无法理解的原因，有时系统日期以美国式的年月日记录，有时以世界其他地方的年月日记录。我希望我能把它设置为年-月-日(ISO 8601)。

## 读入数据

我使用 read_csv2，它期望“；”作为分隔符，从不将文本转换为因子。这个特殊的文本文件没有标题，所以我告诉 R 如何称呼这些列。

```
library(tidyverse) # what else
screenlog <- read_csv2("data/screenlog.csv",col_names = c("type","date","time","counter")) 
```

Enter fullscreen mode Exit fullscreen mode

## 数据清理

我必须处理不同的时间格式，所以我设置了一个正则表达式，只适用于 1 月 1 日，如果它检测到-01-19，它就取出之前的数字，如果它检测到另一个变量，它就取出第二部分。我将日期和时间组合成一个时间戳，并提取小时和分钟，然后将小时和分钟组合成 HMS 时间类。最后，我删除超过 23 小时的所有内容，因为在此期间，计数器被重置。

```
screenlog <-   
  screenlog %>% 
    mutate(
        day = case_when( 
            str_detect(date, "[0-9]{1,2}-01-19") ~ 
                str_replace(date, "([0-9]{1,2})-01-19","\\1"),
            str_detect(date, "1-[0-9]{1,2}-19") ~ 
              str_replace(date, "1-([0-9]{1,2})-19", "\\1") ,
            TRUE ~ NA_character_
            ),
        timestamp = paste0("2019-01-",day, " ",time),
        timestamp = as.POSIXct(timestamp,tz = "GMT", format = "%Y-%m-%d %H.%M"),
        hours = str_replace(time,"\\.[0-9]{1,2}", "") %>% as.numeric(),
        minutes = str_replace(time,"[0-9]{1,2}\\.", "") %>% as.numeric(),
        time = hms::hms(hours = hours, minutes = minutes)
        ) %>% 
    filter(hours < 23) 
```

Enter fullscreen mode Exit fullscreen mode

## 看起来怎么样？

先来个概述:

```
screenlog %>% 
    ggplot(aes(timestamp, counter, color = day))+
    geom_step()+
    ggtitle("Times I looked at my screen during vacation", subtitle = "daily values")+
    theme_light() 
```

Enter fullscreen mode Exit fullscreen mode

[![Daily cumulative screen looking values](img/e9b04158c833e4ec47f654f601d7424f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NI1SRQpd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.rmhogervorst.nl/post/2019-01-28-graphing-my-daily-phone-use_files/screenlogplot_week.png)

每日累计屏幕显示值

但是这很难比较，所以我也将它们重叠显示:

```
screenlog %>% 
    ggplot(aes(time, counter, group = day, color = day))+
    geom_step()+
    ggtitle("Times I looked at my screen during vacation", subtitle = "overlay plot")+
    theme_light() 
```

Enter fullscreen mode Exit fullscreen mode

[![overlay of cumulative screen lookings every day on the same hourly scale](img/be4712173414110b7edd5f539efa8da3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wVCVEHzF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.rmhogervorst.nl/post/2019-01-28-graphing-my-daily-phone-use_files/screenlook_overlay.png)

每天在相同的小时尺度上叠加累积屏幕观看

# 鳍

唯一剩下的问题是:25 号那天我做了什么，在屏幕上看了 326 次？计数中有错误吗？我真的很无聊吗？我拍了很多照片吗？我在马拉加的植物园，用手机拍了很多照片。
# 一个关于制作日历应用程序来练习最佳工作技术“时间盒”的故事

> 原文：<https://dev.to/nagamine/a-story-about-making-a-calendar-application-for-practicing-the-best-work-technique-time-box-4a16>

## 给那些愿意尝试的人

▼在这里下载(针对 mac / dmg 格式)▼
[https://S3-AP-northeast-1 . amazonaws . com/Watari/downloads/Darwin/Watari-0 . 0 . 7 . dmg](https://s3-ap-northeast-1.amazonaws.com/watari/downloads/darwin/Watari-0.0.7.dmg)

## 这是我想要显示的生活“谷歌日历”当前演示文稿唯一的桌面顶端

通过时间框让你的日子更有效率最近出版了。

这是一个故事，把时间表直接放在日历上的人肯定会比扩大的待办事项清单做得更好，因为它无法消化，你可以选择它。

我在做这样一个类似管理的时间盒，但是我面临的问题是**“除非我打开一个玩具日历，否则我无法查看当前的日程安排”**。

在那里

*   如果你把谷歌日历的当前日程安排在桌面上是最好的，不是吗？
*   如果你还能写一个剩余时间倒计时和日历备忘录就好了？
*   如果您能推荐每个活动的网址，会不会很好？

Watari 是一款为 mac 开发的应用程序，采用名为。

## 演示

> 【致被选为最佳工作技能的“时间盒”的哥们】
> ~用谷歌日历管理 Todo，给那些关注生产力的人~
> 
> ▼在这里下载(Mac / dmg 文件)▼[https://t.co/XL5hnpd0uj](https://t.co/XL5hnpd0uj)T5】pic.twitter.com/QkVgobQdkK
> 
> — Tsuyoshi.Nagamine (@nagaminenot_eng) [February 24, 2019](https://twitter.com/nagaminenot_eng/status/1099508587104108544?ref_src=twsrc%5Etfw)

## 技术故事

*   使用[电子眼](https://github.com/SimulatedGREG/electron-vue)作为底座
*   使用当前约会名称搜索 Google 日历 API，查找您在过去同一时间看到的 URL，
*   通过在应用程序中放置 sqlite 3 来查找 Chrome 的历史(可以用 sqlite 3 读取)来搜索 URL(当然，不要担心，因为服务器不会收集 Chrome 的历史。)在用户的 PC 和 Google 日历 API 之间，这是一个用。

### 技术上繁琐

*   构建 sqlite 3 很难构建。我不能在 Windows 上构建。参照[https://stack overflow . com/questions/32504307/how-to-use-sqlite3-module-with-electronic](https://stackoverflow.com/questions/32504307/how-to-use-sqlite3-module-with-electron)解决。
*   自动启动功能的实现有点困难。如果你没有权限，你必须用`catch` `error`代替`false``AutoLaunch.isEnabled ()`。比 sqlite 3 好多了。

就我个人而言，它很方便，我每天都在工作中使用它，但我担心它是否会被打算在其他时间框管理日程的人使用，所以我想尝试一下，并给出我的印象。

▼在这里下载(针对 mac / dmg 格式)▼
[https://S3-AP-northeast-1 . amazonaws . com/Watari/downloads/Darwin/Watari-0 . 0 . 4 . dmg](https://s3-ap-northeast-1.amazonaws.com/watari/downloads/darwin/Watari-0.0.4.dmg)

▼印象这里举报一个 bug▼
[https://twitter.com/nagaminenot](https://twitter.com/nagaminenot)

※本文已由谷歌翻译翻译。原文如下。

* * *

## 面向想先试一下的人

▼ダウンロードはこちら(mac 向け/dmg 形式)▼
[https://S3-AP-northeast-1 . amazonaws . com/Watari/downloads/Darwin/Watari-0 . 0 . 4 . dmg](https://s3-ap-northeast-1.amazonaws.com/watari/downloads/darwin/Watari-0.0.4.dmg)

## 我的人生只想要在桌面上“显示”谷歌日历上的“当前计划”

[最能提高生产率的工作技术是时间盒。 那个方法和优点是什么？ |生活黑客[日本版]](https://www.lifehacker.jp/2019/02/make-your-days-more-productive-by-timeboxing.html) 的报道最近被公开了。

比起无法消化的臃肿的 Todo 列表，直接在日历上输入计划更能切实地完成，可以更好地取舍。

虽然我也进行了那样的时间盒式的管理，但是遇到了**“不逐一打开日历就无法确认现在的安排”**的问题。

于是

*   **在桌面上，把谷歌日历的现在计划排在最前面就好了吗？**
*   **要是还能做剩下时间的倒计时和日历笔记就好了？**
*   **如果能顺便按计划推荐一下 URL 的话我会很高兴的吧？**

用这样的思维电路制作的面向 mac 的 APP 是 Watari。

## 演示

[https://twitter.com/nagaminenot/status/1099265041809825792](https://twitter.com/nagaminenot/status/1099265041809825792)

## 技术上的讲述

*   基础使用[electron-vue](https://github.com/SimulatedGREG/electron-vue)
*   使用当前约会名称搜索谷歌日历 API，查找在同一约会的过去时间查看的 URL，然后命令
*   关于 URL 的查找方法，请在 APP 中内置 sqlite3，搜索 Chrome 的历史记录(可以用 sqlite3 读取) (当然没有用服务器收集 Chrome 的历史记录，请放心。 这是在用户的 PC 和谷歌日历 API 之间完成的推荐。 ）

### 技术上很累

*   想要内置 sqlite3 的话，组建起来会很困难。 然后无法在 Windows 上构建。 [参考这一带](https://stackoverflow.com/questions/32504307/how-to-use-sqlite3-module-with-electron)解决了。
*   自动启动功能的安装有点困难。 如果得不到权限的话，必须用`AutoLaunch.isEnabled()`代替`false`而用`error`来代替`catch`。 比 sqlite3 好得多。

因为对我个人来说很方便，虽然每天都在工作中使用，但是因为很在意能不能让在其他时间箱预定管理的人使用，所以请一定要试试看，发表感想。

▼ダウンロードはこちら(mac 向け/dmg 形式)▼
[https://S3-AP-northeast-1 . amazonaws . com/Watari/downloads/Darwin/Watari-0 . 0 . 7 . dmg](https://s3-ap-northeast-1.amazonaws.com/watari/downloads/darwin/Watari-0.0.7.dmg)

▼感想 bug 报告在这里▼
[https://Twitter.com/nagaminenot](https://twitter.com/nagaminenot)

※不是公司制作，而是个人制作的 APP
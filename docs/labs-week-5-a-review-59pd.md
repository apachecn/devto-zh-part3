# 实验第 5 周:回顾

> 原文：<https://dev.to/mlanders/labs-week-5-a-review-59pd>

<center>And that's a wrap!</center>

<center>[Training Bot](https://www.trainingbot.co)</center>

* * *

Lambda Labs 绝对是一次美妙的经历，我不可能找到比这更好的合作团队了。

我们项目的最后一周都是在打磨和完成应用程序。上周，我认为这将是一个相当容易的一周。我感到惊讶的是，一周下来，有越来越多的工作要完成。它们要么看起来是小的样式问题，要么是意识到我们应该用另一种方式编写应用程序的整个部分。我想这只是伴随着编写 1.0 版本的应用程序而来的。只需要拿出一些东西，然后在此基础上进行构建。

* * *

本周我做的一个大项目是设置我们的应用程序，当编辑团队成员时，如果电话号码不是完整的 10 位数，则禁用添加团队成员按钮或保存按钮。由于我最初设置电话号码格式的方式，我无法在前端对号码进行验证。我很喜欢编写下面的正则表达式，以便在没有所需电话号码的情况下禁用按钮。

```
 let addDisabled = false;
    if (
      /^$/gm.test(this.state.teamMember.phoneNumber) === true ||
      (/\+1 \(\d{0}/gm.test(this.state.teamMember.phoneNumber) === true &&
        /^\s*(?:\+?(\d{1,3}))?([-. (]*(\d{3})[-. )]*)?((\d{3})[-. ]*(\d{4})(?:[-.x ]*(\d+))?)\S*$/gm.test(this.state.teamMember.phoneNumber) === false)
    ) {
      addDisabled = true;
    } 
```

第一部分检查电话号码串是否为空，如果为空，则禁用`true`。第二部分检查表单字符串是否存在，但为空。最后一个检查所有 10 位数字是否都已输入。

我相信有一个更简单的方法来做到这一点，并可能会回到它来简化和获得适当的手机前端验证。亚历克斯能够在后端获得电话号码的验证，这样当他们被发送到 Twilio 时，就不会使我们的服务器崩溃。

* * *

上周有它的挑战。回想起来，我觉得我们经历的事情很相似。我们这周感觉很好，这一周感觉一件接一件的事情发生了。从混合材质 UI 和样式化组件导致的样式化问题，到将组件键更改为非原始内容导致列表项重复。我认为这是第一次在数组上映射一个键引起问题。我想主要是因为我忘记添加一个键并忽略了警告，或者我映射的项目包含一个 id，而这个没有。

回顾过去 5 周，我对我们团队为制作这款应用所做的工作感到非常自豪。通过阅读他人的许多学习经验为这种规模的应用编写了整个规划过程。

感谢内特为我们的应用程序惊人的视频。

[https://www.youtube.com/embed/bR18ee24Gfk](https://www.youtube.com/embed/bR18ee24Gfk)

* * *

<center>[Training Bot](https://www.trainingbot.co)</center>

[![Training Bot](img/ed4bbb267c487d253d877559b1d77cec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--k78dwJAA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6w6gnma59wupyt1e7zl4.png)

* * *

GitHub 公关公司

# [![](img/375dfcc32199b4dedf2b526645c27ff7.png) 代码清理 #155](https://github.com/training-bot/labs11-trainingBot-FE/pull/155) 

[![mlanders avatar](img/08b5542e5a8cf622899ab46ea13cc571.png) ](https://github.com/mlanders)  **[mlanders](https://github.com/mlanders)** commented on [Apr 18, 2019](https://github.com/training-bot/labs11-trainingBot-FE/pull/155)

清除控制台和纱线启动上的所有通知。

Chrome 开发工具审计:100%可访问性，100%搜索引擎优化，93%最佳实践

[View on GitHub](https://github.com/training-bot/labs11-trainingBot-FE/pull/155)

# [![](img/375dfcc32199b4dedf2b526645c27ff7.png) 档案定价&受限页面 #144](https://github.com/training-bot/labs11-trainingBot-FE/pull/144) 

[![mlanders avatar](img/08b5542e5a8cf622899ab46ea13cc571.png) ](https://github.com/mlanders)  **[mlanders](https://github.com/mlanders)** commented on [Apr 17, 2019](https://github.com/training-bot/labs11-trainingBot-FE/pull/144)

更新了定价页面和注销时受限制的页面，如[https://trainingbot-dev.netlify.com/home/](https://trainingbot-dev.netlify.com/home/)

[View on GitHub](https://github.com/training-bot/labs11-trainingBot-FE/pull/144)

# [![](img/375dfcc32199b4dedf2b526645c27ff7.png) 定价页面&电话号码 regex  #137](https://github.com/training-bot/labs11-trainingBot-FE/pull/137) 

[![mlanders avatar](img/08b5542e5a8cf622899ab46ea13cc571.png) ](https://github.com/mlanders)  **[mlanders](https://github.com/mlanders)** commented on [Apr 17, 2019](https://github.com/training-bot/labs11-trainingBot-FE/pull/137)

忘记为这些创建新的分支。

*   登录页面链接到定价页面。
*   定价页面包括一个基本图表
*   向新团队成员添加电话号码时，添加成员功能将被禁用，直到输入有效号码或未添加任何号码。
*   编辑团队成员电话号码时，除非输入有效号码或根本不输入，否则保存按钮将被禁用

[View on GitHub](https://github.com/training-bot/labs11-trainingBot-FE/pull/137)

# [![](img/375dfcc32199b4dedf2b526645c27ff7.png) 通知页面上格式化的电话号码 #136](https://github.com/training-bot/labs11-trainingBot-FE/pull/136) 

[![mlanders avatar](img/08b5542e5a8cf622899ab46ea13cc571.png) ](https://github.com/mlanders)  **[mlanders](https://github.com/mlanders)** commented on [Apr 17, 2019](https://github.com/training-bot/labs11-trainingBot-FE/pull/136)

通知仪表板上的电话号码现已格式化

[View on GitHub](https://github.com/training-bot/labs11-trainingBot-FE/pull/136)

* * *

GitHub 回购

## ![](img/375dfcc32199b4dedf2b526645c27ff7.png) [训练机器人](https://github.com/training-bot) / [实验室 11-训练机器人](https://github.com/training-bot/labs11-trainingBot-FE)

<article class="markdown-body entry-content p-5" itemprop="text">

# [![Training Bot](img/5b23bd302099126931704bd8f90bc4c0.png)](https://raw.githubusercontent.com/training-bot/labs11-trainingBot-FE/master/Assets/Screenshot1.png)

# 训练机器人

| 使用 Twilio API 发送自动文本和电子邮件通知的单页应用程序。Training Bot 通过向团队发送包含自定义内容的自动通知来协助团队领导 |

## 演示

这里是一个工作现场演示:[https://trainingbot.netlify.com](https://trainingbot.netlify.com)

## 位置

### 仪表盘

我们应用程序的中心位置。在这里，您可以看到您的所有团队成员、训练系列和发出/发送的消息。

[![](img/65665de855e947c46ed037df4a1fa120.png)](https://raw.githubusercontent.com/training-bot/labs11-trainingBot-FE/master/Assets/Screenshot2.png)

### 团队成员

您将在此存储您的所有成员。该信息将用于向特定成员 [![](img/e76f8c1aac917091ef18915ba2ed9cf0.png)](https://raw.githubusercontent.com/training-bot/labs11-trainingBot-FE/master/Assets/screenshot4.png) 发送通知

### 培训系列

训练系列包含您的所有内容和分配的成员 [![](img/f4aea9af70c2381dd4780d71655b3a97.png)](https://raw.githubusercontent.com/training-bot/labs11-trainingBot-FE/master/Assets/Screenshot5.png)

### 轮廓

在个人资料部分，您可以查看/删除您的帐户以及升级您的帐户。[![](img/1d3bec75abc9883b9a1bf31d84e0c25f.png)](https://raw.githubusercontent.com/training-bot/labs11-trainingBot-FE/master/Assets/Screenshot6.png)

## 移动支持

Training Bot 兼容所有尺寸的设备和所有操作系统。[![](img/9287604b1c7426404eb9bb6c6e71cecb.png)](https://raw.githubusercontent.com/training-bot/labs11-trainingBot-FE/master/Assets/Screenshot3.png)

## [用途](https://trainingbot.netlify.com)

### 发展

*   派生存储库
*   创建新的分支(`git checkout -b improve-feature`)
*   在文件中进行适当的更改
*   添加…

</article>

[View on GitHub](https://github.com/training-bot/labs11-trainingBot-FE)

## ![](img/375dfcc32199b4dedf2b526645c27ff7.png) [训练机器人](https://github.com/training-bot) / [实验室 11-训练机器人](https://github.com/training-bot/labs11-trainingBot-BE)

<article class="markdown-body entry-content p-5" itemprop="text">

# <g-emoji class="g-emoji" alias="speech_balloon" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4ac.png">💬</g-emoji>训练机器人 API <g-emoji class="g-emoji" alias="speech_balloon" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4ac.png">💬</g-emoji>

## 介绍

Training bot 允许团队经理按照预定义的时间表向他们的队友发送通知。

## 目录

*   [<g-emoji class="g-emoji" alias="speech_balloon" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4ac.png">💬</g-emoji>训练机器人 API <g-emoji class="g-emoji" alias="speech_balloon" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4ac.png">💬</g-emoji>](https://raw.githubusercontent.com/training-bot/labs11-trainingBot-BE/master/#%F0%9F%92%AC-training-bot-api-%F0%9F%92%AC)
    *   [简介](https://raw.githubusercontent.com/training-bot/labs11-trainingBot-BE/master/#introduction)
    *   [目录](https://raw.githubusercontent.com/training-bot/labs11-trainingBot-BE/master/#table-of-contents)
*   [概述](https://raw.githubusercontent.com/training-bot/labs11-trainingBot-BE/master/#overview)
    *   [什么是训练机器人？<g-emoji class="g-emoji" alias="robot" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f916.png">🤖</g-emoji>T3】](https://raw.githubusercontent.com/training-bot/labs11-trainingBot-BE/master/#what-is-training-bot-%F0%9F%A4%96)
    *   [使命陈述<g-emoji class="g-emoji" alias="scroll" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4dc.png">📜</g-emoji>T3】](https://raw.githubusercontent.com/training-bot/labs11-trainingBot-BE/master/#mission-statement-%F0%9F%93%9C)
*   [用途](https://raw.githubusercontent.com/training-bot/labs11-trainingBot-BE/master/#usage)
    *   [入门](https://raw.githubusercontent.com/training-bot/labs11-trainingBot-BE/master/#getting-started)
*   [数据库表](https://raw.githubusercontent.com/training-bot/labs11-trainingBot-BE/master/#database-tables)
    *   [模式](https://raw.githubusercontent.com/training-bot/labs11-trainingBot-BE/master/#schema)
    *   [用户](https://raw.githubusercontent.com/training-bot/labs11-trainingBot-BE/master/#user)
    *   [培训系列](https://raw.githubusercontent.com/training-bot/labs11-trainingBot-BE/master/#trainingseries)
    *   [团队成员](https://raw.githubusercontent.com/training-bot/labs11-trainingBot-BE/master/#teammember)
    *   [发帖](https://raw.githubusercontent.com/training-bot/labs11-trainingBot-BE/master/#post)
    *   [账户类型](https://raw.githubusercontent.com/training-bot/labs11-trainingBot-BE/master/#accounttype)
    *   [关系表](https://raw.githubusercontent.com/training-bot/labs11-trainingBot-BE/master/#relationaltable)
*   [终点](https://raw.githubusercontent.com/training-bot/labs11-trainingBot-BE/master/#endpoints)
    *   [所有端点](https://raw.githubusercontent.com/training-bot/labs11-trainingBot-BE/master/#all-endpoints)
*   [<g-emoji class="g-emoji" alias="desktop_computer" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f5a5.png">🖥</g-emoji> 数据请求和响应 <g-emoji class="g-emoji" alias="desktop_computer" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f5a5.png">🖥</g-emoji>](https://raw.githubusercontent.com/training-bot/labs11-trainingBot-BE/master/#%F0%9F%96%A5-data-requests-and-responses-%F0%9F%96%A5)
    *   [T2`/api/auth`](https://raw.githubusercontent.com/training-bot/labs11-trainingBot-BE/master/#apiauth)
    *   [T2`/api/users/:id`](https://raw.githubusercontent.com/training-bot/labs11-trainingBot-BE/master/#apiusersid)
    *   [T2`/api/users/:id/training-series`](https://raw.githubusercontent.com/training-bot/labs11-trainingBot-BE/master/#apiusersidtraining-series)
    *   [T2`/api/users/:id/team-members`](https://raw.githubusercontent.com/training-bot/labs11-trainingBot-BE/master/#apiusersidteam-members)
    *   [T2`/api/training-series`](https://raw.githubusercontent.com/training-bot/labs11-trainingBot-BE/master/#apitraining-series)
    *   [T2`/api/training-series/:id`](https://raw.githubusercontent.com/training-bot/labs11-trainingBot-BE/master/#apitraining-seriesid)
    *   [T2`/api/training-series/:id/posts`](https://raw.githubusercontent.com/training-bot/labs11-trainingBot-BE/master/#apitraining-seriesidposts)
    *   [T2`/api/posts`](https://raw.githubusercontent.com/training-bot/labs11-trainingBot-BE/master/#apiposts)
    *   [T2`/api/posts/:id`](https://raw.githubusercontent.com/training-bot/labs11-trainingBot-BE/master/#apipostsid)
    *   [T2`/api/team-members`](https://raw.githubusercontent.com/training-bot/labs11-trainingBot-BE/master/#apiteam-members)
    *   [T2`/api/team-members/:id`](https://raw.githubusercontent.com/training-bot/labs11-trainingBot-BE/master/#apiteam-membersid)
    *   [T2`/api/team-members/assign`](https://raw.githubusercontent.com/training-bot/labs11-trainingBot-BE/master/#apiteam-membersidtraining-series)
    *   [T2`/api/team-members/assign/:id`](https://raw.githubusercontent.com/training-bot/labs11-trainingBot-BE/master/#apiteam-membersidtraining-seriestsid)
    *   [T2`/api/stripe`](https://raw.githubusercontent.com/training-bot/labs11-trainingBot-BE/master/#apistripe)
    *   [T2`/api/stripe/unsubscribe`](https://raw.githubusercontent.com/training-bot/labs11-trainingBot-BE/master/#apistripeunsubscribe)

# 概观

## 什么是训练机器人？<g-emoji class="g-emoji" alias="robot" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f916.png">🤖</g-emoji>

Training Bot 是一个学习应用程序，允许团队领导创建一系列培训，并在预定时间通过文本或电子邮件向指定的学习者提供这些培训。用户将能够添加成员，并将其分配到一组预定的培训开始日期。每个培训都有一个标题、正文和链接。它们应该很小…

</article>

[View on GitHub](https://github.com/training-bot/labs11-trainingBot-BE)
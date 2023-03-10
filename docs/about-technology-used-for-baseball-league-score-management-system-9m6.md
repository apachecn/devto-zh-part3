# 关于棒球联赛成绩管理系统使用的技术

> 原文：<https://dev.to/ckoshien/about-technology-used-for-baseball-league-score-management-system-9m6>

我将介绍个人开发的当前棒球联赛成绩管理系统的技术和正在开发的系统的技术。

# 关于当前的棒球联赛成绩管理系统

[![image](img/e956bd6368b2fec411cd6d4b30655532.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--B3iSPijF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storage.googleapis.com/deviita/upload_images/b08b548caec5795ad40e35af9c85f4c95c0241635d9a3.png)

这是一个 CMS，为日本棒球联盟提供球员成绩。
我从 2015 年 6 月开始开发、运营、添加新功能。

# 功能介绍

## 赛季成绩页面

这个页面是从棒球联盟的官方页面的框架中调用的。
显示除团队排名外的前十名是一个规范。

*   团队排名
*   个人击球组
    *   击球率
    *   打击
    *   本垒打
    *   打点(击球得分)
*   个人投手区
    *   ERA(挣得运行平均值)
    *   胜利次数
    *   救援
    *   三振出局
*   个人无头衔部门
    *   OBP(按基数百分比)
    *   两倍
    *   段塞百分比
    *   四球
    *   (美国)物价管制局(Office of Price Stabilization)

## 玩家个人页面

[![image](img/21bb6fe36003d56f1b458e31bf069d08.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1ZWC9252--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storage.googleapis.com/deviita/upload_images/e40e06bce35e3ceca1be5b957251e7805c024cbc9d874.png)

*   球员介绍(所属球队，标题摘要等。)
*   击球结果(按时间段，按对手球队)
*   投球结果(按周期，按对手球队)
*   上面的图表

该图由 highcharts 实现。

## 团队介绍页面

[![image](img/b2755993becca74b23518cd258465af1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--O1bZPLE9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storage.googleapis.com/deviita/upload_images/7f39cb0c5dca52ee81244c660d3c2ff65c024aef4da4a.png)

*   每个季度的总结果
*   最近的胜利/失败结果
*   队员的总击球成绩
*   队员的总投球成绩

## 统计页面

[![image](img/b6229d2fabaef88a9187397818186a05.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AC0ft8x9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storage.googleapis.com/deviita/upload_images/dff46e83372943224665879750da16be5c024fc71a26e.png) 
我正在使用 highcharts 按赛季和团队实现参与者转换图。

## 所有时期总分页面

联赛本身从 2004 年开始举办，成绩数据从 2010 年输入到 2018 年。布局与季节结果页面相同。

## 持续参与者等级页面

它显示参与者在过去两年中的参与列表。
布局与赛季结果页面相同。

## slack/push pullet(推送通知)协同

有一个用于 slack 和 pushbullet 的推送通知。

*   长期天气预报采集
*   进度调整

# (当前)棒球联赛成绩管理系统中使用的技术

## 后端

*   语言:Java 8
*   FW: SAStruts(在继续运行时停产)
*   数据库:MySQL 5.7
*   AP 服务器:Tomcat
*   WEB 服务器:Nginx

## 前端

虽然基础仍然是 JSP，但我正在进行 API 化和客户端渲染，并且是用 ajax + datatables 动态生成的。
jQuery/[materialize](https://materializecss.com/)/[datatables](https://datatables.net/)
我主要用的是 Noto Sans 日文字体，数字部分是 Roboto 字体。

# 关于棒球联赛成绩管理系统(正在开发中)

我解释了当前的系统。然而，由于 SAStruts 是一种 EOL 技术，目前正在现代开发环境中更新。
从这里开始，我将介绍正在开发的系统。

## 后端(开发中)

*   TypeScript / NodeJS / Express
*   数据库:MySQL 5.7
*   Netlify

# 前端(开发中)

设计继续基于物化，组件使用用户友好的 react-bootstrap。

*   反应堆
*   组件库
    *   [反应-引导](https://react-bootstrap.github.io/)
    *   [反应-引导-表格-下一个](https://react-bootstrap-table.github.io/react-bootstrap-table2/)
*   [chart.js](https://www.chartjs.org/)
*   实现

## 功能仅在开发中的测试版中可用

我实现了当前系统中没有实现的功能。新功能正在我认为的地方实现。

### 击球率分布图

联赛也运行了很久，如果不是干净的正态分布，除了某个正常的数据之外，什么都没有。

[![image](img/188f5b84d13ed4ea384f06aaf5faed72.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pks0Eimt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storage.googleapis.com/deviita/upload_images/aa4dd599478ee4d40f9496e64b955de75c025692ec31f.png)

### 各玩家能力图表

虽然标准差计算过程有些繁重是一个难点，但它比句子更能雄辩地说明运动员的性格。

[![image](img/eecdb7218170adaa0c4d7ddf03fdb843.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--D2_Otd6F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storage.googleapis.com/deviita/upload_images/7dde7d39177c2fc8ac81e6e469fa63b55c0256b957aab.png)

# 任务

## API 响应缓慢

因为我使用的是低规格的服务器，API 响应大约需要 1 秒钟。我打算更换服务器。

## 没有设计感

UI 的感觉是毁灭性的，因为业务系统的体验是漫长的。UI 仍在试错中。

## 我没有写测试代码

这基本上是人体试验...最近我开始使用 Jest 来编写测试代码....

## 有许多页

虽然它成为上面提到的 UI 的故事，但由于它有许多页面，所以使用什么样的菜单以及如何处理导体...那是。

# 原帖

[关于棒球联赛得分管理系统中使用的技术](https://crieit.net/posts/0431d68ab5279f65797e41cfc18a0eb5)
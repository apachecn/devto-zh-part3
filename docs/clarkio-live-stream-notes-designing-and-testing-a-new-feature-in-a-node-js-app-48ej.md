# Clarkio Live Stream Notes:在 Node.js 应用程序中设计和测试新功能

> 原文：<https://dev.to/clarkio/clarkio-live-stream-notes-designing-and-testing-a-new-feature-in-a-node-js-app-48ej>

以下是我们一起生活代码的过去的生活流的笔记和细节。这些流背后的想法是，它们提供了遇到问题并解决问题或努力解决问题的真实世界的例子。

## 2019 年 5 月 31 日星期五

## 流回放

*   [抽动](https://www.twitch.tv/videos/432550248)
*   [YouTube](https://youtu.be/8--RNqksYzE?t=720)

## 目标

✅热身/叙旧聊天
✅今日咖啡杯
✅泼塞
✅椅子外观
✅凉亭外观
✅从只是聊天转变类别➡科学&科技
✅拉伸/休息之间~1-2 小时刻度
添加！马克/！向[流笔记工具](https://github.com/clarkio/stream-note-taker)记录聊天命令，并测试
✅关闭和突袭 [@h3h394](https://twitch.tv/h3h394)

## 结果

*   设计并考虑了新的需求！马克/！注释特征
*   做了大量工作来计算流正常运行时间(日期/时间数学)

## 我们学到的东西

*   如何用 moment.js 计算两个 UTC 日期之间的时差
*   [moment.js 库](https://momentjs.com/docs/)
*   [date-fns library](https://date-fns.org/)

## 下一步

*   为新的`stream.js`函数`getStreamStartTime`和`getStreamUptime`编写测试

## 段

| 时间戳 | 主题 |
| --- | --- |
| 00:09:00 | 开始吧 |
| 00:12:00 | 录音已取消静音 |
| 00:38:20 | 凉亭外观 |
| 00:40:05 | 椅子外观 |
| 00:46:00 | VS 代码片段 |
| 00:55:00 | 开始编写新的特性和测试 |
| 00:58:27 | 为编程任务定义“完成”( 7D) |
| 01:02:19 | 设计我们想要添加到流笔记记录的功能 |
| 01:15:00 | VS 代码自动更新在重命名时导入 |
| 01:25:25 | 又是椅子 |
| 01:33:30 | 我爱上了 LIGMA 特技 |
| 01:42:00 | 从钓鱼中恢复并回到研究 Streamelements API |
| 01:45:00 | 使用 Postman 和其他工具作为 REST 客户端测试 http 请求 |
| 01:48:30 | 试用 [REST 客户端 VS 代码扩展](https://marketplace.visualstudio.com/items?itemName=humao.rest-client&WT.mc_id=twitch-social-brcl) |
| 02:01:20 | 谈论 HTTP 请求中的基本认证以及它为什么不好 |
| 02:03:30 | 谈论中间人攻击 |
| 02:16:00 | 拉伸/断裂 |
| 02:20:05 | 回到设计/讨论要实施的新功能 |
| 02:38:48 | 我们开始了计算日期/时间数学以确定流正常运行时间的旅程 |
| 02:53:00 | 使用 [Quokka.js VS 代码扩展](https://marketplace.visualstudio.com/items?itemName=WallabyJs.quokka-vscode&WT.mc_id=twitch-social-brcl)运行并测试我们的代码进行日期/时间计算 |
| 02:55:15 | 检查[日期-fns 库](https://date-fns.org/)以帮助日期/时间计算 |
| 02:55:15 | 使用 [moment.js 库](https://momentjs.com)代替日期/时间数学 |
| 04:12:30 | [@ electricha 浩劫](https://twitch.tv/ElectricHavoc)在 ftw 中为我们的日期/时间数学问题提供了一个解决方案 |
| 04:17:30 | “纪元”是什么意思？ |
| 04:28:54 | 关闭和突袭 [@h3h394](https://twitch.tv/h3h394) |

## 今日之流引发的想法

*   ( [@LittleDan45](https://twitch.tv/LittleDan45) )场景椅但是它应该会使用语音模式，这样你就可以继续说话了
*   ([@ m follow ay 24](https://twitch.tv/mholloway24))可以在 raid 上把灯泡换成派对模式吗？
*   ( [@PatPat1567](https://twitch.tv/PatPat1567) )添加视觉效果(鲍尔号叫的 pic 或视频)为！咆哮

## VS 显示/使用的代码扩展

*   [休息客户端](https://marketplace.visualstudio.com/items?itemName=humao.rest-client&WT.mc_id=twitch-social-brcl)
*   [Quokka.js](https://marketplace.visualstudio.com/items?itemName=WallabyJs.quokka-vscode&WT.mc_id=twitch-social-brcl)

## 剪辑

剪辑是 Twitch 上的一项功能，可以让您剪切和捕捉实时流的片段(最长 60 秒)。参见[如何在 Twitch 上使用夹子](https://help.twitch.tv/s/article/how-to-use-clips)了解更多详情。以下是从这条溪流中捕捉到的一些有趣的瞬间:

*   [我爱上了 LIGMA](https://clips.twitch.tv/SpikyHotPineappleJebaited)`by`[@ roberttables](https://twitch.tv/roberttables)
*   [编程适合你吗？(笑话)](https://clips.twitch.tv/EphemeralFurryGoblinRickroll) `by` [@roberttables](https://twitch.tv/roberttables)
*   [我听不懂的笑话](https://clips.twitch.tv/RoughFurtiveShallotBloodTrail) `by` [@PatPat1567](https://twitch.tv/PatPat1567)
*   [哈哈](https://clips.twitch.tv/HelplessVainTurtleAMPEnergyCherry)
*   粉色毛绒绒的独角兽在彩虹上跳舞
*   当你意识到你需要停止一天的流式传输时...
*   [谁让我写这个代码的？够了，你被禁赛了！](https://clips.twitch.tv/NiceCrackySwanDoritosChip)

## 与克拉克奥取得联系

*   [推特](https://twitter.com/_clarkio)
*   [抽动](https://twitch.tv/clarkio)
*   [Instagram](https://instagram.com/_clarkio)
*   [YouTube](https://youtube.com/clarkio)
*   [GitHub](https://github.com/clarkio)
*   [网站](https://www.clarkio.com)
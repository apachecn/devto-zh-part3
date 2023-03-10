# React useReducer 的时间旅行调试器

> 原文：<https://dev.to/murphybrandon/time-travel-debugger-for-react-usereducer-1jed>

[![](img/32a89bb7a335e3903f2e7a8a29619491.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iKYW1I6z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m5v1ylsiv2radif0ppm4.gif)

<center>
[React Rewind Github Repo](https://github.com/reactrewind/react-rewind)

我和几个朋友想了解更多关于 React 16.8 中发布的新特性。在玩了一圈并构建了一些迷你应用之后，我们注意到有一些东西可以帮助我们更容易地调试代码:
**减少时间旅行，但对于 *useReducer* 钩子来说。**

因此，我们决定建立一个开发工具来做这件事:React Rewind。
**React 的 *useReducer* 的第一个时间旅行调试器。**

我们是一个开源工具，昨天刚刚发布了我们的测试版！我们目前完全支持 useReducer 挂钩。我们计划继续开发该工具并扩展其功能。

我们希望听到关于开发工具的任何反馈/想法/问题，并获得一些关于下一步发展方向的想法。

查看我们的 github repo，并告诉我们您的想法。

### 应用功能

[![](img/9d302a914b92e84e0325445c71c69c6f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZE9pdbiP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/b3sednix1286sgpunpga.gif)

| 田 | 描述 |
| --- | --- |
| 事件 | 自上次派单以来的操作类型和时间 |
| 行动 | 被调度的操作对象 |
| 效果 | 调度操作前后的状态差异 |
| 状态 | 调度操作后的状态对象 |

### 投稿

请随意叉我们的代表。我们欢迎拉请求。如需建议或报告错误，请记录问题。

</center>
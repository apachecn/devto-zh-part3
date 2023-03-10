# 在 10 分钟内部署一个速度极快、功能丰富且可免费使用的博客网站

> 原文：<https://dev.to/ciokan/deploy-a-blazing-fast-feature-rich-and-free-to-use-website-with-a-blog-in-under-10-minutes-1e9a>

2018 年对我这个开发者来说是很棒的一年。我设法整合了一个搁置多年的开源项目(仍在紧张开发中)。

我不知道你是怎么想的，但是作为一个对后端和前端都很熟悉的程序员，我总是在测试新的想法、应用和网站。这么做了一年又一年，我注意到一个重复的任务变得相当烦人。我的每个项目都需要一个带有博客的演示网站，在那里我可以更详细地谈论它。

这意味着至少有一个附带数据库的 web 服务器。这并不难，但我宁愿花很多时间在其他事情上，所以我停下来，集思广益，我的下一个项目将结束这种昂贵的部署重复循环。

## 每一栋好房子都始于坚实的基础

我选择的框架是盖茨比。它拥有我认为必需的大部分东西:

*   它必须编译成静态文件
*   易于部署到 Netlify 等 CDN
*   图像优化到位
*   充满活力的社区
*   因为我喜欢弄脏我的手
*   可通过插件扩展
*   使用 GraphQl 提取数据

[![Fast deployments](img/1738634c7521105240780be8a04bb8f7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6mBtkPus--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/razqzt3omu8kac5egf8v.png)T3】

```
 query($slug: String, $tags: [String], $categories: [String]) {
        post: markdownRemark(fields: { slug: { eq: $slug } }) {
            ...postFragment
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

结果必须是面向程序员的东西。我非常讨厌 wysiwyg 编辑器，因为有很多限制，而且输出似乎永远不可预测。我希望开发人员和内容编辑人员都能够接触到框架的强大功能。

[![Power to the publishers](img/3cf409f8a13df33fa6ede9a32bcbb861.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--E3rydTqD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/92bys8ygfhfofvpgtt2n.png)

## 开发者和发布者无障碍表达

缓慢但稳定地形成了 Qards。我的目标是通过使用我喜欢称之为“智能卡”的东西，给作者更多的权力。内容编辑器应该能够使用响应事件、日期、浏览器类型、区域或前端工程师可用的任何其他外部因素的小部件来创建交互式呈现。这些部件包括:

*   自动生成的目录(已开发)
*   图表(计划中)
*   手风琴(已开发)
*   图像(已冲洗)
*   画廊(已开发)
*   视频嵌入(已开发)
*   音频播放列表(已开发)
*   代码块(已开发)
*   标注(已开发)
*   倒计时(已开发)
*   网格列表(计划中)
*   其他职位的参考资料(已开发)
*   等等

[![Smart cards](img/62c647f54070e91ef4739f5b8d503c13.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kLpVcLJy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cqgjevdoxtjy077nsyva.png)

这种平台的另一个必备功能是通过内部插件系统添加定制小部件的能力(仍在开发/计划中)。开发者创建由管理界面(Netlify CMS)解释的指令和数据需求，内容发布者能够在其中创建这些体验。没错，让我们从简单的帖子导航到访问者的“体验”。我们都受到与沉闷界面直接相关的跳出率的影响。

## 让我们把话传出去

项目进行一个月后，我有了一条清晰的道路和一个不再模糊的想法。我喜欢在正式发布之前，在 ProductHunt 和其他类似的平台上进行测试。

Qards 很快被追上并获得了当天的第二名，这对于一个甚至不在 alpha 阶段的人来说一点也不差。

[![Qards on ProductHunt](img/0f0bdf4570c8e5da81bded825d59c7df.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--813XXIZI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vmsnn5fbzqsm5axbshdz.png)

反馈非常有用，我的邮件列表在一个晚上就达到了 2000 +,所以这对我来说是一次富有成效的经历。这就是我验证我的项目所需要的一切。无论如何我都要用它，但我不确定我是否能从中为大众做点什么。

## 不仅仅是博客

我可能在为博客做广告，但 Qards 不仅仅是这样。由盖茨比提供动力，这可能是你的下一个大项目...用博客。您只是获得了不必再担心内容的额外好处。

## 总结

总之，我想概括一下 Qards 是什么和做什么，下面是使这个项目成功的所有部分的列表:

*   由 Gatsby 和 Netlify CMS 提供支持，具有所有优势
*   免费使用和免费部署到 Netlify 或其他 CND(也是免费的 SSL)
*   丰富的互动小工具让你的读者参与进来
*   赋予内容编辑更多的权力(可以把它想象成出版商的自举)
*   编译成静态文件
*   离线支持
*   可插入的
*   可扩张的
*   可破解的
*   自由使用和开发，没有任何限制
    *   代码/内容位于 Github 或 Gitlab 上
    *   静态文件由你能想到的任何 CDN 提供服务
*   开放源码
*   已测试(工作进行中)
*   面向开发者
*   用打字稿开发的
*   速度极快
*   吸引人的默认设计
*   主题表
*   不到 10 分钟即可部署和发布
*   降价内容
*   渐进式加载、图像优化和内容的延迟加载
*   不可破解，始终处于生产部署中
*   令人敬畏的性能指数

如果我设法激发你的兴趣，请看看[一些支持的卡](https://www.qards.io/list-of-supported-cards/)来感受一下这个项目能为你做什么。

此外，它仍然是一个早期阶段，所以 AMA 和随时建议新的东西。
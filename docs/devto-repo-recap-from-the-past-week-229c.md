# 开发人员对上周的回购进行总结

> 原文：<https://dev.to/devteam/devto-repo-recap-from-the-past-week-229c>

考虑到假期和其他因素，这一次有点轻松。我们将报道 12 月 23 日至 12 月 29 日。

# 特性

*   [@ben](https://dev.to/ben) 加了一个`tag`液体标签！你可以这样使用它:

```
{% tag markdown %} 
```

Enter fullscreen mode Exit fullscreen mode

其中呈现:

## # [降价](https://dev.to/t/markdown) <button name="button" type="button" data-info="{&quot;className&quot;:&quot;Tag&quot;,&quot;style&quot;:&quot;full&quot;,&quot;id&quot;:40,&quot;name&quot;:&quot;markdown&quot;}" class="crayons-btn follow-action-button whitespace-nowrap  " aria-label="Follow tag: markdown" aria-pressed="false">跟随</button>

This tag is for all things markdown.

*   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1417)

# Bug 修复等贡献

*   [@Link2Twenty](https://dev.to/link2twenty) 正在继续他们为黑暗模式添加 CSS 变量的工作。感谢所有的公关，安德鲁！

    *   [CSS 变量:支持 IE](https://github.com/thepracticaldev/dev.to/pull/1385)
    *   [将主题背景添加到评论页面](https://github.com/thepracticaldev/dev.to/pull/1386)
    *   [添加主题颜色](https://github.com/thepracticaldev/dev.to/pull/1387)
    *   [添加-主题-顶栏-写-*](https://github.com/thepracticaldev/dev.to/pull/1388)
    *   [向新帖子页面添加主题背景](https://github.com/thepracticaldev/dev.to/pull/1402)
    *   [添加-主题-容器-盒子-阴影](https://github.com/thepracticaldev/dev.to/pull/1403)
*   为新编辑器重构并清理了我们的`imageManagement`组件。这应该有助于在新编辑器中向图像 URL 添加复制按钮。如果你想在这方面有所帮助，请在 GitHub 上查看这个问题。谢谢帮忙，[@韵](https://dev.to/rhymes)！

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1390)
*   我们已经使配置文件 URL 验证变得不那么严格了。你应该可以添加你各自的 LinkedIn/Medium/git lab/等。现在个人资料链接更容易了。

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1392)
*   [@somedood](https://dev.to/somedood) 修复了我们的搜索结果过滤器侧边栏和帖子过滤器部分的一些光标问题。感谢修复， [@somedood](https://dev.to/somedood) ！

    *   [修复悬停时搜索查询过滤器按钮的错误光标指针](https://github.com/thepracticaldev/dev.to/pull/1394)
    *   [修复悬停在`.wide-nav-links`](https://github.com/thepracticaldev/dev.to/pull/1404) 上时闪烁的光标
*   [@moriczgergo](https://dev.to/moriczgergo) 将 Docker 安装过程添加到自述文件的目录中。谢谢@moriczgergo！

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1405)
*   Ben 做了一些园艺工作，用一个环境变量替换了一些硬编码的 dev.to 链接，并重构了我们的文章 API 控制器，使之更加灵活。

    *   [在一些地方用 APP_DOMAIN 配置替换 dev . to](https://github.com/thepracticaldev/dev.to/pull/1406)
    *   [重构文章 API 控制器](https://github.com/thepracticaldev/dev.to/pull/1409)
*   @nickytonline 修复了我们的故事书，将它恢复到最近的版本。谢谢你的修理，尼克！

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1410)
*   [@abraham](https://dev.to/abraham) 去掉了一个不必要的变量赋值，全部两行！谢谢亚伯拉罕。

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1414)
*   [@arun](https://dev.to/arun) 修复了 feed 页面上最新和无限时间过滤器的错误命名标题属性。已经合并了，但是 [@arun](https://dev.to/arun) 提到他们“对这种方法不太满意。如果有人有更好的解决方案，请随时(在公关上)发表评论。”谢谢， [@arun](https://dev.to/arun) ！

    *   [链接到 PR](https://github.com/thepracticaldev/dev.to/pull/1416)

# 新问题

上周没有新的问题。查看之前的回购总结，了解要讨论的许多新问题:

[![The DEV Team](img/6b3a9c79aeecf790d5144f3fe1881f50.png)![](img/2c8741e5fbb812e2e857e3cd00f499f7.png)](/devteam) [## 开发人员对上周的回购进行总结

### 安迪赵(他/他)为开发团队 12 月 29 日 184 分钟阅读

#changelog #meta #opensource](/devteam/devto-repo-recap-from-the-past-week-2f5n)

感谢阅读！
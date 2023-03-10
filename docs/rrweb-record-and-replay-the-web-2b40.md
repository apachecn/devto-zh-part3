# rrweb:记录和回放 web

> 原文：<https://dev.to/ben/rrweb-record-and-replay-the-web-2b40>

这是一个用于记录用户活动并将其存储为各种可回放视频的库。

这类似于 [hotjar](https://www.hotjar.com) 和类似的服务，但它是开源的，似乎更具可配置性。本质上更具可配置性，因为你有代码。

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)[RR web-io](https://github.com/rrweb-io)/[RR web](https://github.com/rrweb-io/rrweb)

### 记录和重放网页

<article class="markdown-body entry-content container-lg" itemprop="text">

[![](img/78d3516dea0cd5a43a55cd33abd0b2a2.png)](https://camo.githubusercontent.com/b01daa23614938e0db434ee5019f9a86328d15df4deccf8505cafa3adec89be1/68747470733a2f2f7777772e72727765622e696f2f66617669636f6e2e706e67)

[试试 rrweb](https://www.rrweb.io/)

# rrweb

**[《RR web》纪录片(中文，配英文字幕)](https://www.bilibili.com/video/BV1wL4y1B7wN?share_source=copy_web)**

[![Join the chat at slack](img/306b841fd219631bd2b7875d6c585912.png)](https://join.slack.com/t/rrweb/shared_invite/zt-siwoc6hx-uWay3s2wyG8t5GpZVb8rWg)[![Twitter Follow](img/cf0641ab9ee6100976901a2d650bf6e7.png)](https://twitter.com/rrweb_io)[![total gzip size](img/1ae287438fcd89146058d76c36d21cf1.png)](https://camo.githubusercontent.com/faa07314bcfe48b27382137ba987f7c02e3f26f3cd8cdbf4a543e12770f00e19/68747470733a2f2f696d672e626164676573697a652e696f2f68747470733a2f2f63646e2e6a7364656c6976722e6e65742f6e706d2f7272776562406c61746573742f646973742f72727765622e6d696e2e6a733f636f6d7072657373696f6e3d677a6970266c6162656c3d746f74616c253230677a697025323073697a65)[![recorder gzip size](img/d55f4bb2a73f51750e8911df43adcfaa.png)](https://camo.githubusercontent.com/75df7ef9cfd8196dfdbfe67d306bcebcd100f50d2d7072bb0b4137dfb0b5f9af/68747470733a2f2f696d672e626164676573697a652e696f2f68747470733a2f2f63646e2e6a7364656c6976722e6e65742f6e706d2f7272776562406c61746573742f646973742f7265636f72642f72727765622d7265636f72642e6d696e2e6a733f636f6d7072657373696f6e3d677a6970266c6162656c3d7265636f72646572253230677a697025323073697a65)[![](img/b600828940474f655d6589e81cfff0ba.png)](https://www.jsdelivr.com/package/npm/rrweb)

[Chinese document](https://github.com/rrweb-io/rrweb./README.zh_CN.md)

> 我加入了 Github 赞助商，非常感谢你的赞助。

rrweb 指的是‘记录和回放 web’，是一个记录和回放用户在 web 上的交互的工具。

## 向导

[**T2】📚点击此处阅读 rrweb 指南。<g-emoji class="g-emoji" alias="books" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4da.png">📚</g-emoji>**](https://github.com/rrweb-io/rrweb./guide.md)

[**T2】🍳食谱<g-emoji class="g-emoji" alias="fried_egg" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f373.png">🍳</g-emoji>**](https://github.com/rrweb-io/rrweb./docs/recipes/index.md)

## 项目结构

rrweb 主要由 3 部分组成:

*   **[rrweb-snapshot](https://github.com/rrweb-io/rrweb/tree/master/packages/rrweb-snapshot/)** ，包括快照和重建功能。快照用于将 DOM 及其状态转换为具有唯一标识符的可序列化数据结构；重建特性是将快照重建到相应的 DOM 中。
*   **[rrweb](https://github.com/rrweb-io/rrweb)** ，包括记录和回放两个功能。record 函数用于记录 DOM 中的所有突变；重放是根据对应的时间戳，将记录的突变逐个重放。
*   rrweb 播放器 ，是 rrweb 的播放器 UI，提供基于 GUI 的功能…

</article>

[View on GitHub](https://github.com/rrweb-io/rrweb)

根据自述文件，目前被视为尚未准备好投入生产。

我们为这样的帖子创建了一个名为 GitHunt 的新标签。如果你喜欢这种东西，就跟着标签走。当然，也欢迎所有人投稿。

## # [githunt](https://dev.to/t/githunt) <button name="button" type="button" data-info="{&quot;className&quot;:&quot;Tag&quot;,&quot;style&quot;:&quot;full&quot;,&quot;id&quot;:18442,&quot;name&quot;:&quot;githunt&quot;}" class="crayons-btn follow-action-button whitespace-nowrap c-btn--secondary fs-base " aria-label="Follow tag: githunt" aria-pressed="false">跟随</button>

Interesting open source repos you've discovered and want to share with the community.

快乐编码！〈希莫丝〉
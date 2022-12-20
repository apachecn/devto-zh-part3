# 油墨:为 CLIs 反应

> 原文：<https://dev.to/nickytonline/ink-react-for-clis-2l3b>

这个又短又甜。

你熟悉 React 吗？那么你知道墨水。我还没有尝试过这个，但基本上它是一个[自定义反应渲染器](https://github.com/nitin42/Making-a-custom-React-renderer)，允许你构建漂亮的 CLI。

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)/[墨](https://github.com/vadimdemedes/ink)

### 🌈对交互式命令行应用作出反应

<article class="markdown-body entry-content container-lg" itemprop="text">

[![](img/105ede4fb6a6e79bd2b06fdc369f26b4.png)](https://github.com/vshymanskyy/StandWithUkraine/blob/main/docs/README.md)

* * *

[![Ink](img/de8ac85422fd784f294eee3f23440afa.png)](https://github.com/vadimdemedes/inkmedia/logo.png)

> 为 CLI 做出反应。使用组件构建和测试您的 CLI 输出。

[![Build Status](img/e9041a74352fcf0737e66fef0a3049cc.png) ](https://github.com/vadimdemedes/ink/actions) [ ![npm](img/1d7b4e2817a24550b0e37acb3f68eb38.png)](https://npmjs.com/package/ink)

Ink 提供了与 React 在浏览器中提供的相同的基于组件的 UI 构建体验，但对于命令行应用程序，它使用 [Yoga](https://github.com/facebook/yoga) 在终端中构建 Flexbox 布局，因此大多数类似 CSS 的道具在 Ink 中也是可用的如果你已经熟悉 React，你就已经知道 Ink。

由于 Ink 是一个 React 渲染器，这意味着 React 的所有功能都受支持，请访问 React 网站获取如何使用它的文档。本自述文件中将只记录 Ink 的方法。

**注:**这是油墨 3 的文档。如果你正在寻找关于 Ink 2 的文档，请查看[这个版本](https://github.com/vadimdemedes/ink/tree/v2.7.1)。Ink 2 中还有一个[迁移指南](https://github.com/vadimdemedes/inkmigrate.md)。

* * *

<sup>[我的开源工作得到了社区的支持 <g-emoji class="g-emoji" alias="heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2764.png">❤️</g-emoji>](https://opencollective.com/vadimdemedes)</sup>

<sup>Special thanks to:</sup>
[![WorkOS](img/f5e341fe74f9667cde83b102300e2d51.png)**Your app, enterprise-ready.**<sub>Start selling to enterprise customers with just a few</sub>…](https://workos.com/?utm_campaign=github_repo&utm_medium=referral&utm_content=ink&utm_source=github)</article>

[View on GitHub](https://github.com/vadimdemedes/ink)

CLI 中的 Flexbox？🤯CLI 中的 React 钩子？YOLO，我的朋友，YOLO。

<center>
![Holy Forking Shirt!](img/71f0ea432d2ec88774e5dca3adef9a10.png)
</center>

这里有一个直接来自资源库自述文件的小例子:

```
import React, {Component} from 'react';
import {render, Color} from 'ink';

class Counter extends Component {
    constructor() {
        super();

        this.state = {
            i: 0
        };
    }

    render() {
        return (
            <Color green>
                {this.state.i} tests passed
            </Color>
        );
    }

    componentDidMount() {
        this.timer = setInterval(() => {
            this.setState({
                i: this.state.i + 1
            });
        }, 100);
    }

    componentWillUnmount() {
        clearInterval(this.timer);
    }
}

render(<Counter/>); 
```

Enter fullscreen mode Exit fullscreen mode

甚至 npm 的人都认为这很酷。

> ![Kat Marchán profile image](img/a6370c8bf00efeaf16d9e9c453511318.png)Kat Marchán@ maybe Katz![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)墨迹极其精彩，你们都应该去看看。@npmjs CLI 团队打算将我们的许多 UI 迁移到它上面，我用它开发了一些交互式安装程序和更新程序的早期原型，从中获得了很多乐趣！向瓦迪姆致敬，他做了一些了不起的工作！[twitter.com/vadimdemedes/s…](https://t.co/JwAd3jwuj5)2019 年 03 月 06 日下午 17:32瓦迪姆德梅德斯[@瓦迪姆德梅德斯](https://dev.to/vadimdemedes)🍭我非常兴奋地宣布终于发布了 Ink 2 - library，它可以让您用 React 组件以前所未有的速度构建漂亮的交互式命令行界面！哦，你也可以用 Flexbox 布局组件🙂https://t.co/U6Z0kv2EUG. https://t.co/CZIAaBEcBq[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1103347697107361792)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1103347697107361792)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1103347697107361792)

Ink 的作者 Vadim 有一篇很棒的介绍文章，[用 Ink 和 React](https://vadimdemedes.com/posts/building-rich-command-line-interfaces-with-ink-and-react) 构建丰富的命令行界面。

那么你打算用墨水造什么呢？😉

编码快乐！

Pierre Bamin 在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片
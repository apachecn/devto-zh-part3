# 不丢人，这是我的第一个 React 网站

> 原文：<https://dev.to/bananabrann/no-shame-heres-my-first-react-site-4i33>

在过去的一周里，我和 [@RendonEls](https://github.com/RendonEls) 一起开发了一个游戏。我学到了很多关于 React 的东西，在完成这个项目后，我对它肯定感觉更舒服了。

然而，很多时候我们只能看到别人的精彩片段，所以我想我应该展示一下(躲在角落里)我的第一个使用 React 的全 CRUD 站点。没有人生来就是冠军，所以可以找点乐子，简单看看我的游戏《你愿意吗》,它还没有准备好投入生产。所以嘿，让我们找点乐子，看看所有的错误！

* * *

## 登陆页面

当你第一次登陆这个网站时，你会看到主页。
[![homepage](img/10d543336cb4e3d89fa9562360d5bcf3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Dxz3QuSC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d4n8ywgtduka8zaj3ytb.png) 
你有能力玩，查看统计，或者去选项。

## 玩游戏

[![play](img/a0b6e27fa91fd580e9c0b5dfc0c54f68.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MIiDof52--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dp488oi90r5mk9qsx69s.png) 
这里是播放画面。现在，这个游戏甚至不能玩，哈哈！但是，嘿，它显示了两个随机问题！

*   无法选择问题。
*   没有下一步按钮。

## 选项-创建

[![create](img/b25661432c97bba2f6bbeed92f9229f2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vbV7ANOu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8ps9p1erkjexnkrkopjd.png) 
进入选项后，可以创建一个问题。网站的这一部分可能是看起来最好的部分，但也是最简单的部分。

*   按提交既不会重定向也不会刷新。
*   提交后导航离开，不会在任何地方显示您的问题。无论转到哪个页面，都要手动刷新。😅

## 选项-编辑

[![edit](img/4f7213f61d6cf98db9f78e457967661d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GrayP3-X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8igjw1vpno1adn81ets9.png) 
啊，编辑页面。我们存在的祸根，后端和前端都有。最后，它变成了 40 行代码。这花了我们大约 24 个工作小时来完成，但它的工作！

*   单击删除也会立即删除问题，而不会刷新！😃
*   添加足够多的问题，使其越过页脚**破坏页脚**。

[![edit with inserts](img/89e5c0d18af3f4f5985112b4aecc4ee2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qOU7HXJt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tky92gx6zxvp8p5m1kp8.png)T3】...然后你按下编辑。

*   单击其中一个按钮的“编辑”将下拉所有按钮的框。
*   单击 submit 会立即在 state 中更新它们，而不会刷新！😃😃😃

* * *

就是这样！我希望这残酷地激励了一些人，或者至少从一些资深程序员那里得到了一丝微笑。愿你的 bug 短，编码快乐！

部署网站:
[http://dirty-fan.surge.sh/](http://dirty-fan.surge.sh/)
前端回购:
[https://github.com/bananabrann/would-you-rather-frontend](https://github.com/bananabrann/would-you-rather-frontend)
后端回购:
[https://github.com/RendonEls/would-you-rather-backend](https://github.com/RendonEls/would-you-rather-backend)

附:如果你真的很有野心，分享你的第一个网站吧！

皮尔森·布兰南
# CSS 网格布局-一个“快速”概述

> 原文：<https://dev.to/adyngom/css-grid-layout-a-quick-overview-1kce>

[https://www.youtube.com/embed/wWGjogHcLxY](https://www.youtube.com/embed/wWGjogHcLxY)

### 快速警告和请求

我的一个朋友告诉我，他从视频中学到了很多东西，尽管它有点长——25mn 有点长。如果你把它加速到 1.5 倍，它仍然是可观看的，但在 2 倍时，我开始听起来像一只彻头彻尾的花栗鼠，你可能会觉得它很有趣。如果 CSS Grid 对您来说是新的，我希望您喜欢它并从中学习一些东西。我真的很怀念在用户界面方面的工作，如果你想看到更多关于用户界面的帖子，请在评论中告诉我

在本教程中，我们将快速查看 CSS 网格系统提供的功能。一旦你看到 CSS 网格的潜力，是时候抛弃你已经习惯的所有框架代码了。

启动器代码是内联添加的，或者您可以从这里复制:
[网格启动器代码](https://gist.github.com/adyngom/dadfa4150296546c32897153addd2385)

并且使用的模板可以在这里下载:
[埃尔顿 UI 套件](https://pixelbuddha.net/freebie/elton-ui-kit)

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    Elton ui Kit
    <style>
        @import 'https://fonts.googleapis.com/css?family=Comfortaa:300,700|Bungee+Shade|Josefin+Sans:400';

        html {
            background-color: #eee;
        }
        html, body {
            margin: 0;
            padding: 0;
        }
        body {
            counter-reset: section;
        }
        .wrapper {
            width: 1170px;
            margin: 30px auto;
            box-sizing: border-box;
        }
        .box {
            border-radius: 3px;
            background-color: #ffffff;
            box-shadow: 0 1px 3px 0 rgba(0, 0, 0, 0.06);
        }
        .hero-fashion > .box {
            width: 1170px;
            height: 505px;
            margin-bottom: 30px;
        }
        .row {
            display: grid;
            grid-template-columns: 1fr 1fr 1fr 1fr;
            grid-auto-rows: 238px;
            grid-gap: 30px;
        }
        .tall-span {
            grid-row: span 2;
        }
        .wide-span {
            grid-column: span 2
        }

    </style>
</head>
<body>
    <div class="wrapper">

        <!-- hero section -->
        <div class="hero-fashion">
            <div class="box"></div>
        </div>

        <!-- grid section -->
        <div class="row">
            <div class="box tall-span"></div>
            <div class="box wide-span"></div>
            <div class="box"></div>
            <div class="box"></div>
            <div class="box wide-span"></div>
            <div class="box"></div>
            <div class="box"></div>
            <div class="box"></div>
            <div class="box"></div>
            <div class="box"></div>
            <div class="box"></div>
            <div class="box"></div>
            <div class="box"></div>
        </div>
    </div>
</body>
</html> 
```
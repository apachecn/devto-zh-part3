# JS13K 2018 游戏比赛事后

> 原文：<https://dev.to/mrlopis/js13k-game-competition-postmortem-2j1p>

## 挑战

2018 版 [js13k 游戏开发大赛](http://js13kgames.com/)已经落下帷幕。1 个月内几十(几百？)的开发人员试图开发一个 13KB 大小的微型 javascript 游戏——包括图形、代码和声音。

2018 年是我第二次尝试这个挑战。去年，我开发了一款名为 [Hoosdere](http://js13kgames.com/entries/hoosdere) 的 JS+CSS 游戏，在这款游戏中，你必须逃离怪物，逃离森林。控制和视觉效果相当差。游戏也是不完整的交付。今年，我承诺我会做得更好更快。

### 概念

每年，比赛都有一个主题。之前的主题是“迷失”，而今年的主题是“线下”。你当然可以随心所欲地诠释这个主题。不知何故，我很快就想出了一个新游戏的主意。我会制作一个类似 tamagochi 的游戏，其中设备的在线/离线状态会直接影响宠物的精神状态。所以我像往常一样开始了:用笔和纸:

[![Initial mockup of the game](img/961f4ebe3e8ab7dd83459f12c7a993d2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DDucoOwI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uj59kwlp8817fc3wtp67.jpg)

虽然我今年开始得晚，但我尽力在剩下的 3 周时间里做到最好。随着游戏概念的产生，是时候探索技术和工具了。

### 工具

#### 发动机

去年，我用 CSS 动画和滤镜以及一些 SVG 设计了我的游戏。这很好，但是表现真的很差。所以今年我决定我要成长并学习一些合适的 WebGL。我尝试了几个不同于 js13kgames 建议的工具，最终选择了 [tiny-Canvas](https://github.com/bitnenfer/tiny-canvas) ，它设置了 webGL 上下文并提供了渲染和操作图片的工具。

#### 设计

我还决定，我想刷我的像素艺术技能，所以游戏将有一个有限的调色板像素艺术设计。我发现了一个很棒的在线编辑器，叫做 [Piskel](https://www.piskelapp.com/) ，它可以让你在浏览器中保存你所有的设计，也可以离线工作。它还让我创建精灵表和动画，以及定义调色板。

具有讽刺意味的是，我主要工作中的某些任务提供了一些重要线索，可以节省我宝贵的千字节:压缩 png。我用的是在线 PNG 压缩工具 [TinyPNG](https://tinypng.com/) 。它轻而易举地为我节省了 50%的图片大小。

明年我想探索的是精灵图片的文件大小是否小于单独图片的总和。此外，如果网格中的正方形子画面比单列或单行具有更好的压缩结果。在我运行的有限测试中，我看到了不同的结果。

#### 代码

去年，我探索了几种最小化代码的方法。因为目标浏览器是常青树，所以我不在乎编译 javascript: ES6 就可以了！为了缩小，我改编了去年的大口文件([https://github.com/lopis/signalmon/blob/master/gulpfile.js](https://github.com/lopis/signalmon/blob/master/gulpfile.js))。没有什么新奇的东西，只是简单陈旧丑陋的 javascript、css 和 html。

我的 Javascript 代码由几个独立的类和一个声明它们的主脚本组成:

```
 controls.js - handle user input a trigger events from them
    draw.js - handle all the drawing cycles in a functional way
    game.js - contains the game state; handles the game cycles and updating the state when events occur
    microphone.js - handles input from the microphone (more on this later)
    mini-events.js - https://github.com/allouis/minivents
    tiny-canvas.js - https://github.com/bitnenfer/tiny-canvas
    utils.js - helper global functions

    main.js - main game cycle and initializes the other modules 
```

## 控件

我从去年学到的一点是，游戏输入可以成就或毁灭一个游戏。我尝试了复杂的游戏控制方式，最终得到了一个糟糕的游戏体验，不是直观的，而是缓慢和令人困惑的。所以今年我决定买一些基本的东西:纽扣，就像一个玉米卷。我想让这个游戏与众不同，所以我引入了两个有趣的用户输入。

*   首先，游戏取决于设备是否连接到互联网。这是我的想法，遵循比赛的“线下”主题。
*   第二，游戏麦克风用于检测噪音。没有录音，我只是想让“嘈杂的环境”成为游戏的一个元素。
*   将会有第三个——设备震动——但是我意识到 web APIs 还没有到那一步时已经太晚了。所以我放弃了这个想法。

我想让我的游戏让玩家离线。这个游戏也需要你时不时地上线，但是大部分时间你的设备必须完全离线才能成功玩游戏。我认为这很有哲理，或者是对现实生活的一个很好的隐喻，或者是其他一些很深刻的东西。

## 发展

我有工作。这不是它。因此，我和我的许多挑战者一样，必须抽出时间来开发这个游戏。

## 工作日程

我每天乘火车上下班的时间相当长——大约 25 分钟。这给了我每天 50 分钟的时间，只要我能找到座位。我也可以在周末工作 6 到 8 个小时。根据我的粗略计算，我可以在这个游戏上花费大约 30 个小时。

因为事情总是在这些挑战中进行，我从来没有真正准备好。我试着做一些产品管理，但很快就放弃了，转而喜欢简单的带复选框的纸。这里的问题是，我不完全确定我的 MVP 应该是什么样的，以及在严格的期限内我能完成多少。

有一件事我希望我已经做了，并且将在明年探索，那就是精确的时间跟踪。我想知道每项任务花了我多少时间，我在哪里浪费了时间。我去年遇到的一个问题是，我在试图获得正确的声音效果时迷失了方向，结果我没有时间改进游戏控制。我是一个程序员，不是艺术家，所以我应该看到那个天坑的到来。

## 我的外卖

我很高兴我了解了自己集中注意力的能力。它本质上是非常差的，但是如果我心中有一个明确的目标，我能够连续几天专注于小任务来完成它。

我想和别人一起工作。在整个过程中，我一直在与一些有创意的区块进行斗争，因为我没有一个可以与我讨论游戏细节的编码伙伴。我还发现我迷失在我的游戏隐喻中，这个概念对于不太了解的玩家来说不太清楚。我很难把这个想法传达给一些人。

我应该提前准备好我的工作环境。我花了太多时间重构我的代码，因为我不知道我要去哪里。为我的图像、动画和循环创建一个好的 webGL 平台将是一个好主意。

像素艺术很有趣。我都忘了自己有多喜欢。

我渴望得到认可。我忍不住把游戏发给尽可能多的人。但是我不得不克制自己直到最后，因为我明白尝试一个半吊子的游戏会破坏他们对最终版本的理解。

明年我肯定会再次努力。
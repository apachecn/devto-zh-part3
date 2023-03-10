# 用 Glitch 和 Raspberry Pi 构建一个未来主义的唱机

> 原文：<https://dev.to/patrickweaver/building-a-futuristic-record-player-with-glitch-and-raspberry-pi-27a2>

去年早些时候，我想探索 Javascript 中新的 [async/await](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/async_function) 功能，所以我开始在 Glitch(【glitch.com】)上与[的一个项目](https://async-await-machine.glitch.me/)一起玩，这个项目将一个接一个地调用 API，然后从每个周期生成新的 API 调用选项。我编制了一长串可能使用的 API，但没有将一个会响应特定品种狗图片的 [API 和一个会响应搜索品种名称的页面的](https://dog.ceo/dog-api/)[维基百科 API](https://www.mediawiki.org/wiki/API:Main_page) 链接在一起。

[https://glitch.com/embed/#!/embed/async-await-machine?previewSize=100&path=index.html](https://glitch.com/embed/#!/embed/async-await-machine?previewSize=100&path=index.html)

##### 我的狗的一个故障嵌入→维基百科 API 原型:点击一只狗的名字，你会得到那只狗的图片，以及从那只狗的维基百科页面链接的页面列表。

我开始探索我可以连接的其他 API，并意识到它们中的大多数似乎都实施了相对严格的内部类型系统。即使从维基百科提供的众多链接中，也很难想出一个潜在的下一个链接总是与维基百科返回的大杂烩相匹配。虽然将 Google Maps API 与天气 API 匹配起来可能很容易，但是将更抽象的概念联系起来就要困难得多。

## “唱机”应用程序

思考这个问题时，我突然想到了一个主意。我可以将 Google Vision API 连接到 Spotify，根据唱片封面的图片来查找专辑。这个想法看起来如此明显，以至于我认为其他人已经做了(后来通过更彻底的研究，我发现了一些类似的项目，但没有一个完全实现)。

我认为这个想法的火花已经比我的无限 API 艺术项目有趣得多，但它仍然具有我所追求的鲁布·戈德堡式的品质。即便如此，如果没有 Glitch 这样的工具，我可能还是会放弃它。对我来说，Glitch 表示，“你的古怪想法值得一提(而且不会花很长时间)。”利用我之前项目的框架，我能够(在大约一个小时内)组装一个应用程序，该应用程序从 Google Vision API 弹出一幅图像，然后从 Spotify 显示搜索结果。我把它发给了一个在 Spotify 工作的朋友，当她确认它还不存在时，我决定花几个小时组装一个更精致的版本(最难的部分是拖放文件上传)。

由于 Glitch 的嵌入功能，你可以看看这个应用程序(和代码！)如下:

[https://glitch.com/embed/#!/embed/record-player?previewSize=100&path=index.html](https://glitch.com/embed/#!/embed/record-player?previewSize=100&path=index.html)

##### 电唱机 app 的一个小故障嵌入

这个项目的“最终”版本“[唱机](http://record-player.glitch.me/)”出奇的简单。当给定一张相当明亮的甚至有点流行的专辑的图像时，Google Vision API 能够识别专辑的名称(有时只是艺术家)。有几个词我很难编码忽略(如“乙烯”、“cd”或“进口”)，但除此之外，我可以将谷歌视觉的“最佳猜测”发送到 Spotify，并播放第一个结果。我设计了一个愚蠢的前端(包含所有与音乐相关的表情符号),并在 Twitter 上分享了它，以为会有一些人尝试它。

[https://glitch.com/embed/#!/embed/record-player?sidebarCollapsed=true&path=censoredWords.js](https://glitch.com/embed/#!/embed/record-player?sidebarCollapsed=true&path=censoredWords.js)

##### 我的令人惊讶的短名单中嵌入了一个小故障

[安尼尔破折号](https://twitter.com/anildash/)出人意料地被分享了，很快它就开始出现了[中的](https://pitchfork.com/news/new-app-is-basically-shazam-for-album-covers/) [中的](https://www.pastemagazine.com/articles/2018/05/new-app-automatically-recognizes-album-covers.html) [中的](https://www.nme.com/news/music/app-album-cover-shazam-2306795) [中的](https://www.engadget.com/2018/05/04/record-player-app-image-based-spotify-search/) [中的](https://www.altpress.com/news/app_matches_album_covers_spotify/) [中的](https://www.androidauthority.com/record-player-spotify-google-861977/) [中的](https://www.rollingstone.it/musica/news-musica/ora-esiste-uno-shazam-per-le-copertine-degli-album/410773/) [中的](https://lifehacker.com/stream-a-vinyl-album-by-snapping-a-pic-of-its-cover-art-1825800020) [互联网上的](https://www.thecurrent.org/feature/2018/05/02/app-album-covers)。我对这些报道感到受宠若惊，但是有几个人指出，“为什么你要拍照而不是只输入专辑的名字？”首先是好玩，这应该是足够的理由。但除此之外，现在真的没有理由，在我们每天使用的工具中有太多的障碍和限制，如 Spotify 和 Google，制作这样的 mashup 通常需要工程师，而不仅仅是想象力。

## 让唱机在树莓派上工作

从一开始，我就想创造一个实体版本的电唱机(灵感来自于他们在没有“传统”输入和输出设备的情况下似乎正在做的惊人的事情)。这将是一台可以检测你何时在它前面放上唱片封面，并自动开始播放第一首歌曲的机器。没有屏幕，没有搜索，没有精心策划的播放列表来分散我对手中实体事物的注意力。

我再次惊喜地发现，通过找到正确的工具，概念验证比我想象的要容易得多。使用带有摄像头模块的 Raspberry Pi 可以简单地捕捉图像。这就只剩下确定何时开始播放音乐的挑战了。起初，我认为通过再次利用谷歌的高级图像处理，这可能很容易做到，但我意识到，如果我希望设备自动响应，谷歌视觉 API 查询的成本(你每月只能获得一定数量的免费)将会令人望而却步。

我决定看看在 Raspberry Pi 上运行的简单算法是否可以识别什么时候新的东西被放在相机前面，这似乎足够好。将它连接到稍加修改的 Node.js 服务器版本，该服务器运行最初的唱片播放器 Glitch 应用程序，创建了我想象中的机器。下面的视频显示了我的“唱机”在看到唱片封面时自动开始播放。这个原型有屏幕，但它们只用于启动应用程序和故障诊断。

[https://player.vimeo.com/video/288443309](https://player.vimeo.com/video/288443309)

##### 一个在树莓派上运行的电唱机的视频

Anil 在分享 Glitch 应用程序时建议使用“唱片播放器”，是为了看看自拍时会出现什么歌曲，或者用他的话说，“Shazam for your face”。当然这很搞笑，当我们开始打破目前限制我们与技术互动的严格参数和物理障碍时，这种游戏和动态互动就会发生。

浏览 Spotify 远不如浏览朋友的唱片集有趣，但任何曾经录制过的歌曲的便利性通常会战胜发现意想不到的东西的喜悦。如果我们希望这种“魔法”将谷歌视觉这样的技术从我们手机和笔记本电脑中预先打包的应用程序中带出来，我们需要允许与计算机以及相互之间进行交流的新方式，这种方式允许实验、怪异和想象。

当我们开始将现实世界中有趣的部分(例如:1997 年之前创造的一切)与智能手机带来的便利联系在一起时，我们可以开始想象像我们数百年来一直想出来的模拟体验那样的动态数字体验。黑胶唱片本身被设计成一种静态媒介，但有创造力的音乐家们发现了一种互动的方式，并开始采样和刮擦它们来创造嘻哈音乐。

在花了几个小时筛选了几十个 API 的文档后，我才明白了“唱机”的概念。作为用户我们看不到互联网的“形状”，但是这些形状决定了我们能做什么不能做什么，我们能想象什么不能想象什么是可能的。毫无疑问，在天气或地图应用上的每次搜索都是一个城市或地址，在谷歌图片上的几乎所有搜索都是名词，在 Spotify 或 Genius 上的每次搜索都是艺术家或歌曲。但是尝试沿着不寻常的角度连接这些服务和 API，你会开始碰壁。

在谷歌地图上扔一首歌，看看它是在哪里录制的？将 Yelp 页面传递给纽约时报 API 以查找任何关于该社区的新闻？这些查询可能是可行的，但是它们几乎总是需要深入几个 API 的本质，并处理数据以建立连接。在今天的世界里，像“唱机”这样的想法必须是价值百万美元的生意才值得去做。许多关于唱片播放器应用的文章暗示 Spotify 已经成功了。我们体验现代技术的方式告诉我们，只有大公司才能创造体验世界的新方式。

像 [Glitch](https://glitch.com/) 、[树莓派](https://www.raspberrypi.org/)、 [IFTTT](https://ifttt.com/) 、[快捷键](https://support.apple.com/guide/shortcuts/welcome/ios)这样的产品，以及树莓派唱机的灵感、[dynamic and](https://dynamicland.org/)让一个更有趣、更开放的未来似乎成为可能，现在我们只需要构建剩下的部分。

现在开始重新混合电唱机的故障:
[https://glitch.com/~record-player](https://glitch.com/~record-player)

或者自己制作树莓派动力版:
[https://github.com/patrickweaver/record-player-rpi](https://github.com/patrickweaver/record-player-rpi)
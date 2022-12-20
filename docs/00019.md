# 使用 JavaScript 在 Vimeo 中选择字幕

> 原文：<https://dev.to/samvloeberghs/selecting-subtitles-in-vimeo-using-javascript-3c6i>

*最初发布于[https://samvloeberghs . be](https://samvloeberghs.be/posts/selecting-subtitles-in-vimeo-using-javascript)2018 年 3 月 1 日*

在[sponsor formatch](https://sponsormatch.eu/en)我们部署了一个用不同语言显示的 Angular Universal 网站&应用程序。目前我们支持荷兰语(荷兰)和英语(英国)。在主页上，我们使用一个简单的介绍视频/解释，由 Vimeo 的[主持，展示产品如何工作。这部动画视频是用英语制作的，但是对于荷兰语市场，我们提供了荷兰语字幕。](https://vimeo.com/231377016)

## 问题

似乎不可能使用 Vimeo iframe embed 解决方案来设置默认的选定字幕。有可能，但是无论如何，我在 Google 或者 Vimeo 文档上找不到任何信息。关于 StackOverflow 甚至有一些问题多年来一直没有答案:

*   [Vimeo 强制 CC 语言](https://stackoverflow.com/questions/27014658/vimeo-force-cc-language)
*   [Vimeo 嵌入语言/字幕](https://stackoverflow.com/questions/27953778/vimeo-embed-language-subtitles)

## 解

有几种可能的解决方案，其中一种是完全转向 Vimeo 提供的 JavaScript API。更多信息可以在这里找到:[https://github.com/vimeo/player.js](https://github.com/vimeo/player.js)

我使用的第二个解决方案是将 iframe embed 解决方案与 JS API 相结合。基本上，我们只是使用用于嵌入的 iframe 作为播放器构造函数中的参数:

## 用它来表示棱角分明

在 Angular 中使用它相当容易..我们需要使用 yarn 或 npm 安装@vimeo/player 包:

```
npm i -s @vimeo/player 
```

接下来，我们可以直接导入播放器，通过使用`enableTextTrack`函数，我们可以很容易地设置语言。作为开发人员/实现人员，我认为我们应该在某种程度上负责了解哪些语言可供使用。含义:在嵌入的视频上实现了哪些字幕/文本轨道。

```
import Player from '@vimeo/player';
const player = new Player(document.getElementById('intro_explanimation'));

player.enableTextTrack(this.language, 'subtitles').then(function (track) {
    // track contains language, kind and label
}).catch(function (error) {
    // error holds what happened in detail
}); 
```

请看看下面的完整示例。最终结果实现在[赞助模式](https://sponsormatch.eu/en)的主页上。

角度-视频-设置-语言-字幕

## 进一步阅读

*   [Vimeo -开发者 API](https://developer.vimeo.com/)
*   [Player.js -与嵌入式 Vimeo 播放器互动并控制该播放器](https://github.com/vimeo/player.js)

*最初发布于[https://samvloeberghs . be](https://samvloeberghs.be/posts/selecting-subtitles-in-vimeo-using-javascript)2018 年 3 月 1 日*
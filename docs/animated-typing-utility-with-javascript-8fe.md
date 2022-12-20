# 带有 JavaScript 的动画打字工具

> 原文：<https://dev.to/saigkill/animated-typing-utility-with-javascript-8fe>

曾经希望你的 web 项目中有动画打字效果吗？如果是，您应该明确地检查一下 typit.js 库。最近我偶然发现了那个有趣的图书馆。

效果看起来像:

[![](img/07c3d0f09cb74f9858f084592dfdff18.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--s4KR3zMz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://raw.githubusercontent.com/alexmacarthur/typeit/master/demo.gif)

你如何实现它？

比方说，你想用这种效果向别人展示你不同的工作经历。

首先在你的代码中放置一个标签，就像这样:

```
<p id="replaceJobs"></p> 
```

然后创建一个内容为
的`typeit.js`文件

```
new TypeIt('#replaceJobs', {
    strings: ["Job1", "Job2", "Job3"],
    speed: 200,
    cursorSpeed: 1000,
    nextStringDelay: 750,
    loop: true,
    breakLines: false,
    waitUntilVisible: true
}).go(); 
```

现在将 JavaScript 添加到布局部分:

```
<script src="https://cdn.jsdelivr.net/npm/typeit@VERSION_NUMBER/dist/typeit.min.js"></script> 
```

并将您的`typeit.js`指向放置“replaceJobs”标签的页面。现在你的文字效果将开始:-)

更多内容:[https://typeitjs.com/](https://typeitjs.com/)
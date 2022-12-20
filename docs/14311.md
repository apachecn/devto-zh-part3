# 悬停时预加载？

> 原文：<https://dev.to/itachiuchiha/preload-on-hover-4i4d>

你好。我想知道预载技术。例如，我有一段如下所示的代码:

```
const links = document.querySelectorAll('a')

Array.from(links).forEach((link) => {

    link.addEventListener("mouseenter", () => {

        const xhr = new XMLHttpRequest()

        xhr.open("GET", link.href)
        xhr.send()

    })

}) 
```

Enter fullscreen mode Exit fullscreen mode

或者用 Fetch

```
const links = document.querySelectorAll('a')

Array.from(links).forEach((link) => {

    link.addEventListener("mouseenter", () => {

        fetch(link.href, { cache: "force-cache" })

    })

}) 
```

Enter fullscreen mode Exit fullscreen mode

这段代码有助于提高性能吗？我们有一个房地产项目。属性包含太多项目，如图像、文本等。

事实上，我不明白这是否如预期的那样有效。

你对此有什么看法？
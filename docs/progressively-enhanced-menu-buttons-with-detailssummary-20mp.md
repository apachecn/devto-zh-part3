# 带有详细信息/摘要的渐进增强菜单按钮

> 原文：<https://dev.to/marcush/progressively-enhanced-menu-buttons-with-detailssummary-20mp>

在本周研究 Vue 中菜单按钮的[可访问方式时，我忍不住打开浏览器的开发工具，每次看到“菜单按钮”,我都好奇各自的团队或开发人员是如何实现的。](https://marcus.io/blog/a11y-app-menu-button)

任何种类的“菜单”的概念经常出现在配置文件下拉菜单中。一些网站决定根本不使用语义菜单，“只是”在点击时显示一个 div 或列表，并将其内容按 tab 键顺序发布，例如，[dev . to)](https://dev.to/)。

GitHub 的方法使用菜单的细节和摘要元素，使得整个结构即使没有 JavaScript 也能工作(几乎每个浏览器都有)。下面是他们方法的鸟瞰图:

```
<details>
    <summary>Click me</summary>
    <details-menu role="menu"> <!-- a custom element -->
        <li><button type="button" role="menuitem">Agnetha</button></li>
        <li><button type="button" role="menuitem">Björn</button></li>
        <li><button type="button" role="menuitem">Benny</button></li>
        <li><button type="button" role="menuitem">Anni-Frid</button></li>
    </details-menu>
<details> 
```

我不会试图进一步解释它，因为:

*   GitHub 把他们的方法放到了 GitHub 上:[`<details-menu>`自定义元素](https://github.com/github/details-menu-element)
*   GitHub 的 Web 系统工程师 Mu-An Chiou 不仅做了一个关于他们技术的演示(链接到幻灯片)，还提供了一个关于细节的[*文档*](https://github.com/muan/details-on-details)*以及更多的链接和轶事*
**   Mu-An 在 Twitter 上很平易近人，主动提出谈论她的方法*
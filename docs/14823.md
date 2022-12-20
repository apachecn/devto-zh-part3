# JSON 中的简历:jsonresume-theme-stackoverflow

> 原文：<https://dev.to/phoinixi/resume-in-json-jsonresume-theme-stackoverflow-4hi8>

### JSON 中的简历🤓

几年前，我发现了 [jsonresume](http://jsonresume.org) ，这是一个非常有前途的项目，旨在提供一个 json 模式来写简历。

我立刻兴奋起来，为此我写了两个模板:👨🏼‍💻

*   我为🕺感到骄傲的 : [代码](https://github.com/phoinixi/jsonresume-theme-stackoverflow)和[演示](https://phoinixi.github.io/website/resume/stackoverflow)
*   另一个我不再喜欢的😆

### 我的第一份手工制作的简历模板🌟

StackOverflow 模板是我用网络技术写的第一份简历，它很受欢迎，我收到了投稿和特写请求。我自己用它做简历，我的一群朋友也用它。然而，这个模式不是很灵活，最后一次迭代是在 5 年前完成的 [json-resume-schema](https://github.com/jsonresume/resume-schema) 。

目前为 **json-resume** 写模板的栈也挺老的: **Handlebars.js** 。

### 需要重写😎

我的想法是使用不同的技术完全重写我的 StackOverflow 模板，(可能还有 json-schema，但这是另一个故事了)，我想到的最合理的是:

*   反应
*   [自定义元素](https://developer.mozilla.org/en-US/docs/Web/Web_Components/Using_custom_elements)

### 的帮助

*   有人对那个项目感兴趣吗？
*   你建议我选择哪些技术，为什么？
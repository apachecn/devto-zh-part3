# 我开发了一个由 Vue.js 制作的块编辑器

> 原文：<https://dev.to/takitakit/i-developed-a-block-editor-made-by-vue-js-d8f>

这是一个演示页面。

[https://codepen.io/takitakit/embed/RmpVKL?height=600&default-tab=result&embed-version=2](https://codepen.io/takitakit/embed/RmpVKL?height=600&default-tab=result&embed-version=2)

# 背景

如果我制作 WordPress 或 CMS 并提供给我的客户，
我发现 WYSIWYG 编辑器存在以下问题:。

*   除非你习惯了，否则很难使用。
*   即使习惯了，也有标记可能崩溃的情况。
*   限制标记输出的格式很困难或很麻烦

作为 WYSIWYG 编辑器的替代方案，我们开发了一个满足以下要求的编辑器:。

*   好用，即使不习惯也好用
*   最小化标记编辑
*   轻松限制加价

你可以在 Github 上找到更多信息。

# 这个编辑器能做什么

*   编辑预定块元素的任意组合，并整体输出单个标记。
*   从隐藏元素导出或导入标签(假设在管理屏幕上发布)

为了提高可用性，您不能直接编辑 HTML 标记或直接从 Word 粘贴样式文本。

# 用法

可以使用的块元素类型有:。

*   段落(文本+图像)
*   标题
*   列表。
*   桌子
*   列(嵌套非列元素)
*   HTML(输入任何 HTML 标记)

对于每个块元素，标记输出都有些特定。
例如，对于一个段落:。

```
<div class="paragraph-wrap align-left">
    <div class="text">
        "<a href="https://github.com/takitakit/block-editor-vue" target="_blank">block-editor-vue</a>" is a block editor that allows you to stack any combination of block elements, such as paragraphs, headings, lists, and so on.<br>If you are using the CMS management screen, you can use this editor as a means of editing the body HTML of an article.
    </div>
</div> 
```

类名(段落换行)可以作为一个选项进行更改，以便在各种情况下使用。

## 其他特性

*   限制可用的块元素类型。[演示](https://codepen.io/takitakit/pen/XwpeEj/)

*   使用特定的块元素、类名等命名预设。[演示](https://codepen.io/takitakit/pen/arNwGv/)

*   在块元素中应用文本样式。[演示](https://codepen.io/takitakit/pen/OYWzmg/)

如果你读了这篇文章，有任何意见或要求，请把你的意见发给我。
# 将调色板从 Figma 导出到 JSX

> 原文：<https://dev.to/aexol/export-color-palette-from-figma-to-jsx-5382>

# 如何将 figma 的颜色导出到 JSX？

我写了简单的正则表达式，可以在 VS 代码中使用。

1.  选择您想要导出的所有颜色
2.  转到 figma 中的代码选项卡
3.  将所有代码复制到 VS 代码中的文本文档
4.  使用我的 regex1 `\/\* (.*) \*\/\nbackground: #(.*);`和 in 替换`['$1']: '#$2'`
5.  使用另一个正则表达式删除不需要的 CSS `position: absolute;\nleft:(.*);\nright:(.*);\ntop:(.*);\nbottom:(.*);\n`并替换` ` in

记得在 VS 代码中的查找和替换中打开正则表达式

对于巨大的调色板来说，它可以节省 30 分钟来编写这个文件。这是有点 hacky，所以请告诉我是否有任何其他方法。
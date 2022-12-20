# 缩小 HTML 文件的快速方法

> 原文：<https://dev.to/agmm/a-quick-way-of-minifying-an-html-file-9k9>

*自我提醒*

一种快速缩小包含`<style>`和`<script>`标签的 HTML 文件的方法。

1.  首先用
    `npm install html-minifier -g`安装 HTML minifier

2.  执行 minifier 命令:
    `html-minifier --minify-css true --minify-js true input.htm -o output.htm`

附加选项:
`--collapse-whitespace
--remove-comments
--remove-optional-tags
--remove-redundant-attributes
--remove-script-type-attributes
--remove-tag-whitespace
--use-short-doctype`
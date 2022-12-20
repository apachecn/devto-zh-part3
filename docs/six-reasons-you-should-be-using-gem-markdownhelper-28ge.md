# 你应该使用 Gem markdown_helper 的六个理由

> 原文：<https://dev.to/burdettelamar/six-reasons-you-should-be-using-gem-markdownhelper-28ge>

[透明性:我建立这个东西。]

使用 gem [markdown_helper](https://rubygems.org/gems/markdown_helper) ，您可以:

*   使用[文件包含](https://github.com/BurdetteLamar/markdown_helper/blob/page_nav/markdown/use_cases/include_files/reuse_text/use_case.md#reuse-text)让你的文档保持干燥(不要重复)。

*   [包含动态生成的文本](https://github.com/BurdetteLamar/markdown_helper/blob/page_nav/markdown/use_cases/include_files/include_generated_text/use_case.md#include-generated-text)。特别是，您可以通过不断地重新运行和重新包含它们来保持您的示例“绿色”。

*   [嵌套文件内含物](https://github.com/BurdetteLamar/markdown_helper/blob/page_nav/markdown/use_cases/include_files/nest_inclusions/use_case.md#nest-inclusions)。

*   包括文本为:

    *   [降价](https://github.com/BurdetteLamar/markdown_helper/blob/page_nav/markdown/use_cases/include_files/include_markdown/use_case.md#include-markdown)。
    *   [代码块](https://github.com/BurdetteLamar/markdown_helper/blob/page_nav/markdown/use_cases/include_files/include_code_block/use_case.md#include-code-block)。
    *   [高亮显示的代码块](https://github.com/BurdetteLamar/markdown_helper/blob/page_nav/markdown/use_cases/include_files/include_highlighted_code/use_case.md#include-highlighted-code)。
    *   [预格式化文本](https://github.com/BurdetteLamar/markdown_helper/blob/page_nav/markdown/use_cases/include_files/include_text_as_pre/use_case.md#include-text-as-pre)。
    *   [降价评论](https://github.com/BurdetteLamar/markdown_helper/blob/page_nav/markdown/use_cases/include_files/include_text_as_comment/use_case.md#include-text-as-comment)。
*   包括自动生成的[页面 TOC](https://github.com/BurdetteLamar/markdown_helper/blob/page_nav/markdown/use_cases/include_files/include_page_toc/use_case.md#include-page-toc) (目录)。

*   使用任一界面:

    *   CLI (无需 Ruby 编码)。
    *   Ruby 代码中的 API。
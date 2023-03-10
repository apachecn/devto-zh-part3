# Gem markdown_helper 有更简单的页面目录

> 原文：<https://dev.to/burdettelamar/gem-markdownhelper-has-easier-page-toc-1a61>

我已经更新了红宝石宝石`markdown_helper`。

改进了目录页:

*   **Old** :您将首先运行 markdown 助手来生成一个页面目录，然后再次运行助手来将页面目录包含到您想要的位置。

*   **New** :你在页面本身指定页面目录的位置，页面目录自动生成并插入到那里。参见[用例](https://github.com/BurdetteLamar/markdown_helper/blob/master/markdown/use_cases/include_files/include_page_toc/use_case.md#include-page-toc)。

请注意，页面目录是在任何降价文件包含之后*创建的，因此目录覆盖了整个完成的页面。*

旧的方法现在已经过时了。

*   RubyGems.org 的宝石展。
*   Github 上的[文档](https://github.com/BurdetteLamar/markdown_helper#markdown-helper)。
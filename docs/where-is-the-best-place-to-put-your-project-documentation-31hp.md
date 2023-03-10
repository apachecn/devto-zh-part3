# 项目文档最好放在哪里？

> 原文：<https://dev.to/chrisrhymes/where-is-the-best-place-to-put-your-project-documentation-31hp>

创建一个项目、库或包有时看起来很难，但是如果你想让其他人使用它，你可能要花相当多的时间在文档上来帮助其他人开始使用你的包。如果你不花时间去写一个特性，人们不会知道如何使用它，因为他们甚至不知道它的存在。

那么写文档有哪些不同的方法呢？

## 自述

自述文件是最好的起点。自述文件通常是对主要功能以及如何开始安装和使用项目的一页摘要。自述文件很重要，因为它通常是你的代码库在 GitHub 上的登陆页面，所以尽量简洁明了。

自述文件通常以 markdown 格式编写，因此允许基本格式，如标题、段落、表格和代码片段，所以请确保在需要的地方添加代码示例。

如果你的自述文件很长，那么试着在目录中加入标题和副标题的链接，让读者快速跳转到他们需要的部分。

如果你的自述文件太长，你需要把它分成不同的页面，那么你下一步该怎么做呢？

## 维基

如果你正在寻找一个快速简单的解决方案，为什么不使用维基呢？大多数 git 托管服务允许你打开 wiki 并开始写页面。像 readme 一样，wiki 通常允许 markdown 格式，以及页面之间的链接。向你的 git 主机查询他们使用的降价类型以及链接页面的最佳方式。

从链接到其他页面的目录页开始，并尝试确保有一种简单的方法返回目录页或继续到文档的下一页。

如果你对一个基本但功能性的 docs 站点满意，那么你可以就此打住，但是如果你想包含真实的例子，比如 CSS 或 JavaScript 例子，那么 wiki 就不能满足你的要求。

## GitHub 页

如果你想用内嵌的例子(恐怕不是服务器端代码)创建一个更加定制的站点，那么你可以使用 GitHub 页面来免费托管你的项目站点(也有其他的选择，比如 BitBucket 页面)。

GitHub pages 允许您从头开始创建自己的 html 页面，或者使用 Jekyll。如果你使用 Jekyll，那么当你上传到 GitHub 时，它会从你的 markdown 文件中构建你的 html。如果你制作自己的 HTML 页面或者使用其他静态站点生成器，那么你需要在本地机器上编译 HTML，然后将编译好的 HTML、CSS 和 JavaScript 上传。

我个人使用 Jekyll 托管我的站点和项目，但我也在另一种情况下使用 VuePress 用 JavaScript 编译 markdown。如果你想展示它们，VuePress 还允许你使用你自己的 Vue 组件。

如果你竭尽全力为你的文档制作一个专用网站，那么你会想让人们知道它的存在。如上所述，GitHub 页面只允许前端代码，但它也允许您使用某些 Jekyll 插件(如果您正在使用 Jekyll)来允许一些附加功能，如网站地图、seo 优化和 RSS 提要，以使您的生活更轻松，并使您的网站更容易被搜索引擎看到。

最后一件事是确保在你的 GitHub repo 描述和自述文件中添加一个到你的文档站点的链接。这将允许搜索您的包的人找到您的文档站点，以及 GitHub 中的代码。
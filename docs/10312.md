# 用 PHP 和 WordPress 反应 SSR

> 原文：<https://dev.to/li/react-ssr-with-php-and-wordpress-32j1>

我正在寻找一个解决方案，使用反应的字体端和后端与以下要求的 WordPress:

*   构建时没有静态呈现:
    *   起初，我实际上在考虑 Gatsby 或 React Static，但它要求不要在每次内容更新时都重新构建
*   没有 node.js 中间件:
    *   无法添加中间件来调用 WordPress api，然后呈现 React
*   没有经典水疗:
    *   出于 SEO 和性能方面的考虑，我们不希望从响应中返回一个空白页面

我们正在寻找的一个潜在解决方案是使用 Gatsby 或 React Static 来构建，然后使用 PHP 用最新的内容更新生成的 HTML 和 JSON，在我看来这并不是一个理想的解决方案；所以去看看有没有其他的解决方法。

非常感谢！
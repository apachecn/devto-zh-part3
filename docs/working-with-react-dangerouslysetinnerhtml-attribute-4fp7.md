# 使用 react dangerouslySetInnerHTML 属性

> 原文：<https://dev.to/skptricks/working-with-react-dangerouslysetinnerhtml-attribute-4fp7>

帖子链接:[使用 react dangerouslySetInnerHTML 属性](https://www.skptricks.com/2018/12/working-with-react-dangerouslysetinnerhtml-attribute.html)

本教程解释了如何在 react 应用程序组件中使用 react dangerouslySetInnerHTML 属性。dangerouslySetInnerHTML 是 React 对在浏览器 DOM 中使用 InnerHTML 的替代。一般来说，从代码中设置 HTML 是有风险的，因为很容易无意中让您的用户受到跨站点脚本(XSS)攻击。所以，你可以直接从 React 设置 HTML，但是你必须键入 dangerouslySetInnerHTML 并传递一个带有 __html 键的对象，以提醒自己这是危险的。

注意:不正确地使用 innerHTML 可能会使您遭受跨站点脚本(XSS)攻击。众所周知，净化用户输入以供显示是容易出错的，而未能正确净化是互联网上 web 漏洞的主要原因之一。

[使用 react dangerouslySetInnerHTML 属性](https://www.skptricks.com/2018/12/working-with-react-dangerouslysetinnerhtml-attribute.html)

[![](img/545afa6df013ad7396ab12e1e4c3a16e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bIx4TiV3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://1.bp.blogspot.com/-WIq-Rz2iXsw/XAS8h5qQhlI/AAAAAAAACNQ/Y8YCzIXG-yMLC3gi78j4WMEkf63nFdIYwCLcBGAs/s400/danger.png)
# 分割重复静态 html 的最佳方法？

> 原文：<https://dev.to/prnthh/best-way-to-partition-repetitive-static-html-1fgm>

我想为一个网站制作多个页面，它们都有共同的页眉和页脚段。编写这段代码的最佳方式是什么，这样我就不必在多个 html 文件中做相同的修改了？

我猜现代的方法是使用 React-router，将重复的页眉和页脚声明为组件，然后使用 Gatsby 之类的东西将其呈现在服务器端。

我还遇到了 [handlebars.js](https://handlebarsjs.com/) ，这似乎是我正在寻找的，但我想选择一种初始设置最少的方法，或者至少是一个易于重现的环境。
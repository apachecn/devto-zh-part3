# 为什么浏览器不能读取 JSX？

> 原文：<https://dev.to/cloud_developr/why-can-t-browsers-read-jsx-5boc>

浏览器只能读取 JavaScript 对象，但 JSX 不是一个普通的 JavaScript 对象。因此，要使浏览器能够读取 JSX，首先，我们需要使用像巴别塔这样的 JSX 转换器将 JSX 文件转换成 JavaScript 对象，然后将它传递给浏览器。
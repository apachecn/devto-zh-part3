# 如何禁用 WordPress 中的表情符号

> 原文：<https://dev.to/lucagrandicelli/how-to-disable-emojis-in-wordpress-4bja>

当谈到网站速度性能时，限制 HTTP 调用的数量是至关重要的，即使我们节省了几个字节。

从 4.2 版开始，Wordpess 在老版本浏览器的核心中引入了对表情符号的支持，但很可能你根本不需要它。因此，这里有一个小要点可以帮助你，删除对 wp-emoji-release.min.js 文件的任何引用。
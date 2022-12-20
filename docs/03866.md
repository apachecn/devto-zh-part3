# Js 版本疑问

> 原文：<https://dev.to/tharund/js-versioning-doubt-30j6>

嗨伙计们，

我看到了一些我不理解的东西。我有一个 abc.com/script.js 网站，使用 cloudflare 作为 cdn，在 aws 中运行良好。

几天后，我修改了 script.js，但它没有立即在 abc.com/script.js 得到反映，但在 abc.com/scrpit.js?1 得到了反映

有人能解释一下吗？我清空了 cdn 中的缓存，但 script.js 仍然没有反映到新的缓存中。
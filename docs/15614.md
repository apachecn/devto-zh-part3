# 只有在没有*免费* SSL 提供商的世界中，CAA 记录才有意义

> 原文：<https://dev.to/kapouer/caa-records-only-have-a-meaning-in-a-world-without-free-ssl-providers-34cb>

`thatsheepdomain.com CAA 0 "evilexpensiveauthority.com"`

五年前出了一个好看的[https://tools.ietf.org/html/rfc6844](RFC%206844):DNS CAA。
这看起来很酷，当它不存在时，甚至 ssllabs 也会显示警告。

我认为这很棒，[四处游说](https://github.com/AnalogJ/lexicon/issues/282)支持它！

除了今天我在这里警告你关于那件事。

CAA 记录是为了让组织确保每个人都在给定的根域名下为网站使用相同的 SSL 证书颁发机构。

所有提供`xxx.thatsheepdomain.com`的承包商都需要从“evilexpensiveauthority.com”获取证书。

这通常作为公司政策强制执行，以确保只有一个证书提供者——并且只有一种方法来处理这些可怕的证书——毕竟，未能更新它们可能会中断服务。

这种公司内部政策一直存在，有时理由非常充分，现在可以通过官方标准强制执行。有很多方法可以让浏览器使用它。Mozilla 接受了它，[让我们加密](https://letsencrypt.org/docs/caa/)尊重它。

好看吗？

但细节决定成败。

几年来，Let's encrypt +开源 acme protocol +开源 certbot 客户端(或任何其他 acme 兼容的客户端)一直在提供数亿份免费的 SSL 证书，促使“遗留”公司(那些要钱的公司，其员工编写了关于 CAA 的 RFC，只需查看链接)推动他们的客户使用 CAA 记录，作为对难以忍受的开放和自由运动的反应。

我认为非自由公司使用“害怕安全问题”策略。请迅速执行公司政策，否则将面临地狱。
采用这种政策的公司将确保没有完全不同的 SSL 授权承包商。

神盾局。

CAA 记录只有在没有免费 SSL 提供商的世界里才有意义。
# 是时候升级 RubyGems 了

> 原文：<https://dev.to/philnash/time-to-upgrade-rubygems-2m7o>

当心卢布主义者！今天，RubyGems 披露了大量安全漏洞。

为了保护自己免受可能删除你机器上任意文件的 gems 的攻击，在你的终端中注入不安全的转义序列或者*在你的机器上运行任意代码*那么是时候升级 RubyGems 了。

要升级，请运行:

```
gem update --system 
```

Enter fullscreen mode Exit fullscreen mode

要阅读更多关于漏洞的信息(如果你使用的是早于 2.7 的 RubyGems 版本并且无法升级，也可以阅读更多信息)，请查看[公告帖子](http://blog.rubygems.org/2019/03/05/security-advisories-2019-03.html)。

在外面注意安全！
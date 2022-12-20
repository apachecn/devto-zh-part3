# 已解决:自定义域和 github 页面的隐私错误

> 原文：<https://dev.to/priteshusadadiya/privacy-error-with-custom-domain-in-and-github-pages-2ll2>

大家好，

我试图在 github 上托管[我的 hugo 网站，自定义子域【https://www.priteshusadadiya.com](https://github.com/priteshusadadiya/priteshusadadiya.github.io)[结果为](https://www.priteshusadadiya.com)

> 您的连接不是私人的

当我从 url 中删除 https 时，会导致一个没有引导样式的网站。

我遵循了 [github 的故障排除](https://help.github.com/articles/troubleshooting-custom-domains/#https-errors)指南，但运气不好。

这里是 DNS **的一个**记录。

185.199.108.153
185.199.109.153
185.199.110.153
185.199.111.153

我还增加了一项 CNAME 记录

www priteshusadadiya.com CNAME

我也尝试了删除和重新添加我的自定义域到资源库，仍然没有运气。

回购:[https://github . com/priteshusadadiya/priteshusadadiya . github . io](https://github.com/priteshusadadiya/priteshusadadiya.github.io)

尝试搜索 stackoverflow 和 github 问题的解决方案，但没有找到任何解决方案。有人能帮我调试一下吗？

谢谢，请

* * *

## 更新

终于发现我的 DNS 配置有什么问题了？

问题是我已经创建了一个子域`WWW`，它指向顶点(主)域 priteshusadadiya.com

```
CNAME record

www CNAME   priteshusadadiya.com 
```

Enter fullscreen mode Exit fullscreen mode

我要做的就是让它指向`priteshusadadiya.github.io`

```
CNAME record

www CNAME   priteshusadadiya.github.io 
```

Enter fullscreen mode Exit fullscreen mode

而且成功了。🙌
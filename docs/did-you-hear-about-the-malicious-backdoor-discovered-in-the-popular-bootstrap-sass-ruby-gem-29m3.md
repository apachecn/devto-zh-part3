# 你听说过流行的 bootstrap-sass Ruby gem 中发现的恶意后门吗？

> 原文：<https://dev.to/lirantal/did-you-hear-about-the-malicious-backdoor-discovered-in-the-popular-bootstrap-sass-ruby-gem-29m3>

我最近分享了事件的[概要和后门背后的技术细节](https://snyk.io/blog/malicious-remote-code-execution-backdoor-discovered-in-the-popular-bootstrap-sass-ruby-gem/)，该后门被明智地隐藏在 3.2.0.3 版本的 bootstrap-sass 中，这是一个流行的 ruby gem，自 8 年前添加到知识库以来已被下载了 2800 万次。

恶意版本允许远程攻击者在托管易受攻击版本的服务器上动态执行代码，方法是发送一个特制的 HTTP 请求，该请求将有效负载隐藏在一个看似无害的 cookie 中🍪。

由于没有日志和证据来追溯这是如何发生的，维护者怀疑 gem 是使用他们两个中有发布权限的一个被破坏的帐户发布的。

我们以前在 JavaScript 社区也听说过这种事情。一个很好的例子是 eslint-scope 包。

## 我们能做些什么呢？

对于维护人员和开发人员来说，提升他们的安全水平是非常重要的，这一点我怎么强调都不为过。我为 JavaScript 开发人员编制了一个列表，列出了 10 个 npm 安全最佳实践，至少在 RubyGems 存储库上启用 2FA 是必须的。

如果你使用的是 Snyk，我们已经更新了漏洞数据库，以防你使用的是恶意版本。
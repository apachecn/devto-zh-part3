# 用户喜欢无密码登录吗？

> 原文：<https://dev.to/mathieud/do-users-like-passwordless-logins-35bp>

如果没有真正的需要，我不会询问客户信息。

当 kanochart 所有的调查都是免费的时候，没有必要询问电子邮件地址，更不用说让用户创建账户了。

因此，当我实施付费调查时，我做了一些研究来寻找注册表格的替代方法。我不希望用户必须设置密码或注册社交账户。

一种方式是[无密码登录](https://medium.com/findworkco/password-less-login-df0354c3f3ee)。你只需要给出你的电子邮件地址，你就会得到一个带有唯一登录令牌的链接。你点击链接并登录，准备访问你的帐户。

[![](img/14ce4506e1cc681d3ef77e0b9cf23f35.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UXckh2T4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/23xq2pfuvqqpmqljh7mv.png)

我使用 [flask-security](https://flask-security-fork.readthedocs.io/en/latest/) 实现了这个特性。虽然医生说这个功能是实验性的，但是一切都很好。

在我看来，无密码登录很棒。然而，我想知道用户是否真的喜欢它。

你们有没有人有过无密码登录的经验，用户是否喜欢？

提前感谢！
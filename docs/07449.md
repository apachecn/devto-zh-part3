# 让会话变量在。网络核心

> 原文：<https://dev.to/catriname/making-session-variables-work-in-net-core-3599>

如上所述，我使用 Legacy，并且经常必须从 API 中引入变量，这些变量必须在整个会话中保持不变..(而且我肯定可能有更好的办法，评论和建议！).我现在的情况是，我查询 API，引入变量，但我如何避免一遍又一遍地调用这些变量:旧的解决方案是会话变量，这就是我现在的情况。

当我开始在 Core 上这样做时，最有帮助的文章是这样的(在我的评论里):[https://adamstorr . azure websites . net/blog/are-you-registrating-ihttpcontextaccessor-correctly](https://adamstorr.azurewebsites.net/blog/are-you-registering-ihttpcontextaccessor-correctly)

他引导您完成 HttpContext helper 类的基本设置(我今天还在使用它)以及如何配置启动..也许我迷路了，但是今天我遇到了一个问题:我能够设置会话变量，但是 Get 却拉空。

为什么！！？？

秩序。是的，您将在 Configure 中看到 1000 个关于订单的 stackflow 响应，但现在是在 **ConfigureServices** (与示例相反，因为我现在使用的是 Core 2.2？)，订单再次进场:

```
public void ConfigureServices(IServiceCollection services){ //this MUST be before add mvc or session returns null services.AddSingleton<IHttpContextAccessor, HttpContextAccessor>(); services.AddMvc(); 
```

错误是如何出现的？全文:[https://www . cat rina . me/making-session-variables-work-in-net-core/](https://www.catrina.me/making-session-variables-work-in-net-core/)
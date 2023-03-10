# 如何通过集成新遗迹和 Bugfender 来修复服务器问题

> 原文：<https://dev.to/bugfenderapp/how-to-fix-server-issues-by-integrating-new-relic-with-bugfender-2p8b>

[![](img/4d6bf94b8980be081b7665bd4a9dad48.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--S0A8UZd_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bugfender.com/wp-content/uploads/2019/04/new-relic-integration.jpg)

[新的遗迹](https://newrelic.com/)改变了应用性能监控市场的游戏规则。它的收入接近 10 亿美元，用户已经超过 17，000 人。

现在，我们想解释如何将这款神奇的产品与 Bugfender 集成，以确保您的移动应用程序的端到端可追溯性。

## **有什么新遗物？**

New Relic 的 APM 是一款软件，可提供关于您的 web 应用程序性能和最终用户体验满意度的实时趋势数据。它还跟踪后端代码中的错误，因此当客户发现错误时，您可以轻松地修复它们。

## **我们为什么需要新遗迹整合？**

如今大多数移动应用程序都与后端有某种形式的通信；app 与世隔绝的时代早已过去。

然而，这意味着开发人员在开发他们的应用程序时，还必须考虑到后端的潜在问题。即使你是一个出色的移动开发者，并且你的代码几乎没有错误，你的客户可能仍然会发现其中的错误。

因此，当使用 Bugfender 来监控你的应用程序时，安装一个监控后端发生的事情的解决方案也很重要。

新遗迹是实现这一目的的理想之选。该工具使开发人员能够跟踪后端代码中的错误，并识别服务器响应时间中的任何问题，使开发人员能够优化它。

通过结合 Bugfender 和 New Relic，我们可以创建一个完整的监控解决方案，提供 100%的可追溯性。Bugfender 在应用的前端识别问题，然后开发者可以使用 New Relic 追踪问题到服务器。

## **如何识别请求**

但是我们仍然需要正确标记应用程序和服务器之间的所有请求。通过标记每个请求，开发人员可以向后工作，并与后端团队协商，以检查为什么该请求的行为不符合预期。

但是我们如何确保所有的请求都被正确地标记呢？

我们需要做的是给每个请求添加一个自定义的头。一个好的技巧是将这个头称为 X-Request-Id。

此外，建议您添加 Bufender 设备 ID 和会话，这样，如果后端团队在他们的监控系统上发现一些奇怪的东西，您可以恢复应用程序日志。

如何添加此标题取决于您在应用中使用的网络库。下面是一个 Android 示例，它将这些自定义标题放在一个 HashMap 中。

```
Map<String, String> params = new HashMap<String, String>();
params.put("X-Request-Id", UUID.randomUUID().toString());
params.put("X-Bugfender-Id", Bugfender.getDeviceIdentifier());
params.put("X-Bugfender-Session", Bugfender.getSessionIdentifier()); 
```

## **新文物自定义标题跟踪**

默认情况下，New Relic(至少对于 PHP 来说)不会跟踪所有的自定义头。即使我们在应用程序中添加它们，它们也不会在 New Relic 中提供。因此，我们正确配置 New Relic 来保存所有请求头是至关重要的。

为了跟踪这些客户头，我们将使用[new relic _ add _ custom _ parameter](https://docs.newrelic.com/docs/agents/php-agent/php-agent-api/newrelic_add_custom_parameter)函数。这里有一个你可以使用的代码的例子:

```
$headers = $app->request->headers;

 if ($headers->has("X-Request-Id")) {
   if (extension\_loaded('newrelic')) {
     newrelic\_add\_custom\_parameter("request-id", $headers->get("X-Request-Id"));
   }
 }

 if ($headers->has("X-Bugfender-Id")) {
   if (extension\_loaded('newrelic')) {
     newrelic\_add\_custom\_parameter("bugfender-id", $headers->get("X-Bugfender-Id"));
   }
 }

 if ($headers->has("X-Bugfender-Session")) {
   if (extension\_loaded('newrelic')) {
     newrelic\_add\_custom\_parameter("bugfender-session", $headers->get("X-Bugfender-Session"));
   }
 } 
```

这里你可以看到新遗迹追踪这些信息。这是一个错误的屏幕截图:

[![](img/91f0facba57c84c831a3ef19d69394a0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--84RJXa2F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh4.googleusercontent.com/8Xrd803V_MQiuZPp90YnzbD9NI6FkLDNZzlvMpohAzxfB94XlVbzWeJ-YKzakznFmSSgnPTFy82pq-BRfzrhWGJkClhHoGg4_gCEhbFvuTzmg_IfMw0UZmNbOsga7XRVQaS-G14)

有了这个代码，你将能够实现从移动应用到后端的问题的完全可追溯性。狩猎愉快！
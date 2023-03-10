# 通过上下文跟踪更改

> 原文：<https://dev.to/luizdamim/tracking-changes-with-context-35ek>

想象一个网店谁卖...任何事，真的。用户下单购买他们想要的产品。但是有时候他们会在买东西后两分钟后悔自己的决定(T2)。有时*支付网关*会出现*问题*。有时你的仓库出了问题，产品*缺货*。有时候因为无法*发货到用户地址*或者其他任何结账时无法验证的约束而无法销售(请请在结账时验证一切可以验证的，皆大欢喜:)。

所有这些可能性都会导致订单取消。在后台，应用程序将订单的`status`字段从`processing`更新为`cancelled`，并记录日期/时间。就是这样。对吗？对吗？

## 有时候光知道*变了什么还不够。有必要知道*为什么*会改变。*

 *如果你不知道**为什么**会发生一些事情，你就失去了对你的应用程序的洞察力，使你无法采取行动。

* * *

**例 1:用户没有足够的资金**

付款被拒绝，因为用户没有足够的资金。应用程序自动取消订单。

字段`gateway`和`reason`可以由您的应用程序的支付工作流和网关处理器返回的`error_code`来设置。

```
{  "event":  "order_cancelled",  "id":  42,  "actor_id":  null,  "metadata":  {  "reason":  "payment_declined",  "error_code":  "INSUFFICIENT_FUNDS",  "gateway":  "IPayU"  ...  }  } 
```

*虽然在这个例子中* `actor_id` *是* `null`，*我通常有一个应用程序用户，它被分配给应用程序中的每个自动化动作。*

**例 2:无法发货到用户地址**

用户在下订单后更改了他们的送货地址，无论出于什么原因，您都无法送货到那里。

这里`reason`和`description`是一个员工手动分析问题后告知的。

```
{  "event":  "order_cancelled",  "id":  42,  "actor_id":  19,  "metadata":  {  "reason":  "shipping_address",  "description":  "User changed his address, can't ship to the new one."  ...  }  } 
```

这些都是人为的例子。有比取消订单更好的方式来处理这些情况。

* * *

在下一篇文章中，我将展示一种在仙丹中使用*埃克托*和*凤凰*的方法。

*非常感谢来自* [*的 Allan Jorge、@gustavoaguiar 和@tyurok 帮助我克服写作障碍/写第一篇文章的恐惧。*](https://t.me/elixirbr_offtopic)*
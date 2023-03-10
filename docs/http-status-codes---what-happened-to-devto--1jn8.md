# HTTP 状态代码-dev . to 发生了什么？

> 原文：<https://dev.to/yafkari/http-status-codes---what-happened-to-devto--1jn8>

大家好，今天，我将谈论 HTTP 状态码。

昨天晚上 7:30，我在看 [Github](https://github.com/) ，不知道为什么，我需要访问[dev to](//dev.to)。
但是当我试图加载这个页面时，我收到了一个漂亮的“错误 503 ”,上面有一只狗在看着我！😝

这就是我发现自己写这个小帖子给你的原因。

“永远要看到生活的光明面。”🌔、*T3、我妈 T5。*

## 如此...开始了。

首先，为什么我们需要一个状态代码？

状态码是来自服务器的一个小消息，告诉你你的请求进展如何(是否一切顺利，等等)...).因此你可以想象得到请求状态的反馈是多么的必要。

有几种状态代码，每一种都专用于特定的情况。

## 类别

HTTP 状态代码分为 5 个不同的类别，由组成它们的第一个数字定义。

以下是在第 47 页的 **[RFC 7231](https://tools.ietf.org/html/rfc2616) 中描述的类别列表:**

*   **1xx** :信息-请求已收到，正在继续处理
*   **2xx** :成功-行动被成功接收、理解和接受
*   **3xx** :重定向-为了完成请求，必须采取进一步的行动
*   **4xx** :客户端错误-请求包含错误的语法或无法实现
*   **5xx** :服务器错误——服务器未能满足一个明显有效的请求

## 快速查看最常见的状态代码

排名第一的当然是著名的:

*   **错误 404 未找到**
    [![ERr0r4O4](img/e6e253fe7eae9c9fd89ff7d3b1e2940b.png)](https://i.giphy.com/media/zBEQkZ5CR6WFa/giphy.gif) 
    当您询问的资源不存在时会出现此错误，请尝试检查拼写并保持冷静🙏😄。

*   **503 服务不可用**
    [![ERr0r5O3](img/8f3224bddef7c7bd119bbee8ea02103e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dFPUdHgd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jazci8094gkjigr3rwgf.gif) 
    昨天我有幸看到的错误主要出现在服务器忙的时候(请求太多过载？)或当服务器处于维护状态时。
    一般都是暂时的，耐心点！

*   **200 OK**
    [![are you sure everything is fine ??](img/5efb7d8e6ea436abae4bc127b24be49d.png)](https://i.giphy.com/media/l3q2LH45XElELRzRm/giphy.gif)
    一个普通用户甚至不知道它的存在，这个状态码意味着一切都很顺利。
    你要的是 X，我找到了，而且我已经给你看了。

*   **等...**
    [![an image](img/7865b58b10d00cf5171c700afd8e5546.png)](https://i.giphy.com/media/Tt9jctxaVjRny/giphy.gif) 
    有很多 HTTP 状态码，所以我让你自己看[这里](https://tools.ietf.org/html/rfc2616)。

如你所见，最常见的状态代码是错误😭😂。

这就是本文的内容，像每次一样，如果我忘记了什么，请随时提醒我😜

我知道，我知道，你很难过。但是我会尽快带着比之前更好的帖子回来。(希望如此)
# ANN:sirix . io 的 0.9.0 版本 XML 和 JSON 数据的高效版本化/支持时间旅行查询和区分

> 原文：<https://dev.to/johanneslichtenberger/ann-version-0-9-0-of-sirix-io-efficient-versioning-of-xml-and-json-data-supports-time-travel-queries-and-diffing-fnl>

[![SunburstView](img/83d073e0f27af01673c3a1991fc436b7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--I6FimSu7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9xggm4bqnvbbkwo8w5md.png)

嗨，

我已经在几天前发布了，但是我现在发布了 0.9.0 版本，也许对你们中的一些人有一些价值，我仍然希望至少是:-)

Sirix.io 是一个高效的数据版本。也就是说，一方面，它降低了在每次事务提交期间存储新修订的存储成本，同时通过新颖的滑动快照算法和动态页面压缩来平衡读写性能。另一方面，Sirix 支持简单的查询功能，例如通过时间戳打开特定修订或通过给定时间跨度打开修订，导航到树结构中节点的未来或过去版本等等。它基本上从不覆盖数据，并且深受 ZFS 的启发，借用了一些想法，并在子文件级别上进行测试。

类似这样的查询是可能的:

```
let $statuses := jn:open('mycol.jn','mydoc.jn', xs:dateTime('2019-04-13T16:24:27Z'))=>statuses
let $foundStatus := for $status in bit:array-values($statuses)
  let $dateTimeCreated := xs:dateTime($status=>created_at)
  where $dateTimeCreated > xs:dateTime("2018-02-01T00:00:00") and not(exists(jn:previous($status)))
  order by $dateTimeCreated
  return $status
return {"revision": sdb:revision($foundStatus), $foundStatus{text}} 
```

这一切始于 2006 年左右，当时是 Marc Kramis 的一个大学/博士项目，我已经在该项目上花费了我的学士论文、硕士论文以及几份 HiWi-Jobs，直到 2012 年，我仍然比以往任何时候都更渴望提出一个版本化的分析平台的想法，以基于数据的当前和历史执行分析任务。

在过去的几个月里，我在 Sirix.io 上花了很多时间，而且大部分晚上都是在我的日常软件工程工作之后度过的。我已经在圣诞节前后发了帖子，但现在除了 XML 之外，我还添加了一个原生的 JSON 存储层，即存储本身，用于联合 XML 和 JSON 查询的 JSON XQuery extensions，一个 RESTful-API，也用于 JSON，添加了一个网站、一个论坛和第一个文档...:-)昨晚我发布了 0.9.0 版本。

因此，任何反馈，意见，建议，只是一切都非常欢迎。我会愉快地(尽力？；))回答你可能有的任何问题。大家复活节快乐:-)

孩子看约翰尼斯

[https://sirix.io](https://sirix.io)

[https://github.com/sirixdb/sirix](https://github.com/sirixdb/sirix)

[![SunburstMoves](img/d295ead344b8027f3673b459af0a4246.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UqYBbp36--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lochycib8nkrn04e081n.png)
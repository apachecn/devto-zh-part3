# 文本挖掘、api 和解析 API 日志

> 原文：<https://dev.to/sckott/text-mining-apis-and-parsing-api-logs-3l4n>

## 获取全文文章

[fulltext](https://github.com/ropensci/fulltext/) 是我维护的一个 R 包，用来获取文本挖掘研究文章的全文版本。

这是一个很难解决的问题，有一大堆代码。其中一个难题是弄清楚一篇文章的全文版本的 URL 是什么。随着时间的推移，发布者没有一致的 URL 模式，所以你不能设置一次规则就再也不用重新访问它们。

[Crossref API](https://github.com/CrossRef/rest-api-doc) 为选择分享它们的出版商提供了全文版本的链接。然而，即使出版商选择分享他们的全文链接，它们也可能过时或完全错误(实际上不会导致全文)。

有各种各样的其他 API 可以获得文章的链接，但是没有一个真正达到目的，这导致了 [ftdoi API](https://ftdoi.org/) 的产生。

## ftdoi API

[ftdoi API](https://ftdoi.org/) 是一个 web API，它的主要目标是从它的 doi 获得一篇文章的全文版本的最佳猜测(这是通过`/api/doi/{doi}/`途径完成的)。API 返回所有可能的 URL，包括 pdf、xml 和 html。大多数出版商只提供 PDF 格式的全文，但当 XML 可用时，我们也会提供这些 URL。

API 使用在 [pubpatterns](https://github.com/ropenscilabs/pubpatterns/tree/master/src) repo 中维护的规则。这些规则只是粗略的指导方针，通常需要至少一个步骤向发布者站点或另一个站点发出 web 请求，这在 pubpatterns 规则中没有规定。例如， [Biorxiv 文件](https://github.com/ropenscilabs/pubpatterns/blob/master/src/biorxiv.json)有关于如何获得完整 URL 的必要部分的注释，但这样做的实际逻辑在 API 代码库[这里](https://github.com/ropenscilabs/pubpatternsapi/blob/master/utils.rb#L590-L601)。

ftdoi API 将请求的响应缓存 24 小时，因此即使处理一个请求需要 5 秒左右，至少在接下来的 24 小时内，它几乎是即时的。我们不想无限期地缓存，因为 URL 可能会被发布者随时更改。

`fulltext`包在内部使用 ftdoi API 来获得一个全文 URL，这对于用户来说是隐藏的。

## 但是为什么是 API 呢？

为什么不在 R 包中有一套规则，然后从那里开始呢？一个 API 对我来说比较容易建立，我认为它有很多好处:可以被任何人使用，而不仅仅是 R 用户；对用于生成 URL 的发布者特定规则的更新可以独立于`fulltext`而发展；日志可以用作改进 API 的工具。

## 人到底想要什么？

ftdoi API 已经运行了一段时间，可能有一年左右，我一直在收集日志。查看日志来确定用户正在请求 ftdoi API 当前不支持的文章的出版商似乎是明智的，这样 API 就有希望添加这些出版商。由于明显的原因，我不能共享日志数据。

加载包并定义文件路径。

```
library(rcrossref)  library(dplyr)  library(rex)  logs  <-  "~/pubpatterns_api_calls.log" 
```

日志如下所示(删除了 IP 地址，缩短了一些用户代理):

```
[28/Nov/2018:20:09:49 +0000] GET /api/members/ HTTP/2.0 200 4844 Mozilla/5.0 ...
[28/Nov/2018:20:23:15 +0000] GET /api/members/317/ HTTP/2.0 200 228 Mozilla/5.0 ...
[29/Nov/2018:01:52:58 +0000] GET /api/members/19/ HTTP/1.1 400 65 fulltext/1.1.0
[29/Nov/2018:01:52:58 +0000] GET /api/members/2308/ HTTP/1.1 400 65 fulltext/1.1.0
[29/Nov/2018:01:52:59 +0000] GET /api/members/239/ HTTP/1.1 400 65 fulltext/1.1.0
[29/Nov/2018:01:53:00 +0000] GET /api/members/2581/ HTTP/1.1 400 65 fulltext/1.1.0
[29/Nov/2018:01:53:00 +0000] GET /api/members/27/ HTTP/1.1 400 65 fulltext/1.1.0
[29/Nov/2018:01:53:01 +0000] GET /api/members/297/ HTTP/1.1 200 336 fulltext/1.1.0
[29/Nov/2018:01:53:01 +0000] GET /api/members/7995/ HTTP/1.1 400 65 fulltext/1.1.0
[29/Nov/2018:10:46:53 +0000] GET /api/members/unknown/ HTTP/1.1 400 65 fulltext/1.1.0.9130 
```

使用 Kevin Ushey 等人的 awesome [rex](https://github.com/kevinushey/rex/) 包来解析日志，只提取 API 请求中的 Crossref 成员 ID，以及 HTTP 状态代码。当然，日志中还有其他 API 请求，但是我们只对`/api/doi/{doi}/`路线感兴趣。

```
df  <-  tbl_df(scan(logs,  what  =  "character",  sep  =  "\n")  %>%  re_matches(  rex(  "/api/members/",  capture(name  =  "route",  one_or_more(numbers)  ),  "/",  space,  space,  "HTTP/",  numbers,  ".",  numbers,  space,  capture(name  =  "status_code",  one_or_more(numbers)  )  )  ))  df$route  <-  as.numeric(df$route)  df  #> # A tibble: 896,035 x 2  #> route status_code  #> <dbl> <chr>  #> 1 NA <NA>  #> 2 317 200  #> 3 19 400  #> 4 2308 400  #> 5 239 400  #> 6 2581 400  #> 7 27 400  #> 8 297 200  #> 9 7995 400  #> 10 NA <NA>  #> # … with 896,025 more rows 
```

过滤那些导致 400 HTTP 状态代码的请求，也就是说，它们没有导致任何返回数据，这表明我们可能没有该交叉引用成员的映射。

```
res  <-  df  %>%  filter(status_code  ==  400)  %>%  select(route)  %>%  group_by(route)  %>%  summarize(count  =  n())  %>%  arrange(desc(count))  res  #> # A tibble: 530 x 2  #> route count  #> <dbl> <int>  #> 1 10 345045  #> 2 530 7165  #> 3 286 3062  #> 4 276 2975  #> 5 239 2493  #> 6 8611 1085  #> 7 56 853  #> 8 235 722  #> 9 382 706  #> 10 175 590  #> # … with 520 more rows 
```

添加交叉引用元数据

```
(members  <-  rcrossref::cr_members(res$route))  #> $meta  #> NULL  #>  #> $data  #> # A tibble: 530 x 56  #> id primary_name location last_status_che… total.dois current.dois  #> <int> <chr> <chr> <date> <chr> <chr>  #> 1 10 American Me… 330 N. … 2019-03-20 600092 14714  #> 2 530 FapUNIFESP … FAP-UNI… 2019-03-20 353338 38339  #> 3 286 Oxford Univ… Academi… 2019-03-20 3696643 289338  #> 4 276 Ovid Techno… 100 Riv… 2019-03-20 2059352 167272  #> 5 239 BMJ BMA Hou… 2019-03-20 891239 61267  #> 6 8611 AME Publish… c/o NAN… 2019-03-20 20067 15666  #> 7 56 Cambridge U… The Edi… 2019-03-20 1529029 84018  #> 8 235 American So… 1752 N … 2019-03-20 178890 13984  #> 9 382 Joule Inc. 1031 Ba… 2019-03-20 12666 1868  #> 10 175 The Royal S… 6 Carlt… 2019-03-20 89219 7262  #> # … with 520 more rows, and 50 more variables: backfile.dois <chr>,  #> # prefixes <chr>, coverge.affiliations.current <chr>,  #> # coverge.similarity.checking.current <chr>,  #> # coverge.funders.backfile <chr>, coverge.licenses.backfile <chr>,  #> # coverge.funders.current <chr>, coverge.affiliations.backfile <chr>,  #> # coverge.resource.links.backfile <chr>, coverge.orcids.backfile <chr>,  #> # coverge.update.policies.current <chr>,  #> # coverge.open.references.backfile <chr>, coverge.orcids.current <chr>,  #> # coverge.similarity.checking.backfile <chr>,  #> # coverge.references.backfile <chr>,  #> # coverge.award.numbers.backfile <chr>,  #> # coverge.update.policies.backfile <chr>,  #> # coverge.licenses.current <chr>, coverge.award.numbers.current <chr>,  #> # coverge.abstracts.backfile <chr>,  #> # coverge.resource.links.current <chr>, coverge.abstracts.current <chr>,  #> # coverge.open.references.current <chr>,  #> # coverge.references.current <chr>,  #> # flags.deposits.abstracts.current <chr>,  #> # flags.deposits.orcids.current <chr>, flags.deposits <chr>,  #> # flags.deposits.affiliations.backfile <chr>,  #> # flags.deposits.update.policies.backfile <chr>,  #> # flags.deposits.similarity.checking.backfile <chr>,  #> # flags.deposits.award.numbers.current <chr>,  #> # flags.deposits.resource.links.current <chr>,  #> # flags.deposits.articles <chr>,  #> # flags.deposits.affiliations.current <chr>,  #> # flags.deposits.funders.current <chr>,  #> # flags.deposits.references.backfile <chr>,  #> # flags.deposits.abstracts.backfile <chr>,  #> # flags.deposits.licenses.backfile <chr>,  #> # flags.deposits.award.numbers.backfile <chr>,  #> # flags.deposits.open.references.backfile <chr>,  #> # flags.deposits.open.references.current <chr>,  #> # flags.deposits.references.current <chr>,  #> # flags.deposits.resource.links.backfile <chr>,  #> # flags.deposits.orcids.backfile <chr>,  #> # flags.deposits.funders.backfile <chr>,  #> # flags.deposits.update.policies.current <chr>,  #> # flags.deposits.similarity.checking.current <chr>,  #> # flags.deposits.licenses.current <chr>, names <chr>, tokens <chr>  #>  #> $facets  #> NULL 
```

将交叉引用成员名称添加到日志数据中。

```
alldat  <-  left_join(res,  select(members$data,  id,  primary_name),  by  =  c("route"  =  "id"))  alldat  #> # A tibble: 530 x 3  #> route count primary_name  #> <dbl> <int> <chr>  #> 1 10 345045 American Medical Association (AMA)  #> 2 530 7165 FapUNIFESP (SciELO)  #> 3 286 3062 Oxford University Press (OUP)  #> 4 276 2975 Ovid Technologies (Wolters Kluwer Health)  #> 5 239 2493 BMJ  #> 6 8611 1085 AME Publishing Company  #> 7 56 853 Cambridge University Press (CUP)  #> 8 235 722 American Society for Microbiology  #> 9 382 706 Joule Inc.  #> 10 175 590 The Royal Society  #> # … with 520 more rows 
```

美国医学协会收到了很多请求。远远排在第二位的是 FapUNIFESP (SciELO)，然后是牛津大学出版社、Ovid Technologies(Wolters Kluwer Health)、BMJ 和 AME 出版公司，它们的请求都超过了 1000 次。

这些都是发布者进入 ftdoi API 的明确线索，沿着 data.frame 一直到不经常被请求的发布者。

## 更多的工作要做

我已经有了一个很好的发布者列表，我知道用户想要 URL，所以我可以开始实现规则等。对于那些出版商来说。我可以不时地重复这个过程，以增加更多的高需求出版商。
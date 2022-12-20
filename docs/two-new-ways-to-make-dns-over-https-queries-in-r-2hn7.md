# 在 R 语言中通过 HTTPS 查询实现 DNS 的两种新方法

> 原文：<https://dev.to/hrbrmstr/two-new-ways-to-make-dns-over-https-queries-in-r-2hn7>

相当长一段时间以前,`{gdns}`软件包以其[的方式启动了](https://cran.rstudio.org/web/packages/gdns/),让 R 用户能够通过 HTTPS (DoH)使用谷歌(当时)对 [DNS 的新生支持。稍后，Cloudflare 还提供了一个全局 DoH 端点，并由此产生了(not-on-CRAN) `{dnsflare}`](https://en.wikipedia.org/wiki/DNS_over_HTTPS) [包](https://rud.is/b/2018/04/01/more-options-for-querying-dns-from-r-with-1-1-1-1/)。

实际上有两种方式来进行这些 [DoH 查询](https://tools.ietf.org/id/draft-ietf-doh-dns-over-https-05.html):一种是通过 HTTPS `GET` REST API，另一种是通过使用 [DNS wireformat](https://www.icann.org/en/system/files/files/presentation-dns-wireformat-protocols-13jul18-en.pdf) 查询和回复的 HTTPS `POST`查询。DoH 的`POST`方面相当标准化/统一，而`GET` /REST API 方面则有点像西部荒野。我想要一种既支持 wireformat 又支持 REST 习惯用法的方法，但也不需要编写大量的包来支持最终过多的不同 DoH `GET` /REST API 服务。

我“解决”了这个问题，首先增加了我的(非 CRAN) `{clandnstine}` [包](https://git.sr.ht/~hrbrmstr/clandnstine)来支持`POST` wireformat DoH 查询(因为底层的 [`{getdns} library`](https://getdnsapi.net) 支持解码 wireformat 响应])，并创建了一个非常小的 [`{playdoh}`包](https://git.sr.ht/~hrbrmstr/playdoh)，它为(希望)任何 DoH `GET` /REST 端点提供了通用支持。

### DoH vs DoT

我制作`{clandnstine}`包主要是为了支持通过 TLS (DoT)查询制作 [DNS，但是将 DoH 和 DoT 功能结合到这个包中是有意义的。问题是你们大多数用户使用的遗留平台(即 Windows)使得使用该软件包存在问题。因此，通过将 DoH `GET`功能分离到一个独立的包中，我不必编写 DNS wireformat pure R 响应处理程序。](https://en.wikipedia.org/wiki/DNS_over_TLS)

DoH 和 DoT 之间存在性能和其他差异。我怀疑大多数 DNS 提供商和大多数开源 DNS 服务器最终都将支持 DoH 和 DoT，所以你使用哪一个将取决于你的客户和用例。

### 两个(或更多)查询的故事

我们将通过 DoH 和 DoT 向一些服务器发出一些查询，以确保我们得到相同的结果。

```
library(clandnstine) # both of these are on sourcehut (~hrbrmstr/pkgname), 
library(playdoh) # or gitlab/gitugh (hrbrmstr/pkgname)

# DoT
x <- gdns_context()
gdns_query(x, "example.com", rr_type = "a")$just_address_answers$address_data
## [1] "93.184.216.34"

# DoH POST (wireformat)
doh_post("example.com", "a", server_path = doh_servers$quad9$url)$answer$rdata$ipv4_address
## [1] "93.184.216.34"

# DoH GET (rest)
doh_get("example.com", "a", service_path = doh_servers$securedns_eu$url)$data[1]
## [1] "93.184.216.34" 
```

Enter fullscreen mode Exit fullscreen mode

为了支持跨越各种`GET` /REST 端点的需求的*多样性*,`playdoh::doh_get()`函数有一个`extra_params`参数，允许您指定任何所需的额外 REST 查询参数。这两个包都有一个公开的全局变量`doh_servers`，它既有 URL 又有任何必需的额外参数。

### 鳍

像往常一样，踢轮胎，文件问题和 PRs 在你喜欢的地方，如果你最终使用任何一个包，请在评论中注明。
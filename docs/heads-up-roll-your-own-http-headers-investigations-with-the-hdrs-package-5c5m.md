# 抬头！使用“hdrs”包滚动您自己的 HTTP 头调查

> 原文：<https://dev.to/hrbrmstr/heads-up-roll-your-own-http-headers-investigations-with-the-hdrs-package-5c5m>

我在[的上一篇文章](https://dev.to/hrbrmstr/cran-mirror-security-53po)中讲了很多关于 HTTP 头的内容*。*

如果你想更深入地挖掘，我[做了一个小的包，可以让你从一个给定的 URL 获取 HTTP 头并查看它们。自述文件中有大部分内容的示例，但我们也将在这里浏览一部分。](https://git.rud.is/hrbrmstr/hdrs)

对于那些只想玩的人，你可以做:

```
install.packages("hdrs", repos = "https://cinc.rud.is/")

hdrs::explore_app() 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/d44d8f1b1e79a3eb93f6f5dc2dc42e4f.png)](https://i0.wp.com/rud.is/b/wp-content/uploads/2019/03/shiny-headers.png?ssl=1)

并使用小巧闪亮的应用程序来探索网站的安全标题或查看他们返回的所有标题。(*哦，是的……如果你读了之前的帖子，然后看了上面的截图，你会注意到 IP 拦截对于决心已定的~~攻击者~~个人来说是多么的毫无用处。*)

注意:在我的 CINC 仓库里有 macOS 和 Windows 的二进制文件，所以如果你使用上面的方法，你会得到它们。在那个调用中使用`type='source'`或者使用各种`remotes`包函数来安装源包(读完之后，你真的不应该相信任何包，永远不要):

*   [来源帽子](https://git.sr.ht/~hrbrmstr/hdrs)
*   [GitLab](https://gitlab.com/hrbrmstr/hdrs)
*   如果你必须 [GitHub](https://github.com/hrbrmstr/hdrs)

### 向前移动

让我们使用命令行来查看我新发现的最喜欢的站点，以便在安全相关的示例中使用:

```
library(hdrs)

assess_security_headers("https://cran.r-project.org/") %>% 
  dplyr::select(-url)
## # A tibble: 13 x 4
## header value status_code message            
## * <chr> <chr> <chr> <chr>              
## 1 access-control-allow-origin NA WARN Header not set     
## 2 content-security-policy NA WARN Header not set     
## 3 expect-ct NA WARN Header not set     
## 4 feature-policy NA WARN Header not set     
## 5 public-key-pins NA WARN Header not set     
## 6 referrer-policy NA WARN Header not set     
## 7 server Apache/2.4.10 (Debian) NOTE Server header found
## 8 strict-transport-security NA WARN Header not set     
## 9 x-content-type-options NA WARN Header not set     
## 10 x-frame-options NA WARN Header not set     
## 11 x-permitted-cross-domain-policies NA WARN Header not set     
## 12 x-powered-by NA WARN Header not set     
## 13 x-xss-protection NA WARN Header not set 
```

Enter fullscreen mode Exit fullscreen mode

哎哟。这并不是一个很好的结果(因此，也许下游镜像维护得有多差并不重要，或者也许运行一个有五年历史的[web 服务器](http://mail-archives.apache.org/mod_mbox/httpd-announce/201407.mbox/%3C650BABAF-9B03-4EEB-94EC-D6DD833C248F@apache.org%3E)很有趣 [vulns](https://httpd.apache.org/security/vulnerabilities_24.html) 也很好*)。*

无论如何…

`assess_security_headers()`函数查看 13 个现代的“面向安全”的 HTTP 头，执行一个非常简单的功效评估并返回结果。

*   `access-control-allow-origin`
*   `content-security-policy`
*   `expect-ct`
*   `feature-policy`
*   `server`
*   `public-key-pins`
*   `referrer-policy`
*   `strict-transport-security`
*   `x-content-type-options`
*   `x-frame-options`
*   `x-permitted-cross-domain-policies`
*   `x-powered-by`
*   `x-xss-protection`

由于您可能没有记住每一个 HTTP 头的名称、潜在值、建议值和总体目的，您还可以将`include_ref = TRUE`传递给该函数，以获得更多的信息，如您在屏幕截图中看到的体面的文本描述(闪亮的应用程序省略了许多字段)。

数据元素中提供了完整的参考:

```
data("http_headers")

dplyr::glimpse(http_headers)
## Observations: 184
## Variables: 14
## $ header_field_name <chr> "A-IM", "Accept", "Accept-Additions", "Accept-Charset", "Accept-Datetime", "Accept-Encoding…
## $ type_1 <chr> "Permanent", "Permanent", "Permanent", "Permanent", "Permanent", "Permanent", "Permanent", …
## $ protocol <chr> "http", "http", "http", "http", "http", "http", "http", "http", "http", "http", "http", "ht…
## $ status <chr> "", "standard", "", "standard", "informational", "standard", "", "standard", "", "standard"…
## $ reference <chr> "https://tools.ietf.org/html/rfc3229#section-10.5.3", "https://tools.ietf.org/html/rfc7231#…
## $ type_2 <chr> "Request", "Request", "Request", "Request", "Request", "Request", "Request", "Request", "Re…
## $ enable <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, TRUE, TRUE, FALSE, TRUE, FALSE, FAL…
## $ required <lgl> NA, NA, NA, NA, NA, NA, NA, NA, TRUE, TRUE, NA, TRUE, NA, NA, NA, TRUE, NA, NA, NA, NA, NA,…
## $ https <lgl> NA, NA, NA, NA, NA, NA, NA, NA, TRUE, TRUE, NA, TRUE, NA, NA, NA, TRUE, NA, NA, NA, NA, NA,…
## $ security_description <chr> "", "", "", "", "", "", "", "", "Sometimes an HTTP intermediary might try to detect viruses…
## $ security_reference <chr> "", "", "", "", "", "", "", "", "https://tools.ietf.org/html/rfc5789#section-5", "https://t…
## $ recommendations <chr> "", "", "", "", "", "", "", "", "Antivirus software scans for viruses or worms.", "Servers …
## $ cwe <chr> "", "", "", "", "", "", "", "", "CWE-509: Replicating Malicious Code (Virus or Worm)", "CWE…
## $ cwe_url <chr> "\r", "\r", "\r", "\r", "\r", "\r", "\r", "\r", "https://cwe.mitre.org/data/definitions/509… 
```

Enter fullscreen mode Exit fullscreen mode

最终会有一个可爱的小插图，有格式良好的部分，包括上述信息，这样你就可以在闲暇时参考(这是很棒的睡前读物)。

`http_headers`对象有完整的文档记录，但这些字段的含义如下:

*   `header_field_name`:表头字段
*   `type_1` : `Permanent`(标准中)；`Provisional`(实验)；`Personal`(非官方)
*   `protocol`:现在应该始终是`http`，但可能不同(如`quic`)
*   `status` : blank ==未知；否则，该值很好地描述了状态
*   哪里可以找到更多信息
*   `type_2` : `Request`(应该只能在请求中找到)；`Response`(只应在回复中找到)；`Request/Response`发现于两者之一；`Reserved`(尚未使用)
*   你应该启用它吗
*   `required`:这个表头是必须的吗
*   `https` : HTTPS 专用标头？
*   `security_description`:表头信息
*   `security_reference`:页眉上的额外外部参考信息
*   `recommendations`:推荐设置
*   `cwe`:关联的公共弱点枚举(CWE)标识符
*   `cwe_url`:关联的 CWE 网址

### 连珠炮头

HTTP 服务器可以发出大量的报头，我们可以用`hdrs::explain_headers()`将它们全部捕获。该函数获取标题，合并来自`http_headers`的完整元数据，并返回一个大的 ol 数据帧。我们将只取出最后一个例子的安全参考 URL:

```
explain_headers("https://community.rstudio.com/") %>% 
  dplyr::select(header, value, security_reference)
## # A tibble: 18 x 3
## header value security_reference                   
## <chr> <chr> <chr>                                
## 1 cache-control no-cache, no-store https://tools.ietf.org/html/rfc7234#…
## 2 connection keep-alive ""                                   
## 3 content-encoding gzip https://en.wikipedia.org/wiki/BREACH…
## 4 content-security-po… base-uri 'none'; object-src 'none'; script-src 'unsafe-eval'… https://www.owasp.org/index.php/List…
## 5 content-type text/html; charset=utf-8 https://tools.ietf.org/html/rfc7231#…
## 6 date Tue, 05 Mar 2019 20:40:31 GMT ""                                   
## 7 referrer-policy strict-origin-when-cross-origin NA                                   
## 8 server nginx https://tools.ietf.org/html/rfc7231#…
## 9 strict-transport-se… max-age=31536000 https://tools.ietf.org/html/rfc6797  
## 10 vary Accept-Encoding ""                                   
## 11 x-content-type-opti… nosniff https://www.owasp.org/index.php/List…
## 12 x-discourse-route list/latest NA                                   
## 13 x-download-options noopen NA                                   
## 14 x-frame-options SAMEORIGIN https://tools.ietf.org/html/rfc7034  
## 15 x-permitted-cross-d… none NA                                   
## 16 x-request-id 12322c6e-b47e-4960-b384-32138097886c NA                                   
## 17 x-runtime 0.106664 NA                                   
## 18 x-xss-protection 1; mode=block https://www.owasp.org/index.php/List… 
```

Enter fullscreen mode Exit fullscreen mode

### 鳍

找点乐子，戳几个头球。或许甚至可以用它来调查你工作/学习领域的主要网站，看看它们的评价如何。像往常一样，在你最喜欢的社交网站上发布公关和问题。
# 在 R 中争论内容安全策略

> 原文：<https://dev.to/hrbrmstr/wrangling-content-security-policies-in-r-2j1>

过去的[两个](https://dev.to/hrbrmstr/heads-up-roll-your-own-http-headers-investigations-with-the-hdrs-package-5c5m) [帖子](https://rud.is/b/2019/03/04/ip-user-agent-and-referrer-tracking-disabled-on-cinc-rud-is-and-git-rud-is/)已经使用 R 来查看 web-y thing-ys 的安全性(假设这些术语从现在开始在每个帖子中都出现在吓人的引号中)。我们将在这篇文章中继续这个主题，我们将重点放在一个[可惜没有使用的] HTTP 服务器头:[内容安全策略](https://www.w3.org/TR/CSP3/)(在这篇文章的剩余部分称为 CSP)。

为了简化事情，CSP 头指示浏览器你已经授权什么是你的站点的一部分。您为不同类型的内容提供指令，告诉浏览器哪些网站可以将内容加载到当前页面，以防止类似于[跨站点脚本攻击](https://www.owasp.org/index.php/Cross-site_Scripting_(XSS))、[恶意 iframes](https://kb.sucuri.net/malware/malicious-iframes) 、[点击劫持](https://www.owasp.org/index.php/Clickjacking)、[密码劫持](https://www.malwarebytes.com/cryptojacking/)等等。如果你认为这不会发生，[再想想](https://www.riskiq.com/blog/labs/magecart-adverline/)。

web 上有大量关于如何配置 CSP 的资源(有些是前面链接的),不需要另外的指南(这篇文章是关于在 R 中使用 CSP 作为数据源的)。然而，如果你目前没有在自己的任何网站上使用 CSP，并且想要一个相当简单的方法来制定策略，那么就拿一份最新的 Firefox，安装 Mozilla 自己的 [CSP Toolkit 扩展](https://addons.mozilla.org/en-US/firefox/addon/laboratory-by-mozilla/)，并在该插件激活的情况下浏览你的网站。它会生成尽可能少的策略，因为它会查看您的站点发出的所有资源请求。另一种方法是使用`Content-Security-Policy-Report-Only`报头和`Content-Security-Policy`报头，因为前者只会报告潜在的资源加载问题，而不会阻止资源加载。

### 现在来点 R 码怎么样，Mmmkay？

即使您没有自己的 CSP，也很容易找到它们，因为它们会出现在 HTTP 服务器响应头中。为了更容易地查看一个站点是否有 CSP 以及加载 CSP，我已经把[一个小的包](https://cinc.rud.is/web/packages/cspy/)(安装说明&源位置在那个链接上)放在一起，它可以为我们做这些。(如果您访问该页面，您将会看到一个为 CRAN 状态徽章加载失败图像。在浏览器中打开开发者工具或 javascript 控制台，您会看到 CSP 规则在努力工作:

[![](img/56973b36919590d65fc97458de040e05.png)](https://i0.wp.com/rud.is/b/wp-content/uploads/2019/03/cran-badge-csp.png?ssl=1)

`cspy`包基于 Shape Security 的[漂亮的 Java 库](https://github.com/shapesecurity/salvation/)，它实现了一个真正的 CSP 规则解析器(而不是我们在这篇文章中也要做的 hacky-string-slicing ),并有一组用 R 编写的安全检查器规则，但基于 Google 定义的规则[。我违反了*“一个 pkg 支持罐子；另一个 pkg"* "规则"中的实际功能，因为 Shape Security 每年只发布 1.5 版，而且它是一个没有依赖关系的超级小 JAR。](https://csp-evaluator.withgoogle.com/)

让我们找到一个以 R 为中心的站点，它有一个我们可以使用的 CSP(假设`tidyverse`和`stringi`被加载到 R 会话中)。我们将从一个超级漂亮的 R org 开始:RStudio！:

```
cspy::has_csp("https://www.rstudio.com/")
## [1] FALSE 
```

Enter fullscreen mode Exit fullscreen mode

哦，嗯……那么，另一个超级棒的组织怎么样:芒果解决方案！:

```
cspy::has_csp("http://mango-solutions.com/")
## [1] FALSE 
```

Enter fullscreen mode Exit fullscreen mode

*呵*。嗯…啊& hellip。Hrm。数据营呢！他们有很多很棒的东西:

```
cspy::has_csp("https://www.datacamp.com/")
## [1] TRUE 
```

Enter fullscreen mode Exit fullscreen mode

W00t！

公平地说，RStudio 和 Mango 的主要品牌网站都使用 WordPress，WordPress 使得编写一个像样的 CSP 变得非常困难。然而，(在我看来)这并不是没有的借口(因为我运行 WordPress 并且有一个)。但是，数据营有！那么，让我们取来看看:

```
(dc_csp <- cspy::fetch_csp("https://www.datacamp.com/"))
## default-src 'self' *.datacamp.com *.datacamp-staging.com
## base-uri 'self'
## connect-src 'self' *.datacamp.com *.datacamp-staging.com https://com-datacamp.mini.snplow.net https://www2.profitwell.com https://www.facebook.com https://www.optimalworkshop.com https://in.hotjar.com https://stats.g.doubleclick.net https://*.google-analytics.com https://frstre.com https://onesignal.com https://*.algolia.net https://*.algolianet.com https://wootric-eligibility.herokuapp.com https://d8myem934l1zi.cloudfront.net https://production.wootric.com
## font-src 'self' *.datacamp.com *.datacamp-staging.com https://fonts.gstatic.com data:
## form-action 'self' *.datacamp.com *.datacamp-staging.com https://*.paypal.com https://www.facebook.com
## frame-ancestors 'self' *.datacamp.com *.datacamp-staging.com
## frame-src 'self' *.datacamp.com *.datacamp-staging.com *.zuora.com https://www.google.com https://vars.hotjar.com https://beacon.tapfiliate.com https://b.frstre.com https://onesignal.com https://tpc.googlesyndication.com https://*.facebook.com https://*.youtube.com
## img-src 'self' *.datacamp.com *.datacamp-staging.com https://s3.amazonaws.com https://checkout.paypal.com https://cdnjs.cloudflare.com https://www.gstatic.com https://maps.googleapis.com https://maps.gstatic.com https://www.facebook.com https://q.quora.com https://bat.bing.com https://*.ads.linkedin.com https://www.google.com https://*.g.doubleclick.net https://*.google-analytics.com https://www.googletagmanager.com blob data:
## script-src 'self' 'unsafe-inline' 'unsafe-eval' *.datacamp.com *.datacamp-staging.com https://*.googletagmanager.com https://*.google-analytics.com https://browser.sentry-cdn.com https://js.braintreegateway.com https://*.zuora.com https://*.paypal.com https://js-agent.newrelic.com https://bam.nr-data.net https://maps.googleapis.com https://assets.calendly.com http://com-datacamp.mini.snplow.net https://cdn.jsdelivr.net https://*.algolia.net https://www.gstatic.com https://www.google.com https://cdnjs.cloudflare.com https://static.tapfiliate.com https://cdn.onesignal.com https://onesignal.com https://static.hotjar.com https://script.hotjar.com https://*.linkedin.com https://sjs.bizographics.com/insight.min.js https://bat.bing.com/bat.js https://a.quora.com/qevents.js https://connect.facebook.net https://www.googleadservices.com https://www.googletagmanager.com https://*.googlesyndication.com https://dna8twue3dlxq.cloudfront.net/js/profitwell.js https://disutgh7q0ncc.cloudfront.net/beacon.js data:
## style-src 'self' 'unsafe-inline' *.datacamp.com *.datacamp-staging.com https://cdnjs.cloudflare.com https://fonts.googleapis.com https://assets.calendly.com
## worker-src 'self'
## report-uri https://infra-reporter.datacamp.com/csp-report 
```

Enter fullscreen mode Exit fullscreen mode

哇哦。字面:*哇*。我们将让他们最近的违规和缺乏 2FA 登录滑一点 b/c 这是一个强大的 CSP，所以很明显他们确实关心你(不管有什么 CSP 助手工具，这些 CSP 是一种痛苦的设置，所以这个如此彻底的事实表明他们“明白”)。

不过，r 国人可能会看着这一团乱麻，畏缩不前。我们将很快整理它，但我们要确保它是一个有效的 CSP:

```
glimpse(validate_csp(dc_csp))
## Observations: 1
## Variables: 8
## $ message <chr> "A draft of the next version of CSP deprecates report-uri in favour …
## $ type <chr> "INFO"
## $ start_line <int> 1
## $ start_column <int> 2680
## $ start_offset <int> 2679
## $ end_line <int> 1
## $ end_column <int> 2690
## $ end_offset <int> 2689 
```

Enter fullscreen mode Exit fullscreen mode

验证器只注意到正在使用一个即将被否决的指令(但这只是信息性的，在 CSP v3 退出草案并且浏览器支持它之前完全没问题)，并且验证输出具有发生违规的位置值(这显示了使用真正的解析器而不是字符串拆分器的好处)。

这些`csp`对象的定制打印方法将每个指令放在单独的行上，并保持资源值不变。如上所述，它又丑又乱，所以让我们来处理一下:

```
(csp_df <- as.data.frame(dc_csp))
## # A tibble: 111 x 3
## directive value origin                   
## <chr> <chr> <chr>                    
## 1 default-src 'self' https://www.datacamp.com/
## 2 default-src *.datacamp.com https://www.datacamp.com/
## 3 default-src *.datacamp-staging.com https://www.datacamp.com/
## 4 base-uri 'self' https://www.datacamp.com/
## 5 connect-src 'self' https://www.datacamp.com/
## 6 connect-src *.datacamp.com https://www.datacamp.com/
## 7 connect-src *.datacamp-staging.com https://www.datacamp.com/
## 8 connect-src https://com-datacamp.mini.snplow.net https://www.datacamp.com/
## 9 connect-src https://www2.profitwell.com https://www.datacamp.com/
## 10 connect-src https://www.facebook.com https://www.datacamp.com/
## # … with 101 more rows 
```

Enter fullscreen mode Exit fullscreen mode

现在，每个指令/值对都有一行。如果您想将这些绑定在一起，那么原点(策略的来源)也会随之出现。

让我们看看他们需要捕获多少第三方网站并添加到该策略中:

```
filter(csp_df, stri_detect_fixed(value, ".")) %>% 
  pull(value) %>% 
  stri_replace_all_fixed("*.", "") %>% 
  urltools::url_parse() %>% 
  distinct(domain) %>% 
  filter(!stri_detect_regex(domain, "(datacamp\\.com|datacamp-staging\\.com)$")) %>% 
  arrange(domain) %>% 
  pull(domain)
## [1] "a.quora.com" "ads.linkedin.com" "algolia.net"                      
## [4] "algolianet.com" "assets.calendly.com" "b.frstre.com"                     
## [7] "bam.nr-data.net" "bat.bing.com" "beacon.tapfiliate.com"            
## [10] "browser.sentry-cdn.com" "cdn.jsdelivr.net" "cdn.onesignal.com"                
## [13] "cdnjs.cloudflare.com" "checkout.paypal.com" "com-datacamp.mini.snplow.net"     
## [16] "connect.facebook.net" "d8myem934l1zi.cloudfront.net" "disutgh7q0ncc.cloudfront.net"     
## [19] "dna8twue3dlxq.cloudfront.net" "facebook.com" "fonts.googleapis.com"             
## [22] "fonts.gstatic.com" "frstre.com" "g.doubleclick.net"                
## [25] "google-analytics.com" "googlesyndication.com" "googletagmanager.com"             
## [28] "in.hotjar.com" "js-agent.newrelic.com" "js.braintreegateway.com"          
## [31] "linkedin.com" "maps.googleapis.com" "maps.gstatic.com"                 
## [34] "onesignal.com" "paypal.com" "production.wootric.com"           
## [37] "q.quora.com" "s3.amazonaws.com" "script.hotjar.com"                
## [40] "sjs.bizographics.com" "static.hotjar.com" "static.tapfiliate.com"            
## [43] "stats.g.doubleclick.net" "tpc.googlesyndication.com" "vars.hotjar.com"                  
## [46] "wootric-eligibility.herokuapp.com" "www.facebook.com" "www.google.com"                   
## [49] "www.googleadservices.com" "www.googletagmanager.com" "www.gstatic.com"                  
## [52] "www.optimalworkshop.com" "www2.profitwell.com" "youtube.com"                      
## [55] "zuora.com" 
```

Enter fullscreen mode Exit fullscreen mode

这个列表是这些 CSP 可能难以配置的一个重要原因，也是为什么有些人认为它们可能没有什么好处。为什么收益不大？让我们看看他们允许浏览器从哪些网站加载和执行 javascript 内容:

```
filter(csp_df, directive == "script-src") %>% 
  select(value) %>% 
  filter(!stri_detect_regex(value, "(datacamp\\.com$|datacamp-staging\\.com$|'|data:)")) %>% 
  pull(value)
## [1] "https://*.googletagmanager.com" "https://*.google-analytics.com"                       
## [3] "https://browser.sentry-cdn.com" "https://js.braintreegateway.com"                      
## [5] "https://*.zuora.com" "https://*.paypal.com"                                 
## [7] "https://js-agent.newrelic.com" "https://bam.nr-data.net"                              
## [9] "https://maps.googleapis.com" "https://assets.calendly.com"                          
## [11] "http://com-datacamp.mini.snplow.net" "https://cdn.jsdelivr.net"                             
## [13] "https://*.algolia.net" "https://www.gstatic.com"                              
## [15] "https://www.google.com" "https://cdnjs.cloudflare.com"                         
## [17] "https://static.tapfiliate.com" "https://cdn.onesignal.com"                            
## [19] "https://onesignal.com" "https://static.hotjar.com"                            
## [21] "https://script.hotjar.com" "https://*.linkedin.com"                               
## [23] "https://sjs.bizographics.com/insight.min.js" "https://bat.bing.com/bat.js"                          
## [25] "https://a.quora.com/qevents.js" "https://connect.facebook.net"                         
## [27] "https://www.googleadservices.com" "https://www.googletagmanager.com"                     
## [29] "https://*.googlesyndication.com" "https://dna8twue3dlxq.cloudfront.net/js/profitwell.js"
## [31] "https://disutgh7q0ncc.cloudfront.net/beacon.js" 
```

Enter fullscreen mode Exit fullscreen mode

其中一些被指定到`.js`文件，但其他的则在整个 CDN 网络上工作。当然，没有什么比亚马逊的 S3 顶级域名(不管有没有通配符)更糟糕的了，但是这个列表中有很多值得信任的地方。CSP 的好处是，如果 Data Camp 了解到这些网站中的任何一个有问题，它可以快速地从标题中删除它们，因为它们已经完成了艰难的枚举工作。此外，如果其中一个站点遭到破坏，并加载一个试图执行不在该列表中的域的代码的资源，将会生成一个策略错误，Data Camp 出色的 SOC(他们对破坏的响应非常好)可以采取积极的措施，通知他们的下游提供商有问题。

我们还可以做一些安全检查:

```
check_script_unsafe_inline(csp_df)
## Category: script-unsafe-inline
## Severity: HIGH
## Message: 'unsafe-inline' allows the execution of unsafe in-page scripts and event handlers.
## 
## directive value origin
## 67 script-src 'unsafe-inline' https://www.datacamp.com/

check_script_unsafe_eval(csp_df)
## Category: script-unsafe-eval
## Severity: HIGH
## Message: 'unsafe-eval' allows the execution of code injected into DOM APIs such as eval().
## 
## directive value origin
## 68 script-src 'unsafe-eval' https://www.datacamp.com/

check_plain_url_schemes(csp_df)
## Category: unsafe-execution
## Severity: HIGH
## Message: URI(s) found that allow the exeution of unsafe scripts.
## 
## directive value origin
## 102 script-src data: https://www.datacamp.com/

check_wildcards(csp_df)

check_missing_directives(csp_df)
## Category: missing-directive
## Severity: HIGH
## Message: Missing object-src allows the injection of plugins which can execute JavaScript. Can you set it to 'none'?
## 
## directive value
## 1 object-src <NA>

check_ip_source(csp_df)

check_deprecated(csp_df)
## Category: deprecated-directive
## Severity: INFO
## Message: Found deprecated directive(s). See https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy for more information.
## 
## directive value
## 111 report-uri https://infra-reporter.datacamp.com/csp-report
## origin
## 111 https://www.datacamp.com/

check_nonce_length(csp_df)

check_src_http(csp_df)
## Category: http-src
## Severity: MEDIUM
## Message: Use HTTPS vs HTTP.
## 
## directive value
## 81 script-src http://com-datacamp.mini.snplow.net
## origin
## 81 https://www.datacamp.com/ 
```

Enter fullscreen mode Exit fullscreen mode

伴随着漂亮的打印，它们返回潜在有问题的规则的数据帧。

对于某些页面，Data Camp 会做您在编织 R markdown 文档时可能会做的事情:将其保存为自包含页面。因此，对一些指令使用`'unsafe-inline'`、`'unsafe-eval'`和`data:`指令值是绝对必要的。这是没办法的。我们已经知道了`report-uri`的贬值。虽然`object-src`不在 CSP-proper 中，但是有一个`default-src`，它将生效。但是，检查器知道它没有设置为`'none'`，这意味着插件仍然可以从 Data Camp 自己的域中加载(这可能完全没问题，取决于它们使用的嵌入类型)。

我将不得不支持检查器的一个发现是最后的那个(`check_src_http()`的输出)。他们似乎允许网站的众多分析提供商之一(我认为是[这个组织](https://snowplowanalytics.com/))使用`http://`对`https://`。如果他们真的通过普通的`http://`从该网站加载 javascript 内容，那么攻击者就有可能劫持内容并插入恶意的 javascript。使用基于 DNS(例如 [pi-hole](https://pi-hole.net/) )或 uBlock Origin 来阻止任何跟踪器或分析脚本加载的另一个好理由。

还有一点要注意的是，Data Camp 使用亚马逊 AWS 作为他们的网站主机，你必须要经历重重困难才能在那里添加这个标题，所以他们在考虑你的安全方面做得很好。

### 大规模的角力

`cspy`包很适合临时/轻量级检查，但是它使用了 Java shim，这意味着在 R 和 JVM 之间封送数据💤大规模。根据您要做的事情，一些基本的字符串争论可能就足够了。

在$DAYJOB，我们执行许多 [HTTP 扫描](https://opendata.rapid7.com/sonar.https/)。我们内部的一次扫描(我不认为这是公开数据的一部分，但我会在周一检查)是对 Alexa 前 100 万名主机的定期抓取。在最近的扫描中，我们发现了大约 65K 个带有 CSP 标题的站点。您可以在这里获取包含虚拟主机名称和找到的 CSP 策略字符串[的 CSV。](https://rud.is/dl/top1m-csp.csv.gz)

一旦你下载了，我们就可以使用字符串操作来得到一个整洁的数据帧:

```
read_csv("top1m-csp.csv.gz", col_types = "cc") %>% # use the location where you stored it
  mutate(csp = stri_trans_tolower(csp)) -> top1m_csp

stri_split_regex(top1m_csp$csp, ";[[:space:]]*", simplify = FALSE) %>% # split directives for each policy
  map2_df(top1m_csp$vhost, ~{ # we want to keep the origin
    stri_match_first_regex(.x, "([[:alpha:]\\-]+)[[:space:]]+(.*)$|([[:alpha:]\\-]+)") %>% # get the directive
      as.data.frame(stringsAsFactors = FALSE) %>%
      mutate(origin = sprintf("https://%s/", .y)) %>% 
      mutate(raw = .x) # save the raw CSP
  }) %>%
  mutate(V2 = ifelse(is.na(V2), V4, V2)) %>% # figure out directive
  mutate(V3 = ifelse(is.na(V3), "", V3)) %>% # and value (keeping in mind no-value directives)
  select(directive = V2, value = V3, origin, raw) %>%
  as_tibble() %>%
  separate_rows(value, sep="[[:space:]]+") -> top1m_csp # tidy it up with each row being directive|value|origin like the above 
```

Enter fullscreen mode Exit fullscreen mode

这对我来说只花了 2 分钟，这 2 分钟太长了，无法分发给那些可能正在家里尝试的勇敢的读者，所以结果的 RDS 也是可用的。

因此，在排名前 100 万的网站中，只有大约 65000 个有 CSP 标题(大约 6.5%)，但这并不意味着它们是好的。让我们看看他们用过的无效指令:

```
top1m_csp %>%
  filter(!is.na(directive)) %>%
  count(directive, sort = TRUE) %>%
  mutate(is_valid = directive %in% valid_csp_directives) %>%
  filter(!is_valid) %>%
  select(-is_valid) %>% 
  print(n=65)
## # A tibble: 65 x 2
## directive n
## <chr> <int>
## 1 referrer 69
## 2 reflected-xss 58
## 3 t 50
## 4 self 34
## 5 tscript-src 22
## 6 tframe-src 17
## 7 timg-src 16
## 8 content-security-policy 14
## 9 default 10
## 10 policy-definition 9
## 11 allow 8
## 12 defalt-src 8
## 13 tstyle-src 8
## 14 https 7
## 15 nosniff 7
## 16 null 7
## 17 tfont-src 6
## 18 unsafe-inline 6
## 19 http 5
## 20 allow-same-origin 4
## 21 content 4
## 22 defaukt-src 4
## 23 default-scr 4
## 24 defaut-src 4
## 25 data 3
## 26 frame-ancestor 3
## 27 policy 3
## 28 self-ancestors 3
## 29 defualt-src 2
## 30 jivosite 2
## 31 mg-src 2
## 32 none 2
## 33 object 2
## 34 options 2
## 35 policy-uri 2
## 36 referrer-policy 2
## 37 tconnect-src 2
## 38 tframe-ancestors 2
## 39 xhr-src 2
## 40 - 1
## 41 -report-only 1
## 42 ahliunited 1
## 43 anyimage 1
## 44 blok-all-mixed-content 1
## 45 content-security-policy-report-only 1
## 46 default-sec 1
## 47 disown-opener 1
## 48 dstyle-src 1
## 49 frame-ancenstors 1
## 50 frame-ancestorss 1
## 51 frame-ancestros 1
## 52 mode 1
## 53 navigate-to 1
## 54 origin 1
## 55 plugin-type 1
## 56 same-origin 1
## 57 strict-dynamic 1
## 58 tform-action 1
## 59 tni 1
## 60 upgrade-insecure-request 1
## 61 v 1
## 62 value 1
## 63 worker-sc 1
## 64 x-frame-options 1
## 65 yandex 1 
```

Enter fullscreen mode Exit fullscreen mode

拼写错误比人们想象的更常见(这是 CSP 很难的另一个原因，因为人类往往不得不犯这些错误)。

我们还可以看到使用最多的指令:

```
top1m_csp %>%
  filter(!is.na(directive)) %>%
  count(directive, sort = TRUE) %>%
  mutate(is_valid = directive %in% valid_csp_directives) %>%
  filter(is_valid) %>%
  select(-is_valid) %>% 
  print(n=25)
## # A tibble: 25 x 2
## directive n
## <chr> <int>
## 1 script-src 57636
## 2 frame-ancestors 47685
## 3 upgrade-insecure-requests 47515
## 4 block-all-mixed-content 38038
## 5 report-uri 37483
## 6 default-src 36369
## 7 img-src 35093
## 8 style-src 23005
## 9 connect-src 22415
## 10 frame-src 15827
## 11 font-src 12878
## 12 media-src 6718
## 13 child-src 5043
## 14 object-src 4235
## 15 worker-src 3076
## 16 form-action 1522
## 17 base-uri 861
## 18 manifest-src 374
## 19 sandbox 79
## 20 script-src-elem 54
## 21 plugin-types 47
## 22 report-to 32
## 23 prefetch-src 22
## 24 require-sri-for 14
## 25 style-src-elem 6 
```

Enter fullscreen mode Exit fullscreen mode

好奇的读者应该加载`urltools`，看看那些`script-src`指令允许哪些域。请记住，指向第三方网站的每个值条目都意味着该网站可以干扰您的浏览器或监视您。

并且，看看人们在哪里做 CSP 错误报告:

```
filter(top1m_csp, directive %in% c("report-uri", "report-to")) %>%
  mutate(report_host = case_when(
    !grepl("^http[s]*://", value) ~ origin,
    TRUE ~ value
  )) %>%
  select(report_host) %>%
  mutate(report_host = sub('"$', "", report_host)) %>%
  pull(report_host) %>%
  urltools::url_parse() %>%
  as_tibble() %>%
  count(domain, sort=TRUE) %>% 
  print(n=20)
## # A tibble: 18,565 x 2
## domain n
## <chr> <int>
## 1 csp.medium.com 113
## 2 sentry.io 43
## 3 de.forumhome.com 40
## 4 report-uri.highwebmedia.com 33
## 5 csp.yandex.net 23
## 6 99designs.report-uri.io 20
## 7 endpoint1.collection.us2.sumologic.com 20
## 8 capture.condenastdigital.com 17
## 9 secure.booked.net 14
## 10 bimbobakeriesusa.com 10
## 11 oroweat.com 10
## 12 ponyfoo.com 8
## 13 monzo.report-uri.com 6
## 14 19jrymqg65.execute-api.us-east-1.amazonaws.com 5
## 15 app.getsentry.com 5
## 16 csp-report.airfrance.fr 5
## 17 csp.nic.cz 5
## 18 myklad.org 5
## 19 reports-api.sqreen.io 5
## 20 tracker.report-uri.com 5
## # … with 1.854e+04 more rows 
```

Enter fullscreen mode Exit fullscreen mode

榜单(18K！)远比我预期的更加多样化。

你可以做更多的事情(特别是因为争论已经为你做了:-)。

### 鳍

未来的帖子将展示如何在 R 中处理 CSP 错误报告，并且可能会有更多以安全为主题的帖子。

`cspy` [CINC 回购](https://cinc.rud.is/web/packages/cspy/)有 Windows 和 macOS 二进制版本，你可以在所有常见的地方获得源代码，所以踢轮胎，文件问题/PRs，并开始探索你经常访问的网站的内容安全政策(并博客你的结果！).

另一个有趣的练习是抓取 R Weekly 或 R-bloggers 博客，看看有多少博客有 CSP。
# 使用 ropendata R 包从 Rapid7 访问数 Pb 的免费互联网遥测数据

> 原文：<https://dev.to/hrbrmstr/using-the-ropendata-r-package-to-access-petabytes-of-free-internet-telemetry-data-from-rapid7-1n88>

我在 [$DAYJOB 的博客](https://blog.rapid7.com/2019/02/13/level-up-your-internet-intelligence-using-the-rapid7-open-data-api-and-r/)上发了一篇关于使用 [`ropendata`的帖子🔗](https://cran.r-project.org/web/packages/ropendata/)打包通过 Rapid7 [开放数据 API](https://opendata.rapid7.com/) 访问海量且不断增长的互联网遥测(扫描)数据。

它是超级 R-code-heavy，但是在 Ghost(我们在工作中使用的博客平台)中表现得非常好，并且涵盖了从哪里注册帐户、安装包(在 CRAN！)，用免费的地理定位数据库和`rgeolocate` plus 丰富扫描数据，甚至展示了如何拼凑一些相当复杂的 R + Rcpp 代码，以便您可以解码来自深奥的协议交换的粗糙的二进制 UDP 响应。

[看一下](https://blog.rapid7.com/2019/02/13/level-up-your-internet-intelligence-using-the-rapid7-open-data-api-and-r/)，拿起[同伴 Rmd](https://github.com/rapid7/data/tree/master/blogs/2019/2019-02-ropendata) ，如果有任何问题，可以在这里或这里发表评论，或者联系 research@rapid7.com。
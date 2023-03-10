# 我的 Go 工具包构建无框架应用

> 原文：<https://dev.to/biros/my-go-toolkit-to-build-a-frameworkless-app-5777>

几个月前，我开始了从 PHP 到公司新应用程序的旅程。

大约 3 个月后，我在 DEV 上发表了另一篇文章，谈论我对语言的反馈。

现在是时候分享我到目前为止一直在使用的库和工具了。注意，我不使用任何框架，也不使用任何 ORM。合适的库来完成这项工作。

* * *

## 库

### 1。毒蛇

🌠*7353*

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [ spf13 ](https://github.com/spf13) / [毒蛇](https://github.com/spf13/viper)

### 去配置尖牙

<article class="markdown-body entry-content container-lg" itemprop="text">

[![Viper](img/9b4d60b925b735bf2efb022c0aea03b7.png)](https://raw.githubusercontent.com/spf13/viper/master/.github/logo.png?raw=true)

[![Mentioned in Awesome Go](img/79896cc827f775a31397cc9e0028c307.png)](https://github.com/avelino/awesome-go#configuration)

[![GitHub Workflow Status](img/abf24624374de4a6e55b4ee2ac217a30.png)](https://github.com/spf13/viper/actions?query=workflow%3ACI)[![Join the chat at https://gitter.im/spf13/viper](img/54a280a815a3981e4f4180450cdfd483.png)](https://gitter.im/spf13/viper?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)[![Go Report Card](img/2cf88c497d847bf52a59307ec463091d.png)](https://goreportcard.com/report/github.com/spf13/viper)[![go.dev reference](img/82499488cadd6afb9af9b10d5a7ceb63.png)T11】](https://pkg.go.dev/mod/github.com/spf13/viper)

**用獠牙去配置！**

许多 Go 项目都是使用 Viper 构建的，包括:

*   [雨果](http://gohugo.io)
*   [EMC RexRay](http://rexray.readthedocs.org/en/stable/)
*   [Imgur 的砧骨](https://github.com/Imgur/incus)
*   [纳米盒](https://github.com/nanobox-io/nanobox) / [纳米包](https://github.com/nanopack)
*   [码头工人公证人](https://github.com/docker/Notary)
*   [BloomApi](https://www.bloomapi.com/)
*   [doctl](https://github.com/digitalocean/doctl)
*   [Clairctl](https://github.com/jgsqware/clairctl)
*   汞

## 安装

```
go get github.com/spf13/viper
```

## 毒蛇是什么？

Viper 是 Go 应用程序的完整配置解决方案，包括 12 因素应用程序。它被设计为在应用程序中工作，并且可以处理所有类型的配置需求和格式。它支持:

*   设置默认值
*   读取 JSON、TOML、YAML、HCL、envfile 和 Java 属性配置文件
*   实时观察和重读配置文件(可选)
*   从环境变量中读取
*   从远程配置系统(etcd 或 Consul)读取，并观察变化
*   从命令行标志中读取
*   从缓冲区读取
*   设置显式值

Viper 可以被看作是满足所有应用程序配置需求的注册表。

## 为什么是毒蛇？

当构建一个现代化的应用程序时，您不想担心配置文件格式；您希望专注于构建…

</article>

[View on GitHub](https://github.com/spf13/viper)

### 2。杜松子酒

🌠*23913*

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [金-高尼](https://github.com/gin-gonic) / [金](https://github.com/gin-gonic/gin)

### Gin 是一个用 Go (Golang)编写的 HTTP web 框架。它有一个类似 Martini 的 API，性能更好——快了 40 倍。如果你需要出色的表演，给自己弄点杜松子酒。

<article class="markdown-body entry-content container-lg" itemprop="text">

# Gin Web 框架

[![](img/fee9725cbd3bf4b369169c83057943a5.png)](https://raw.githubusercontent.com/gin-gonic/logo/master/color.png)

[![Build Status](img/428aa20da37f844e49ab89da62796415.png)](https://travis-ci.org/gin-gonic/gin)[![codecov](img/9d85e3e639508dda95a9611b93c30493.png)](https://codecov.io/gh/gin-gonic/gin)[![Go Report Card](img/c4b05f883c1c75f4dfd3a683b4e90034.png)](https://goreportcard.com/report/github.com/gin-gonic/gin)[![GoDoc](img/d26257f8728b1b8ddf2a2cbc1e9b8e4a.png)](https://pkg.go.dev/github.com/gin-gonic/gin?tab=doc)[![Join the chat at https://gitter.im/gin-gonic/gin](img/54a280a815a3981e4f4180450cdfd483.png)](https://gitter.im/gin-gonic/gin?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)[![Sourcegraph](img/bd253554f04feb07a0ac533a955845cf.png)](https://sourcegraph.com/github.com/gin-gonic/gin?badge)[![Open Source Helpers](img/446e78ced71251872a80ad8a315e2a0d.png)](https://www.codetriage.com/gin-gonic/gin)[![Release](img/c8e5546de40daa94a3513bb2c9495562.png)](https://github.com/gin-gonic/gin/releases)[T25】](https://www.tickgit.com/browse?repo=github.com/gin-gonic/gin)

Gin 是一个用 Go (Golang)编写的 web 框架。它有一个类似 martini 的 API，由于有了[http outer](https://github.com/julienschmidt/httprouter)，性能提高了 40 倍。如果你需要性能和良好的生产力，你会喜欢杜松子酒。

## 内容

*   [Gin Web 框架](https://raw.githubusercontent.com/gin-gonic/gin/master/#gin-web-framework)
    *   [内容](https://raw.githubusercontent.com/gin-gonic/gin/master/#contents)
    *   [安装](https://raw.githubusercontent.com/gin-gonic/gin/master/#installation)
    *   [快速启动](https://raw.githubusercontent.com/gin-gonic/gin/master/#quick-start)
    *   [基准](https://raw.githubusercontent.com/gin-gonic/gin/master/#benchmarks)
    *   [金酒 v1。稳定](https://raw.githubusercontent.com/gin-gonic/gin/master/#gin-v1-stable)
    *   [用 jsoniter 构建](https://raw.githubusercontent.com/gin-gonic/gin/master/#build-with-jsoniter)
    *   [API 示例](https://raw.githubusercontent.com/gin-gonic/gin/master/#api-examples)
        *   [使用获取、发布、上传、修补、删除和选项](https://raw.githubusercontent.com/gin-gonic/gin/master/#using-get-post-put-patch-delete-and-options)
        *   [路径中的参数](https://raw.githubusercontent.com/gin-gonic/gin/master/#parameters-in-path)
        *   [查询字符串参数](https://raw.githubusercontent.com/gin-gonic/gin/master/#querystring-parameters)
        *   [多部分/Urlencoded 格式](https://raw.githubusercontent.com/gin-gonic/gin/master/#multiparturlencoded-form)
        *   [再比如:查询+发布表单](https://raw.githubusercontent.com/gin-gonic/gin/master/#another-example-query--post-form)
        *   [映射为 querystring 或 postform 参数](https://raw.githubusercontent.com/gin-gonic/gin/master/#map-as-querystring-or-postform-parameters)
        *   [上传文件](https://raw.githubusercontent.com/gin-gonic/gin/master/#upload-files)
            *   [单个文件](https://raw.githubusercontent.com/gin-gonic/gin/master/#single-file)
            *   [多个文件](https://raw.githubusercontent.com/gin-gonic/gin/master/#multiple-files)
        *   [分组路线](https://raw.githubusercontent.com/gin-gonic/gin/master/#grouping-routes)
        *   [默认无中间件的空白轧花机](https://raw.githubusercontent.com/gin-gonic/gin/master/#blank-gin-without-middleware-by-default)
        *   [使用中间件](https://raw.githubusercontent.com/gin-gonic/gin/master/#using-middleware)
        *   [如何写日志文件](https://raw.githubusercontent.com/gin-gonic/gin/master/#how-to-write-log-file)
        *   [自定义日志格式](https://raw.githubusercontent.com/gin-gonic/gin/master/#custom-log-format)
        *   [控制日志输出着色](https://raw.githubusercontent.com/gin-gonic/gin/master/#controlling-log-output-coloring)
        *   [模型绑定和验证](https://raw.githubusercontent.com/gin-gonic/gin/master/#model-binding-and-validation)
        *   [自定义验证器](https://raw.githubusercontent.com/gin-gonic/gin/master/#custom-validators)
        *   [仅绑定查询字符串](https://raw.githubusercontent.com/gin-gonic/gin/master/#only-bind-query-string)
        *   [绑定查询字符串或发布数据](https://raw.githubusercontent.com/gin-gonic/gin/master/#bind-query-string-or-post-data)
        *   [绑定 Uri](https://raw.githubusercontent.com/gin-gonic/gin/master/#bind-uri)
        *   [绑定标题](https://raw.githubusercontent.com/gin-gonic/gin/master/#bind-header)
        *   [绑定 HTML 复选框](https://raw.githubusercontent.com/gin-gonic/gin/master/#bind-html-checkboxes)
        *   [多部分/Urlencoded 绑定](https://raw.githubusercontent.com/gin-gonic/gin/master/#multiparturlencoded-binding)
        *   [XML、JSON、YAML 和 ProtoBuf 渲染](https://raw.githubusercontent.com/gin-gonic/gin/master/#xml-json-yaml-and-protobuf-rendering)
            *   [安全子](https://raw.githubusercontent.com/gin-gonic/gin/master/#securejson)
            *   [JSONP](https://raw.githubusercontent.com/gin-gonic/gin/master/#jsonp)
            *   [腹水位置](https://raw.githubusercontent.com/gin-gonic/gin/master/#asciijson)
            *   [PureJSON](https://raw.githubusercontent.com/gin-gonic/gin/master/#purejson)
        *   [服静](https://raw.githubusercontent.com/gin-gonic/gin/master/#serving-static-files) …

</article>

[View on GitHub](https://github.com/gin-gonic/gin)

### 3。船用汽油（Marine Gas Oil 的缩写）

🌠*1385*

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[globalsign](https://github.com/globalsign)/[MgO](https://github.com/globalsign/mgo)

### 用于 Go 的 MongoDB 驱动程序

<article class="markdown-body entry-content container-lg" itemprop="text">

[![Build Status](img/680d037b44ff7694105d300882ed3246.png) ](https://travis-ci.org/globalsign/mgo) [ ![GoDoc](img/98e94dce65890bfe771c05a98b887b19.png)](https://godoc.org/github.com/globalsign/mgo)

## 用于 Go 的 MongoDB 驱动程序

这个分支由我们自己进行了一些改进，还有几个从最初的 mgo repo 合并而来的 PR，目前正在等待审查。虽然增加了一些新功能，但更改主要是针对性能改进和错误修复。

进一步的公关(与测试)是受欢迎的，但请保持向后兼容。

API 的详细文档可在 [GoDoc](https://godoc.org/github.com/globalsign/mgo) 获得。

实现 [BSON](http://bsonspec.org) 规范的[子包](https://godoc.org/github.com/globalsign/mgo/bson)也包括在内，可以独立于驱动程序使用。

## 支持的版本

众所周知，它可以在 MongoDB v3.0、3.2、3.4 和 3.6 上很好地工作(并对其进行了集成测试)。

MongoDB 4.0 目前处于试验阶段——我们很乐意接受 PRs 来帮助改进支持！

## 变化

*   修复了每次查询前尝试验证的问题([详细信息](https://github.com/go-mgo/mgo/issues/254))
*   取消批量更新/删除批量限制([详情](https://github.com/go-mgo/mgo/issues/288))
*   添加对`time.Duration`编组的本机支持…

</article>

[View on GitHub](https://github.com/globalsign/mgo)

### 4。活力

🌠*5901*

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)/[zap](https://github.com/uber-go/zap)

### Go 中极快、结构化、分级的日志记录。

<article class="markdown-body entry-content container-lg" itemprop="text">

# <g-emoji class="g-emoji" alias="zap" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/26a1.png">zap[![GoDoc](img/4365fb8760196e3293b933c3ea854851.png)](https://godoc.org/go.uber.org/zap)[![Build Status](img/e36ad5a99cf5fcce5a40a1ec18af34a1.png)](https://travis-ci.com/uber-go/zap)[![Coverage Status](img/bf3b65ad4f587661633b30cc0f6c56ca.png)](https://codecov.io/gh/uber-go/zap)</g-emoji>

Go 中极快、结构化、分级的日志记录。

## 装置

`go get -u go.uber.org/zap`

注意 zap 只支持 Go 的两个最近的次要版本。

## 快速启动

在性能不错但不重要的情况下，使用`SugaredLogger`。它比其他结构化日志包快 4-10 倍，包括结构化和`printf`风格的 API。

```
logger, _ := zap.NewProduction()
defer logger.Sync() // flushes buffer, if any
sugar := logger.Sugar()
sugar.Infow("failed to fetch URL"
  // Structured context as loosely typed key-value pairs.
  "url", url
  "attempt", 3
  "backoff", time.Second,
)
sugar.Infof("Failed to fetch URL: %s", url)
```

当性能和类型安全至关重要时，使用`Logger`。它甚至比`SugaredLogger`更快，分配的内存也少得多，但它只是…

</article>

[View on GitHub](https://github.com/uber-go/zap)

### 5。 ~~Go.uuid~~ gofrs/uuid

🌠 *394*

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [ gofrs ](https://github.com/gofrs) / [ uuid](https://github.com/gofrs/uuid)

### 最初从 github.com/satori/go.uuid 叉出的 UUID 包裹

<article class="markdown-body entry-content container-lg" itemprop="text">

# UUID

[![License](img/f02e1e56f6baac2b70cd222ee4378a32.png)](https://github.com/gofrs/uuid/blob/master/LICENSE)[![Build Status](img/ab7781619cde4ec5b5f279f906b0aacb.png)](https://travis-ci.org/gofrs/uuid)[![GoDoc](img/86c0fcf9e125a120a569d59bf44ed268.png)](http://godoc.org/github.com/gofrs/uuid)[![Coverage Status](img/950c2a7c6eeb5c05f3ca90302926276a.png)](https://codecov.io/gh/gofrs/uuid/)[![Go Report Card](img/fbf33a5d563f2a8c4923d011eb4f92ef.png)](https://goreportcard.com/report/github.com/gofrs/uuid)

包 uuid 提供了 RFC-4122 中定义的通用唯一标识符(UUID)变体的纯 Go 实现。这个包支持不同格式的 UUIDs 的创建和解析。

该软件包支持以下 UUID 版本:

*   版本 1，基于时间戳和 MAC 地址(RFC-4122)
*   版本 2，基于时间戳、MAC 地址和 POSIX UID/GID (DCE 1.1)
*   版本 3，基于命名值的 MD5 散列(RFC-4122)
*   版本 4，基于随机数(RFC-4122)
*   第 5 版，基于命名值的 SHA-1 散列法(RFC-4122)

## 项目历史

这个项目最初是从[github.com/satori/go.uuid](https://github.com/satori/go.uuid)库分叉出来的，因为它似乎不再被维护，同时显示出[的关键缺陷](https://github.com/satori/go.uuid/issues/73)。我们已经决定接管这个项目，以确保它得到定期维护，为更大的 Go 社区造福。

我们要感谢马克西姆·布布里斯为最初的版本所做的辛勤工作…

</article>

[View on GitHub](https://github.com/gofrs/uuid)

### 6\. JWT-Go

🌠*4849*

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[dgrijalva](https://github.com/dgrijalva)/[jwt-go](https://github.com/dgrijalva/jwt-go)

### JSON Web 令牌的 Golang 实现(JWT)

<article class="markdown-body entry-content container-lg" itemprop="text">

# 智威汤逊围棋

[![Build Status](img/322f8c338a4afb146d21f041810f45ce.png) ](https://travis-ci.org/dgrijalva/jwt-go) [ ![GoDoc](img/cfca9066a5488d5f05601f44ccfae1bc.png)](https://godoc.org/github.com/dgrijalva/jwt-go)

一个 [go](http://www.golang.org) (或者搜索引擎友好的‘golang’)[JSON Web 令牌](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html)的实现

**新版本来了:**自 2016 年发布的 3.0.0 版本以来，已经有很多改进建议。我现在正致力于切割两个不同的版本:3.2.0 将包含任何不间断的变化或增强。4.0.0 将很快推出，其中将包括突破性的变化。请参见 4.0.0 里程碑，了解即将到来的事情。如果你有其他想法，或者想参与 4.0.0，现在正是时候。如果您依赖这个库，并且不想被中断，我建议您使用您的依赖管理工具来固定到版本 3。

**安全声明:**部分旧版 Go 在 cryotp/elliptic 中存在安全问题。建议至少升级到 1.8.3。有关更多详细信息，请参见问题#216。

**安全通知:**请务必[确认所展示的`alg`是您所期望的](https://auth0.com/blog/critical-vulnerabilities-in-json-web-token-libraries/) …

</article>

[View on GitHub](https://github.com/dgrijalva/jwt-go)

### 7。证明

🌠*6634*

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [担架](https://github.com/stretchr) / [作证](https://github.com/stretchr/testify)

### 一个包含通用断言和模拟的工具包，可以很好地与标准库配合使用

<article class="markdown-body entry-content container-lg" itemprop="text">

# 作证——你应该写测试

<g-emoji class="g-emoji" alias="information_source" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2139.png">https://cutt.ly/testify</g-emoji>ℹ️[我们正在开发 evidence v2，我们很想知道你想在里面看到什么，请在这里发表你的意见](https://cutt.ly/testify)

[![Build Status](img/48dfba51728d436e62c0af5f09782f62.png)](https://travis-ci.org/stretchr/testify)[![Go Report Card](img/c28249e54c516edadaf9e652a502197b.png)](https://goreportcard.com/report/github.com/stretchr/testify)[![GoDoc](img/21ce95ab3f1c434596faf42c7714866e.png)](https://godoc.org/github.com/stretchr/testify)

Go code (golang)一组包，提供了许多工具来证明您的代码将如您所愿地运行。

功能包括:

*   [简单断言](https://raw.githubusercontent.com/stretchr/testify/master/#assert-package)
*   [嘲讽](https://raw.githubusercontent.com/stretchr/testify/master/#mock-package)
*   [测试套件接口和功能](https://raw.githubusercontent.com/stretchr/testify/master/#suite-package)

开始使用:

*   用一行代码安装[验证，或者用另一行代码](https://raw.githubusercontent.com/stretchr/testify/master/#installation)更新
*   关于在 Go 中编写测试代码的介绍，参见[http://golang.org/doc/code.html#Testing](http://golang.org/doc/code.html#Testing)
*   查看 API 文档[http://godoc.org/github.com/stretchr/testify](http://godoc.org/github.com/stretchr/testify)
*   为了让你的测试生活更容易，看看我们的另一个项目， [gorc](http://github.com/stretchr/gorc)
*   关于[测试驱动开发](http://en.wikipedia.org/wiki/Test-driven_development)

## [`assert`](http://godoc.org/github.com/stretchr/testify/assert "API documentation") 包

`assert`包提供了一些有用的方法，允许你在 Go 中编写更好的测试代码。

*   打印友好、易读的故障描述
*   允许非常可读的代码
*   可选地用消息注释每个断言

看到了吗…

</article>

[View on GitHub](https://github.com/stretchr/testify)

### 8。Go-yaml

🌠*2692*

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [围棋手](https://github.com/go-yaml) / [围棋手](https://github.com/go-yaml/yaml)

### YAML 对 Go 语言的支持。

<article class="markdown-body entry-content container-lg" itemprop="text">

# YAML 对 Go 语言的支持

## 介绍

yaml 包使 Go 程序能够轻松地编码和解码 YAML 值。它是在 [Canonical](https://www.canonical.com) 中开发的，作为 [juju](https://juju.ubuntu.com) 项目的一部分，它基于著名的 [libyaml](http://pyyaml.org/wiki/LibYAML) C 库的一个纯 Go 端口来快速可靠地解析和生成 yaml 数据。

## 和睦相处

yaml 包支持大部分 YAML 1.1 和 1.2，包括对锚、标签、地图合并等的支持。多文档解组还没有实现，YAML 1.1 中的 base-60 float 也不被支持，因为它们是一个糟糕的设计，在 YAML 1.2 中已经消失了。

## 安装和使用

包的导入路径是*gopkg.in/yaml.v2*。

要安装它，请运行:

```
go get gopkg.in/yaml.v2 
```

## API 文档

如果在浏览器中打开，导入路径本身会指向 API 文档:

*   [https://gopkg.in/yaml.v2](https://gopkg.in/yaml.v2)

## API 稳定性

yaml v2 的包 API 将保持稳定，如…

</article>

[View on GitHub](https://github.com/go-yaml/yaml)

* * *

## 工具

### 1 .橡皮+海绵

🌠*2023*

Gomock 是一个测试库，它允许你模仿你的依赖项，并对它们做出断言。Mockgen 是一个与 gomock 打包在一起的 CLI 工具，用于创建您的模拟。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [戈朗](https://github.com/golang) / [嘲弄](https://github.com/golang/mock)

### GoMock 是一个模仿 Go 编程语言的框架。

<article class="markdown-body entry-content container-lg" itemprop="text">

# gomock [![Build Status](img/66f1085a4f6868f023732b13e72d7090.png) ](https://travis-ci.org/golang/mock) [ ![GoDoc](img/8e73889349a4228c5d80866537a83c58.png)](https://godoc.org/github.com/golang/mock/gomock)

GoMock 是一个模仿 T2 编程语言的框架。它与 Go 内置的`testing`包集成得很好，但也可以用于其他环境。

## 装置

一旦你安装了[去](http://golang.org/doc/install.html#releases)，安装`mockgen`工具。

要获得最新发布的版本，请使用:

```
GO111MODULE=on go get github.com/golang/mock/mockgen@v1.4.3
```

如果您在 CI 渠道中使用`mockgen`,关注特定的 mockgen 版本可能更合适。

## 证明文件

安装后，您可以使用`go doc`获取文档:

```
go doc github.com/golang/mock/gomock
```

或者，这里有 GoPkgDoc [上托管的包的在线参考。](http://godoc.org/github.com/golang/mock/gomock)

## 运行模拟

有两种操作模式:源和反射源模式从源文件生成模拟接口，它是通过使用-source 标志启用的。在这种模式下可能有用的其他标志是-imports 和-aux_files。

示例:

```
mockgen -source=foo.go [other options]
```

反射模式通过…生成模拟接口

</article>

[View on GitHub](https://github.com/golang/mock)

### 2。勇敢点

🌠 *977*

*一个文件监视器，允许你重启你的应用程序，运行你的单元测试等等*

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [科尔特斯](https://github.com/cortesi) / [莫德](https://github.com/cortesi/modd)

### 一个灵活的开发工具，运行进程并响应文件系统的变化

<article class="markdown-body entry-content container-lg" itemprop="text">

[![Travis Build Status](img/281a7f01126b012078e736adfad52005.png)](https://travis-ci.org/cortesi/modd)

Modd 是一个开发工具，它触发命令并管理守护进程以响应文件系统的变化。

如果你使用 modd，你也应该看看 [devd](https://github.com/cortesi/devd) ，一个面向开发者的紧凑 HTTP 守护进程 devd 与 modd 集成，允许你用 modd 触发浏览器内 livereload。

repo 包含一组示例 *modd.conf* 文件，您可以快速了解 modd 可以做什么:

| 例子 | 描述 |
| --- | --- |
| [frontend.conf](https://raw.githubusercontent.com/cortesi/modd/master/./examples/frontend.conf) | React + Browserify + Babel 的前端项目。Modd 和 devd 取代了 Gulp/Grunt 的很多功能。 |
| [go.conf](https://raw.githubusercontent.com/cortesi/modd/master/./examples/go.conf) | Go 的实时单元测试。 |
| [python.conf](https://raw.githubusercontent.com/cortesi/modd/master/./examples/python.conf) | Python + Redis，用 devd 管理 livereload。 |

# 安装

Modd 是一个没有外部依赖的单一二进制文件，为 OSX Windows、Linux、FreeBSD、NetBSD 和 OpenBSD 发布。转到[发布页面](https://github.com/cortesi/modd/releases/latest)，下载你的操作系统的软件包，并将二进制文件复制到你的路径上的某个地方。

如果你有一个工作的 Go 安装，你…

</article>

[View on GitHub](https://github.com/cortesi/modd)

* * *

我希望您会发现这些库/工具中的一些对您的需求有用。

不要犹豫，提出你觉得很棒的建议。

感谢您的阅读！
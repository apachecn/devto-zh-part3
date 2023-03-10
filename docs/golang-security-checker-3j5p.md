# Golang 安全检查器

> 原文：<https://dev.to/renatosuero/golang-security-checker-3j5p>

Gosec 是运行我们的 Go 代码并确保我们没有安全问题的一个很好的工具。为了保持帖子简短，我不会解释安全问题，我的重点是向您展示如何运行该工具。也许在未来，我可以写关于这个问题，现在如果你想了解更多，请阅读[这篇文章](https://blog.sideci.com/lets-take-a-look-at-the-rules-of-gas-442ba286b0fe)

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)[securego](https://github.com/securego)/[gosec](https://github.com/securego/gosec)

### Golang 安全检查器

<article class="markdown-body entry-content container-lg" itemprop="text">

# gosec - Golang 安全检查器

通过扫描 Go AST 来检查源代码的安全问题。

[![](img/f801a72c2c40489094c6f98f0c03d302.png)](https://camo.githubusercontent.com/19701f26341abb91039ce91da2e1222c2ce8c8c12954ca7f35a6365b79ebe2df/68747470733a2f2f736563757265676f2e696f2f696d672f676f7365632e706e67)

## 许可证

根据 Apache 许可证 2.0 版(下称“许可证”)授权，您不得使用本文件，除非符合许可证的规定。您可以在此处获得许可证的副本[。](http://www.apache.org/licenses/LICENSE-2.0)

## 项目状态

[![CII Best Practices](img/b83c7862fe41fe81aaafaef96379cfdf.png)](https://bestpractices.coreinfrastructure.org/projects/3218)[![Build Status](img/72a666496dd8c80dd36c4b0a5994ba91.png)](https://github.com/securego/gosec/actions?query=workflows%3ACI)[![Coverage Status](img/f31a594f741b3d5115e9d1b1e0fb9ba7.png)](https://codecov.io/gh/securego/gosec)[![GoReport](img/0a894b1510d719de7663f4d28523d8f0.png)](https://goreportcard.com/report/github.com/securego/gosec)[![GoDoc](img/815c6b7478dc0bbea61eb12f9b9e5ddd.png)](https://pkg.go.dev/github.com/securego/gosec/v2)[![Docs](img/1899fa9916eaefb833114d4f0e1619af.png)](https://securego.io/)[![Downloads](img/de2247ef7c81ecc753768e0bccdc6174.png)](https://github.com/securego/gosec/releases)[![Docker Pulls](img/c54c0e3c0f9a0ab629df82a4e32086cf.png)](https://hub.docker.com/r/securego/gosec/tags)[T25】](http://securego.herokuapp.com)

## 安装

### CI 安装

```
# binary will be $(go env GOPATH)/bin/gosec
curl -sfL https://raw.githubusercontent.com/securego/gosec/master/install.sh | sh -s -- -b $(go env GOPATH)/bin vX.Y.Z
# or install it into ./bin/
curl -sfL https://raw.githubusercontent.com/securego/gosec/master/install.sh | sh -s vX.Y.Z

# In alpine linux (as it does not come with curl by default)
wget -O - -q https://raw.githubusercontent.com/securego/gosec/master/install.sh | sh -s vX.Y.Z

# If you want to use the checksums provided on the "Releases" page
# then you will have to download a tar.gz file for your operating system instead of a binary file
wget https://github.com/securego/gosec/releases/download/vX.Y.Z/gosec_vX.Y.Z_OS.tar.gz

# The file will be in
```

…Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/securego/gosec)

您可以使用二进制文件或 Docker 来运行该工具。我会给你看这两种情况。使用二进制你需要运行到你的根目录:

```
gosec *.go 
```

Enter fullscreen mode Exit fullscreen mode

使用 docker，它需要更多的配置，但它工作得很好。在自述文件中，该命令是 *securego/gosec。/...*，对我没用，我就换掉了**。/...**到**投影路径**T6】

```
docker run -it -v $GOPATH/src/<YOUR PROJECT PATH>:/go/src/<YOUR PROJECT PATH> securego/gosec $GOPATH/src/<YOUR PROJECT PATH> 
```

Enter fullscreen mode Exit fullscreen mode

即使使用 go 模块，也需要使用这种格式来运行工具。

乡亲们就这些，希望能对你们有用=)
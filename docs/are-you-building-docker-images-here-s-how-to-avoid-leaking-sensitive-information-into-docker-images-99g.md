# 你在建立 Docker 图像吗？以下是如何避免将敏感信息泄露到 Docker 图像中

> 原文：<https://dev.to/lirantal/are-you-building-docker-images-here-s-how-to-avoid-leaking-sensitive-information-into-docker-images-99g>

> 这个技巧是你应该采用的完整的 [10 个 Docker 图像安全最佳实践](https://snyk.io/blog/10-docker-image-security-best-practices/)的一部分。感谢你的阅读，也感谢和我一起工作的[奥马尔·列维·赫弗罗尼](https://twitter.com/omerlh)。

有时，在 Docker 映像中构建应用程序时，您需要 SSH 私钥之类的秘密来从私有存储库中提取代码，或者您需要令牌来安装私有包。

如果您将它们复制到 Docker 中间容器中，即使您稍后删除它们，它们也会被缓存在它们被添加到的层上。这些令牌和密钥必须保存在`Dockerfile`之外。

## 使用多阶段构建

通过利用 Docker 对多阶段构建的支持，获取并管理中间映像层中的机密信息，该层稍后会被处理掉，这样就不会有敏感数据到达映像构建。

使用代码将秘密添加到所述中间层，例如下面的例子:

```
FROM: ubuntu as intermediate

WORKDIR /app
COPY secret/key /tmp/
RUN scp -i /tmp/key build@acme/files .

FROM ubuntu
WORKDIR /app
COPY --from=intermediate /app . 
```

Enter fullscreen mode Exit fullscreen mode

## 使用 Docker 秘密命令

使用 Docker 中的 alpha 特性来管理机密，以挂载敏感文件而不缓存它们，类似于下面的:

```
# syntax = docker/dockerfile:1.0-experimental
FROM alpine

# shows secret from default secret location
RUN --mount=type=secret,id=mysecret cat /run/secrets/mysecre

# shows secret from custom secret location
RUN --mount=type=secret,id=mysecret,dst=/foobar cat /foobar 
```

Enter fullscreen mode Exit fullscreen mode

## 谨防递归复制

在将文件复制到正在构建的映像中时，您也应该小心。

例如，下面的命令将整个构建上下文文件夹递归地复制到 Docker 映像中，这最终也会复制敏感文件:

```
COPY . . 
```

Enter fullscreen mode Exit fullscreen mode

如果你的文件夹中有敏感文件，要么删除它们，要么使用`.dockerignore`忽略它们:

```
private.key
appsettings.json 
```

Enter fullscreen mode Exit fullscreen mode

* * *

最初的博客帖子包括一个高分辨率的可打印 PDF，就像你在下面看到的片段。[看看吧](https://snyk.io/blog/10-docker-image-security-best-practices/)
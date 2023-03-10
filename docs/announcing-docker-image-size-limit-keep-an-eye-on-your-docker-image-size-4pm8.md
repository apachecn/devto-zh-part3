# 宣布 docker-image-size-limit:留意你的 docker 图像大小

> 原文：<https://dev.to/wemake-services/announcing-docker-image-size-limit-keep-an-eye-on-your-docker-image-size-4pm8>

原发表于我的博客:[https://sobolevn . me/2019/03/announcing-docker-image-size-limit](https://sobolevn.me/2019/03/announcing-docker-image-size-limit)

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[we make-services](https://github.com/wemake-services)/[docker-image-size-limit](https://github.com/wemake-services/docker-image-size-limit)

### 🐳留意你的 docker 图片大小，防止它变得太大

<article class="markdown-body entry-content container-lg" itemprop="text">

# docker-图像-大小-限制

[![wemake.services](img/e779a35745bf1bc131027b7712420e90.png)](https://wemake.services)[![Build status](img/becc03c0d544ec5c4088f4ac53d5fba1.png)](https://github.com/wemake-services/docker-image-size-limit/actions?query=workflow%3Atest)[![codecov](img/d1372b2363c9f9e15357c84561abf695.png)](https://codecov.io/gh/wemake-services/docker-image-size-limit)[![Python Version](img/2be3c9f4c6d1d449dcd5a932a829463f.png)](https://pypi.org/project/docker-image-size-limit/)[![wemake-python-styleguide](img/29fd18d70d1bbea101ed0c3c08584cba.png)](https://github.com/wemake-services/wemake-python-styleguide)

使用简单的 CLI 命令限制您的`docker`图像大小，非常适合在您的 CI 流程中使用。

阅读[公告帖](https://sobolevn.me/2019/03/announcing-docker-image-size-limit)。

## 装置

```
pip install docker-image-size-limit
```

或者使用我们的 [Github 动作](https://github.com/wemake-services/docker-image-size-limit#github-action)或者[预置的 docker 镜像](https://github.com/wemake-services/docker-image-size-limit#docker-image)。

## 使用

我们只支持一个命令:

```
$ disl your-image-name:label 300MiB
your-image-name:label exceeds 300MiB limit by 114.4 MiB
```

## 选择

您可以将图像指定为:

*   图像名称:`python`
*   带标签的图像名称:`python:3.6.6-alpine`

您可以将您的尺寸指定为:

*   原始字节数:`1024`
*   人类可读的兆字节:`30 MB`或`30 MiB`
*   人类可读的千兆字节:`1 GB`或`1 GiB`
*   [`humanfriendly`](https://humanfriendly.readthedocs.io/en/latest/api.html#humanfriendly.parse_size) 支持的任何其他尺寸

## 程序用法

您也可以导入并使用这个库作为`python`代码:

```
from docker import from_env
from docker_image_size_limit import check_image_size
oversize = check_image_size(from_env(), 'image-name:latest', '1 GiB')
assert oversize < 0, 'Too big image!'  # negative oversize - is a good thing!
```

…

</article>

[View on GitHub](https://github.com/wemake-services/docker-image-size-limit)

## 我的故事

那是一个清晨。我正在喝我的第一杯茶🍵以及检查来自另一个开发人员的拉请求。

看起来相当不错。只是一些新的`python`依赖项来生成漂亮的报告。和一堆新的`alpine`库来确保一切正常。

所以，我毫不犹豫地合并了它。

几个小时后，我发现我们的图像尺寸从`~200 MiB`增加到了`~1.5 GiB`。💥这是不可接受的！

所以，我写了这个脚本来限制未来的最大图像大小:

```
LIMIT=1024
IMAGE='your-image-name:latest'

SIZE="$(docker image inspect "$IMAGE" --format='{{.Size}}')"
test "$SIZE" -gt "$LIMIT" && echo 'Limit exceeded'; false 
```

这就好比`js` [`size-limit`](https://github.com/ai/size-limit) 图书馆，但对于`docker`。

效果非常好。现在，我们的 CI 在比我们的`$LIMIT`更大的图像上会失败。但是，你知道我们在用[【无可指责的环境】](https://sobolevn.me/2018/12/blameless-environment)的方法吗？如果某个项目失败了，就一劳永逸地解决它，包括其他项目。现在，我必须通过复制粘贴这四行代码，将这些代码分发到我们所有的项目中。当然，这不是我喜欢的代码分发方式。

## 新建项目

因此，我将这个脚本开源为一个独立的`python` CLI，可以方便地分发、安装和使用:

```
$ pip install docker-image-size-limit
$ disl your-image-name:label 300MiB
your-image-name:label exceeds 300MiB limit by 1200 MiB 
```

仅此而已。

现在，您可以确信您的图像大小将始终在控制之中。签出[文档](https://github.com/wemake-services/docker-image-size-limit)中所有可能的选项。并将其集成到您的 CI 中，以避免犯我已经修复的错误。🔧
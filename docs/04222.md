# 从头开始构建 100kB Docker 映像

> 原文：<https://dev.to/tonymet/build-100kb-docker-images-from-scratch-4ll5>

📓[要旨](https://gist.github.com/tonymet/646f1125f30e826e4734780c74a3fa44)

你可能认为你的 100mB 阿尔卑斯山图片很小——但是 100kB 呢？较小的图像发送速度更快，包含的攻击媒介也更少。此外，通过优化图像，您可以准确地发现和隔离应用运行所需的内容。

## 优化一下吧。

基于暂存的 docker 映像有两个关键特征:

1.  Dockerfile 文件有两个构建阶段:
    *   构建器——包含所有的构建依赖项，包括源代码、库和工具..
    *   最终映像，包含二进制文件和任何运行时依赖项(配置文件、证书和动态链接库)
2.  最后的图像是`FROM scratch`——空的 docker 图像

使用这种方法，您的运行时映像将包含您的应用程序运行所需的内容——没有额外的配置文件、守护程序或库，它们可能会被错误配置或利用。

让我们来看一个基本的静态 Hello World scratch 图像。我们将使用 C 语言，因为它需要最少的依赖，并且产生极小的 ELF 对象二进制。

## 创建 Hello World

`hello.c`

```
#include <stdio.h>
int main(void){
    puts("Hello World\n");
} 
```

`Makefile`

```
hello: hello.c
    gcc -o $@  $< -static 
```

## docker file

`.dockerignore`

```
# this prevents our host binary from sneaking into the build
hello 
```

`Dockerfile`

```
FROM alpine:latest as builder
WORKDIR /build
RUN apk update && \
    apk add gcc make libc-dev
COPY .  ./
RUN make

FROM scratch
WORKDIR /
COPY --from=builder /build/hello .
CMD ["/hello"] 
```

注意，我们有两个`FROM`，一个叫做`builder`。最终图像将是我们的跑步者。使用`COPY --from`我们可以选择哪些文件进入暂存图像。有了静态 ELF 二进制文件，我们只需要`/build/hello`

## 构建并运行

```
$ docker build . -t hello-scratch|tail -n 1
Successfully tagged hello-scratch:latest 
```

```
$ docker run hello-scratch                 
Hello World 
```

## 我们最终的图像是 82.7kB

```
$ docker images |grep hello-scratch | egrep -o '[^ ]+$'
82.7kB 
```

## 里面是什么？

使用`docker save`可以检查图像，看到`layer.tar`只包含一个文件:`hello`

```
 $ mkdir hello-scratch && cd hello-scratch 
$ docker save hello-scratch |tar -x
$ ls
3e69d91b5842be72dcd4175adcf218a03f78826504be6a46ed41c099e97520e8.json  e599e214ce17b356493f9524fa57f7ef816d21dd78020019196020c770a39954  manifest.json  repositories
$ ✗ tar -tf e599e214ce17b356493f9524fa57f7ef816d21dd78020019196020c770a39954/layer.tar 
hello 
```

## 下一步

在接下来的文章中，我将展示一些更复杂的例子。使用静态编译的应用程序，如 C、C++、golang & rust，这个过程是最简单的。但是通过适当的工具，假设您将所有运行时依赖项收集到最终的`scratch`映像中，任何映像都可以构建。
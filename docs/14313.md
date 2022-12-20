# Crystal 的多级 Docker 构建

> 原文：<https://dev.to/citizen428/multi-stage-docker-builds-for-crystal-eci>

Docker 的[多阶段构建](https://docs.docker.com/develop/develop-images/multistage-build/)是缩小图像大小的一个极好的方法，我在我的 Go 项目中大量使用它们。今天我意识到[水晶](https://crystal-lang.org/)有一个`--static`标志，根据文档[它只在 Alpine Linux](https://github.com/crystal-lang/crystal/wiki/Static-Linking) 上工作。因此，我决定尝试多阶段构建，并将其与其他 Docker 方法进行比较。

这里是公认的不太令人兴奋的示例程序:

```
# test.cr
puts "Hello Docker world!" 
```

Enter fullscreen mode Exit fullscreen mode

## 从水晶影像中构建

水晶维护者提供了官方的 Docker 图片，所以这是我们第一次构建的起点:

```
FROM crystallang/crystal
WORKDIR /src
COPY . .
RUN crystal build --release test.cr -o /test
ENTRYPOINT ["/test"] 
```

Enter fullscreen mode Exit fullscreen mode

让我们验证一下一切是否如预期的那样工作:

```
❯ docker run -it --rm crystal-test:crystal
Hello Docker world! 
```

Enter fullscreen mode Exit fullscreen mode

这并不奇怪，但是可惜的是图像的大小还有很多需要改进的地方

```
REPOSITORY   TAG     ... SIZE
crystal-test crystal ... 635MB 
```

Enter fullscreen mode Exit fullscreen mode

## 建筑来自阿尔卑斯山

下一次尝试使用 [Alpine Linux](https://alpinelinux.org/) 作为基础映像。然后，我们安装
水晶本身、[碎片](https://github.com/crystal-lang/shards)依赖管理器和`libc-dev`元包，它们将为平台带来正确的`libc`版本:

```
FROM alpine:latest
RUN apk add -u crystal shards libc-dev
WORKDIR /src
COPY . .
RUN crystal build --release test.cr -o /test
ENTRYPOINT ["/test"] 
```

Enter fullscreen mode Exit fullscreen mode

一切仍按预期运行:

```
❯ docker run -it --rm crystal-test:alpine
Hello Docker world! 
```

Enter fullscreen mode Exit fullscreen mode

我们还设法显著减小了我们的图像大小，但 226MB 对于一个简单的“Hello World”应用程序来说仍然很不理想。

```
REPOSITORY   TAG    ... SIZE
crystal-test alpine ... 226MB 
```

Enter fullscreen mode Exit fullscreen mode

## 阿尔卑斯山的多阶段建造

最后但同样重要的是，承诺的多阶段构建。我们再次从阿尔卑斯山的图像开始，我们称之为`builder`，但是在`crystal build`命令中添加了一个额外的`--static`旗帜。在第二阶段，我们将生成的静态二进制文件复制到一个 [Busybox](https://busybox.net/about.html) 容器中，并从那里运行它:

```
FROM alpine:latest as builder
RUN apk add -u crystal shards libc-dev
WORKDIR /src
COPY . .
RUN crystal build --release --static test.cr -o /src/test

FROM busybox
WORKDIR /app
COPY --from=builder /src/test /app/test
ENTRYPOINT ["/app/test"] 
```

Enter fullscreen mode Exit fullscreen mode

让我们快速验证一下一切都还正常:

```
❯ docker run -it --rm crystal-test:multi
Hello Docker world! 
```

Enter fullscreen mode Exit fullscreen mode

最终得到的图像小于 3MB，比官方图像减少了 630MB，比“正常”的阿尔卑斯山建筑减少了 220MB。

```
REPOSITORY   TAG   ... SIZE
crystal-test multi ... 2.86MB 
```

Enter fullscreen mode Exit fullscreen mode

使用`scratch`而不是`busybox`进一步将大小减少到 1.66MB，考虑到 macOS 上的动态链接版本重达 206kB，这还不错。

## 总结

使用 Alpine Linux 中的 Crystal 的`--static`标志和 Docker 的多阶段构建，很容易向用户提供 Crystal 应用程序的非常小的 Docker 映像。尺寸的大幅减小无疑弥补了稍微复杂一点的`Dockerfile`，并使 Docker 成为 Crystal 应用程序的一种可行的分发格式。
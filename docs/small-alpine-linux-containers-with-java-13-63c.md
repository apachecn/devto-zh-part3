# 带有 Java 的小型 Alpine Linux 容器

> 原文：<https://dev.to/l1x/small-alpine-linux-containers-with-java-13-63c>

## 更新

### 2019.10.21

原帖是关于 Java 13 的。甲骨文相当频繁地更改 JDK 版本。JDK 的最新版本是 14:

[https://download . Java . net/Java/early _ access/alpine/15/binaries/open JDK-14-ea+15 _ Linux-x64-musl _ bin . tar . gz](https://download.java.net/java/early_access/alpine/15/binaries/openjdk-14-ea+15_linux-x64-musl_bin.tar.gz)

## [简介](#intro)

我经常听到开发人员抱怨说 Java 容器太大了，而用 Go 或其他语言会小得多。有了这个名为 [Portola](https://openjdk.java.net/projects/portola/) 的新项目，就有可能制作非常小的(~40MB)容器来运行 Java 应用程序。Alpine Linux 成为小型容器的事实上的标准，但是直到现在，使用它来创建 Java 环境还是一个相当复杂的过程。现在情况不再是这样了。让我们看看如何利用 Portola 项目来创建这些小容器。

## [创建容器](#creating-containers)

首先，我们创建一个容器，它有一个新的小尺寸 JDK。

```
FROM  alpine:latest  as  build

ADD https://download.java.net/java/early_access/alpine/15/binaries/openjdk-14-ea+15_linux-x64-musl_bin.tar.gz /opt/jdk/
RUN tar -xzvf /opt/jdk/openjdk-14-ea+15_linux-x64-musl_bin.tar.gz -C /opt/jdk/

RUN ["/opt/jdk/jdk-14/bin/jlink", "--compress=2", \
     "--module-path", "/opt/jdk/jdk-14/jmods/", \
     "--add-modules", "java.base", \
     "--output", "/jlinked"]

FROM alpine:latest
COPY --from=build /jlinked /opt/jdk/
CMD ["/opt/jdk/bin/java", "--version"] 
```

Enter fullscreen mode Exit fullscreen mode

文章的其余部分与 JDK 版本无关。docker build 的输出可能会反映早期版本。

我们可以开始构建容器:

```
[v@alpine-java jdk14_v]$ sudo docker build .
Sending build context to Docker daemon   2.56kB
Step 1/8 : FROM alpine:latest as build
latest: Pulling from library/alpine
bdf0201b3a05: Pull complete Digest: sha256:28ef97b8686a0b5399129e9b763d5b7e5ff03576aa5580d6f4182a49c5fe1913
Status: Downloaded newer image for alpine:latest
 ---> cdf98d1859c1
Step 2/8 : ADD https://download.java.net/java/early_access/alpine/16/binaries/openjdk-13-ea+16_linux-x64-musl_bin.tar.gz /opt/jdk/
Downloading [==================================================>]  195.2MB/195.2MB
 ---> Using cache
 ---> b1a444e9dde9
Step 3/7 : RUN tar -xzvf /opt/jdk/openjdk-13-ea+16_linux-x64-musl_bin.tar.gz -C /opt/jdk/
 ---> Using cache
 ---> ce2721c75ea0
Step 4/7 : RUN ["/opt/jdk/jdk-13/bin/jlink", "--compress=2",      "--module-path", "/opt/jdk/jdk-13/jmods/",      "--add-modules", "java.base",      "--output", "/jlinked"]
 ---> Using cache
 ---> d7b2793ed509
Step 5/7 : FROM alpine:latest
 ---> cdf98d1859c1
Step 6/7 : COPY --from=build /jlinked /opt/jdk/
 ---> Using cache
 ---> 993fb106f2c2
Step 7/7 : CMD ["/opt/jdk/bin/java", "--version"] - to check JDK version
 ---> Running in 8e1658f5f84d
Removing intermediate container 8e1658f5f84d
 ---> 350dd3a72a7d
Successfully built 350dd3a72a7d 
```

Enter fullscreen mode Exit fullscreen mode

尽管 JDK 映像有 195MB，但内部版本只有 41MB。我们可以标记图像。

```
[v@alpine-java jdk13_v]$ sudo docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
<none>              <none>              350dd3a72a7d        21 seconds ago      41.7MB
<none>              <none>              d7b2793ed509        25 minutes ago      565MB
alpine              latest              cdf98d1859c1        2 weeks ago         5.53MB
[v@alpine-java jdk13_v]$ sudo docker tag 350dd3a72a7d jdk-13-musl/jdk-version:v1
[v@alpine-java jdk13_v]$ sudo docker images
REPOSITORY                TAG                 IMAGE ID            CREATED              SIZE
jdk-13-musl/jdk-version   v1                  350dd3a72a7d        About a minute ago   41.7MB
<none>                    <none>              d7b2793ed509        27 minutes ago       565MB
alpine                    latest              cdf98d1859c1        2 weeks ago          5.53MB 
```

Enter fullscreen mode Exit fullscreen mode

运行容器:

```
[v@alpine-java jdk13_v]$ sudo docker run jdk-13-musl/jdk-version:v1
openjdk 13-ea 2019-09-17
OpenJDK Runtime Environment (build 13-ea+16)
OpenJDK 64-Bit Server VM (build 13-ea+16, mixed mode) 
```

Enter fullscreen mode Exit fullscreen mode

## [构建 HelloWorld 应用](#building-a-hello-world-app)

现在我们有了一个基础容器，可以用它来创建一个 Java 应用程序。让我们用一个简单的 HelloWorld.java。

```
public class HelloWorld {
  public static void main(String[] args) {
    System.out.println("Hello, World");
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

编译 Java 代码:

```
javac HelloWorld.java 
```

Enter fullscreen mode Exit fullscreen mode

为应用程序容器准备另一个 docker 文件:

```
FROM jdk-13-musl/jdk-version:v1
ADD HelloWorld.class /
CMD ["/opt/jdk/bin/java", "HelloWorld"] 
```

Enter fullscreen mode Exit fullscreen mode

建筑容器:

```
sudo docker build . 
```

Enter fullscreen mode Exit fullscreen mode

标记后，我们可以运行 HelloWorld:

```
sudo docker run jdk-13-musl/hello-world:v1
Hello, World 
```

Enter fullscreen mode Exit fullscreen mode

整个 docker 运行大约需要 600 毫秒。对 Java 来说还不错。
# 我在演示和演示 Docker 时使用的技巧和窍门

> 原文：<https://dev.to/gamussa/tips-and-tricks-that-i-use-in-my-demos-and-presentation-with-docker-omo>

目录

*   [删除每个 Docker 容器](#delete-every-docker-containers)
*   [打开运行容器中的外壳](#open-shell-in-running-container)
    *   Extra carriculum matterial:在 Docker 撰写的情况下如何做同样的事情？

[![me presenting](img/80a6d919172b1d3e43c336b2aef883d4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--w-gqcMTx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://gamov.img/me_presenting.jpg)

图一。给你一点小费！

TL；速度三角形定位法(dead reckoning)

这篇文章的唯一目的是帮助我在忘记一些命令时不时回到这里。如果这对任何人有用的话——在 twitter 上赞美我——[http://twitter.com/gamussa](http://twitter.com/gamussa)。

让我们从一件很小但很重要的事情开始——如何擦除 Docker 上的所有内容并从头开始。

## 删除每个 Docker 容器

|注意:|如果你在飞机上，不要这样做。使用航空公司的 wifi 下载新图像将是一大难题。小心行事。你已经被警告了。|

有多少次，你发现自己的笔记本电脑空间不够用了，而你不知道谁在吃掉所有的空间。这里通常怀疑的是 maven 或 gradle 目录 jar 的目录和 Docker 图像目录。

```
❯ du -sh ~/.m2
2.4G /Users/viktor/.m2 (1)

❯ du -sh ~/.gradle
2.8G /Users/viktor/.gradle (2)

❯ du -sh ~/Library/Containers/com.docker.docker/Data/com.docker.driver.amd64-linux/Docker.raw
8.1G /Users/viktor/Library/Containers/com.docker.docker/Data/com.docker.driver.amd64-linux/Docker.raw 
```

Enter fullscreen mode Exit fullscreen mode

我应该用核武器摧毁它吗？|
| **2** |我肯定用 Gradle 比较多|

必须首先运行，因为图像附加到容器

```
docker rm -f $(docker ps -a -q) 
```

Enter fullscreen mode Exit fullscreen mode

删除每个 Docker 图像

```
docker rmi -f $(docker images -q) 
```

Enter fullscreen mode Exit fullscreen mode

## 打开运行容器中的外壳

如果我有一个已经运行的容器，我可以使用`docker exec`命令来连接它！

首先，使用`docker ps`获取现有容器的名称

用`docker exec -it <container name> /bin/bash`获取容器中的 bash shell

本质上，使用`docker exec -it <container name> <command>`来执行您在容器中指定的任何命令。

### 课外资料:Docker Compose 的情况下如何做同样的事情？

```
docker-compose run <container_name_in_yml_file> <command>. 
```

Enter fullscreen mode Exit fullscreen mode

例如，要让一个 shell 进入你的 Kafka 容器，你可以运行`docker-compose run kafka1 /bin/bash`

要运行一系列命令，您必须使用 shell 将它们封装在一个命令中

```
docker-compose run <name in yml> sh -c '<command 1> && <command 2> && <command 3>' 
```

Enter fullscreen mode Exit fullscreen mode

`docker run`命令接受命令行选项来指定卷装载、环境变量、工作目录等等。
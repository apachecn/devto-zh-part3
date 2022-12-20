# 我倾向于临时使用的顶级 docker 图像

> 原文：<https://dev.to/clovis1122/top-docker-images-that-i-tend-to-use-ad-hoc-342g>

Docker 是一个构建和发布软件的伟大工具。世界各地的许多团队都在利用它的特性。对于开发来说，能够在一个`docker-compose.yml`文件中声明您的整个堆栈，并使用它需要运行的所有依赖项和包来启动和关闭它，这是非常有用的。

我倾向于大量使用 Docker，不仅是作为项目的一部分，也是为了执行一些特别的任务。对我来说，最大的好处是我不必费心安装和删除所需的软件——启动容器只需要一个 Docker 映像！这是我最常使用的 Docker 图片列表。

1.管理员

[![](img/11bd603cca60b69a7264280b4bc827e0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UGz-bk08--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://manuelcesarini.it/wp-content/uploads/2017/05/adminer-logo.jpg) 
Adminer(原 phpMinAdmin)是一款用 PHP 编写的全功能数据库管理工具。当您需要访问外部数据库时，它非常方便，拥有一个 UI 会很有帮助。

Docker 命令:

```
docker run -d -p 8080:8080 adminer 
```

解释:

```
-p 8080:8080 = link the host port 8080 with the container port 8080.
-d = run on detached mode. 
```

2.Nginx

[![](img/f8122c79aae6f4e422a95ea25922feae.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VnIMP3A4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.logolynx.cimg/logolynx/61/61c738af9de36116116271f3003c9f07.png)

Nginx 是一款高性能 Web 服务器，是全球多家公司的首选解决方案。如果您曾经不得不配置它，那么您可能希望能够在您的机器上进行，而不是必须连接到运行它的主机。

Docker 命令:

```
docker run -it -p 80:80 -v $(pwd):/usr/share/nginx/html nginx bash 
```

解释:

```
-p 80:80 = link the host port 80 with the container port 80.
-it = It is actually combining two flags, -i (interactive) and -t (allocate a tty).
-v = mount in volumen. $(pwd) is the current working directory and /usr/share/nginx/html is the place inside the container where it will be mounted.
nginx bash = run bash inside nginx (Inside the container you can run nginx commands as much as you want). 
```

3.Ansible

[![](img/813ee3a46ae24c7f2d13545d44b43280.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--h1cb4Ka_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://encrypted-tbn0.gstatic.com/images%3Fq%3Dtbn:ANd9GcSAUWECBfidOSKEcRqfj_EYKCCd7oTnSIvgETsE0QGXZNajCRwi)

Ansible 是一款应用和 IT 基础设施自动化工具。就像厨师和木偶。您告诉 Ansible 如何连接到主机并定义主机应该如何配置，它将为您配置主机。

Docker 命令:

```
docker run -it -v $(pwd):/app ansible bash 
```

解释:

```
-it = It is actually combining two flags, -i (interactive) and -t (allocate a tty).
-v = mount in volumen. $(pwd) is the current working directory and /app is the place inside the container where it will be mounted.
ansible bash = run bash inside ansible (Inside the container you can run ansible commands as much as you want). 
```

4.Sqlite3

[![](img/8b3e7e8b5160d21763cb0a2e4fdcf18d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NWV_RmFW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1200/1%2AfMPUMki182HzyVZvo_awDw.png)

SQLite 是一个 C 语言库，它实现了一个小型、快速、独立、高可靠性、全功能的 SQL 数据库引擎。数据可以存储在磁盘的单个文件中，也可以存储在内存中。

我最近一直在使用 sqlite3 与一个 Python webscrapper 进行交互，这个 webscrapper 将数据保存在 sqlite 文件中。CLI 和指令还可以做得更好，但肯定能完成任务！

Docker 命令:

```
docker run -it -v $(pwd):/root/db --entrypoint bash nouchka/sqlite3 
```

解释:

```
-it = It is actually combining two flags, -i (interactive) and -t (allocate a tty).
-v = mount in volumen. $(pwd) is the current working directory and /root/db is the place inside the container where it will be mounted.
--entrypoint = sets the entrypoint command of the container. In this case, the entrypoint will be Bash. 
```

关于 docker 运行命令的说明:

*   你可以添加`--rm`，它一停就会移除容器。

这些是我现在倾向于使用的。当然，当我学习某些东西时(最新的是 Python3 和 Elixir/Phoenix)或在其他情况下(检查我是否准备好了 Node V10 或对旧的 PHP5.4 应用程序进行维护)，我也会使用特别的图像。

当然，如果您正在为一个特定的项目运行一个特别的容器，您可能想要考虑使用`docker-compose`来代替。

你在日常生活中使用临时图像吗？哪些？
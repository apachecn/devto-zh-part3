# 使用 Docker Compose 快速启动 MySQL

> 原文：<https://dev.to/alexmacarthur/quickly-spin-up-mysql-w-docker-compose-4g35>

我经常需要快速启动 MySQL 的本地实例。[码头工人作曲](https://docs.docker.com/compose/)这让愚蠢变得容易。我不需要运行冗长、复杂的`docker`命令，而是可以按照我想要的方式配置图像，同时保持根据需要轻松打开和关闭图像的能力。

## 开始设置

在一个新目录中，创建一个`data`目录和`docker-compose.yml`文件。

```
new-directory
├── data
└── docker-compose.yml 
```

Enter fullscreen mode Exit fullscreen mode

将以下内容粘贴到您的`docker-compose.yml`文件中:

```
version: '3'

services: 
  db:
    container_name: docker-local-mysql
    image: mysql:5.7.21
    volumes:
      - "./data:/var/lib/mysql"
    restart: always
    ports:
      - 3306:3306
    environment:
      MYSQL_ROOT_PASSWORD: root 
```

Enter fullscreen mode Exit fullscreen mode

## 管理一个容器

要启动容器，运行`docker-compose up`。要停止&移除容器，运行`docker-compose down`。关于利用你的容器的众多命令的更多信息，[请看这里](https://docs.docker.com/compose/reference/)。

## 持久化数据

每当 MySQL 修改容器中的任何数据时，它都将本地保存在您的`./data`目录中，甚至在您停止并重新启动一切之后。这是由您的`docker-compose.yml`文件中的`volumes:`属性配置的。

## 容器内运行命令

要在运行容器中运行任何 Bash 命令，可以使用`docker-compose exec db bash`。

## 用 SequelPro 或类似工具连接

使用以下值连接到正在运行的容器。

**主机:** 127.0.0.1

**用户名:** root

**密码:** root

**端口:** 3306

## 结束。

嘣。只需一个命令，您就拥有了一个运行的、持久的、自包含的 MySQL 实例，可以满足您的开发需求。有什么有用的热门提示吗？分享一下！
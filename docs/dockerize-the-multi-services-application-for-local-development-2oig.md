# 为本地开发对多服务应用进行分类

> 原文：<https://dev.to/amplifr/dockerize-the-multi-services-application-for-local-development-2oig>

由于现在许多复杂的网络应用程序都是在容器化的产品上运行，我们继续用“老派”的方式开发它们，在本地开发机器上安装 [Postgresql](https://www.postgresql.org/) 、 [Redis](https://redis.io/) 、 [Ruby](https://www.ruby-lang.org) 和其他组件。

维护开发过程变得越来越困难，尤其是当系统变得异构并扩展成大量服务，运行各种版本的依赖组件时。当依赖组件的版本变化时，这变得尤其实际。

在本文中，我将以我参与的项目 [Amplifr](https://amplifr.com) 为例，回顾本地开发容器化。在 docker-compose 和 docker networks 的帮助下，它变得简单而高效。

由于所有的基础设施都是集装箱化的，并且在生产上由 Kubernetes 管理，我们将只参与建立**本地开发**，遵循一个原则- **开发过程的便利性**。

## 当地集装箱化的好处

*   不需要在本地机器上安装所有的组件，比如数据库引擎、语言解释器。它保持本地机器*干净*。
*   不同环境的自然支持，例如在本地机器上运行不同版本的 Ruby、Postgresql 的服务

## 项目概述

然而，Amplifr 的后端运行在 Rails 上，该项目也有复杂的前端，由独立的 Node.js 服务器和 Logux web-socket 服务器以及其他基于 Node.js、Ruby 和 Golang 编写的辅助服务提供服务。

下图显示了该项目的简化架构:

[![Overall Amplifr's services map](img/2ae5f8b276cfb964084cadbad69de90f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_G7FOi9_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zaqzt4xuskx9u1nam6ka.png)

我将快速回顾一下整个系统的一些组件。

### 后端服务器

后端是经典的 Rails 应用程序，使用 Sidekiq 完成所有的业务逻辑和许多后台工作。

### 前端服务器

前端是整个应用程序的唯一公共 HTTP 入口点。它为前端资产提供服务，并将其他请求代理到 Rails 后端。
后端还与前端服务器集成在一起，用于共享一些数据，[如`browsers.json`](https://dev.to/amplifr/outdated-browser-detection-with-browserslist-10co) 文件，用于正确呈现 HTML。

[![Frontend-backend integration](img/b3e8cbb99420ce2164a101aadedec4f1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--B68SHOYL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wjjtclqlnm2kzb0tgjvw.png)

### Logux 服务器

Logux 是暴露 web 套接字端口的服务器，保持与客户机浏览器的双向连接。为了执行业务逻辑，它有两种方式与后端进行 HTTP 集成。它允许我们将所有的业务逻辑保存在 Rails-backend 中，并通过 HTTP 点击 Logux 从后端发回通知。

[![Logux-backend integration](img/221e16a31f2e4133edc5fb7eff759fdb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--g3vz7uwI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6alt0cl5v8imhd7potfs.png)

### 【链接缩短器】

link shortener 是一个特定的 web 服务，用 Golang 编写。它的目标是缩短链接，扩展链接，并管理链接扩展的总体统计数据。
[![Link shortener server integration with backend](img/50ceec156c37f98c540ae6ab3df33983.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--9Uua15Fc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7zrnonica8z9dpu6fo2y.png)

### 【预览】服务

预览是公共服务，用于从客户端浏览器呈现任何链接的 OpenGraph 表示。它只有公共 http 端点。

### 其他组件

Shortener -是一个独立的服务，用于缩短 url 并保存关于链接扩展的分析数据。它是用 Golang 写的。它有外部公共端点来扩展缩短的链接，内部端点来缩短链接，同时在后端的后台作业中发布社交内容。

以及一些其他内部服务，如 telegram 和 facebook bot，它们只有后端集成。

## 组件依赖关系

大多数组件本身是复杂的 web 服务，依赖于底层组件，如 Postgres、Redis 和其他服务低级系统服务。
[![Internals of the backend component and how we are going to dockerize it](img/17ce83d180f3f2e21d40bc68275fe6c4.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--AIQEeykP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jsiwcvh1dls5wjh6tx06.png)

### 容器化

💡我们将使用 [Docker Compose](https://docs.docker.com/compose/) 分别封装每个服务。这是一个定义和运行多容器 Docker 应用程序的工具，只需一个命令就可以轻松启动所有的服务:

```
docker-compose up 
```

Enter fullscreen mode Exit fullscreen mode

💡为了使服务集成，我们将使用 [docker 网络](https://docs.docker.com/network/)，它允许任何 docker 容器相互通信。为简单起见，我们将只对所有组件使用一个`internal` docker 网络。更准确地说，读者将能够为每一个服务依赖者和每一组连接建立单独的网络。

[![](img/b33099492365442ccbbccef3c77b0739.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SZh8WON6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k9cq82dh8p3vcf5yo4il.png)

### Dockerize Ruby 后端

这里我们有标准的堆栈:Postgres、Redis、Rails web 服务器和 Sidekiq 后台。对于所有这些，我们将在`docker-compose.yaml`中定义服务。

以下是关键点:

*   对于 Postgres 和 Redis，我们将定义[持久卷](https://docs.docker.com/storage/volumes/)来保存运行之间的数据
*   我们不打算将 Ruby 源代码复制到容器中，相反，我们将把 Rails 应用程序源代码挂载到`/app`文件夹中
*   我们还将为包和其他东西定义持久存储，以便在下次启动时增加
*   我们将定义`amplifr_internal`网络并将交互容器添加到该网络中
*   应用程序应该准备好配置环境变量，我们将在 docker-compose 文件中设置这些变量
*   我们将在 YAML 文件中定义基础应用程序服务，然后将使用 YAML 语法中的锚和别名，这里不再重复。

❗Keep 请记住，这种配置不同于为生产构建 docker 映像的方式，在这种方式中，所有源代码和所有依赖包都被复制到 docker 映像中，以使其完全满足需求，并且没有外部依赖！

这里是完整的[要点和所有的配置](https://gist.github.com/dsalahutdinov/2d8975347ec79e94e7674dd0d9d4c518)，但让我注意要点:

### 形容基-务从中继承

```
services:
  app: &app
    build:
      context: .
      dockerfile: Dockerfile.dev
      args:
        PG_VERSION: '9.6'
    image: amplifr-dev:0.1.0
    volumes:
      - .:/app:cached
      - bundle:/bundle
    environment:
      # environment settings
      - BUNDLE_PATH=/bundle
      - BUNDLE_CONFIG=/app/.bundle/config
      - RAILS_ENV=${RAILS_ENV:-development}

      - DATABASE_URL=postgresql://postgres@postgres/amplifr_${RAILS_ENV}
      - REDIS_URL=redis://redis:6379/

      # service integrations
      - FRONTEND_URL=https://frontend-server:3001/
      - LOGUX_URL=http://logux-server:31338
    depends_on:
      - postgres
      - redis
    tmpfs:
      - /tmp 
```

Enter fullscreen mode Exit fullscreen mode

基本服务的容器将从带有参数的`Dockerfile.dev`开始构建 Postgres 版本。所有其他基于 Ruby 的图像都将继承这个基础。下面是服务继承示意图:
[![Service inheritance](img/2c7e3f5ae0a72d3116c7a932491a4394.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sPsHqHzL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ww54ehqf8cwua1fnasre.png)

我们还定义了当前文件夹到容器的`/app`目录的映射，并为包挂载 docker 卷。它防止每次依赖安装。

我们还定义了两组环境变量:
1) `system`变量，如`BUNDLE_PATH`、`REDIS_URL`和`DATABASE_URL`URL。
2)用于集成的依赖服务内部 URL:
`FRONTEND_URL`——是前端服务器获取支持的 browserslist 的内部端点。
`LOGUX_URL` -是内部 Logux HTTP 端点，用于从 Rails-app 向 Logux 发送动作。

### 形容‘奔跑者’

runner 服务用于运行维护命令，比如 rake 任务，或者 Rails 环境中的生成器。它是面向控制台的服务，所以我们必须设置`stdin_open`和`tty`选项，它们对应于 docker 的`-i`和`--t`选项，并为容器启动启用 bash shell:

```
services:
  runner:
    <<: *backend
    stdin_open: true
    tty: true
    command: /bin/bash 
```

Enter fullscreen mode Exit fullscreen mode

我们可以这样使用:

```
docker-compose run runner bundle exec rake db:create

# or run container and any command within the container
docker-compose run runner 
```

Enter fullscreen mode Exit fullscreen mode

### 构成服务器

定义网络服务器。这里的关键点是，我们定义了额外的 docker 网络`internal`，并向其添加了 web 服务器，为该网络中的容器主机提供了`backend-server`别名。因此 web 容器将可以使用`backend-server`网络名称进行访问。

```
services:
  server:
    <<: *app
    command: bundle exec thin start
    networks:
      default:
      internal:
        aliases:
          - backend-server
    ports:
      - '3000:3000'

networks:
  internal: 
```

Enter fullscreen mode Exit fullscreen mode

### 撰写 Sidekiq

很简单，它只是运行 sidekiq 并继承基本服务:

```
services:
  sidekiq:
    <<: *app
    command: sidekiq 
```

Enter fullscreen mode Exit fullscreen mode

### 作文《论文集》

```
 postgres:
    image: postgres:9.6
    volumes:
      - postgres:/var/lib/postgresql/data
    ports:
      - 5432

  redis:
    image: redis:3.2-alpine
    volumes:
      - redis:/data
    ports:
      - 6379

volumes:
  postgres:
  redis: 
```

Enter fullscreen mode Exit fullscreen mode

这里的要点是，我们为存储数据的容器路径挂载卷。它在两次运行之间保存数据。

### Dockerfile

我们不会深入去写`Dockefile`。你可以在这里找到它[。请注意，它从标准的 ruby 映像继承了一些必需的组件，如 Postgresql client 和其他一些二进制文件来构建这个包。](https://gist.github.com/dsalahutdinov/2d8975347ec79e94e7674dd0d9d4c518)

### 用法

用法很简单:

```
docker-compose run runner ./bin/setup # runs the bin/setup in docker
docker-compose run runner bundle exec rake db:drop # runs rake task
docker-compose up server # get the web-server running
docker-compose up -d # runs all the services (web, sidekiq)
docker-compose up rails db # runs the postgres client 
```

Enter fullscreen mode Exit fullscreen mode

Docker Compose 还允许指定服务依赖关系，并在运行的服务需要时启动依赖服务，例如，Sidekiq 需要 Redis 和 Postgres 服务才能正常工作，这就是为什么我们在服务的`depends_on`部分定义它们。

这里是服务依赖关系图，显示了服务是如何运行的:
[![Service startup sequence in case of depencencies](img/9434667a36b11ad28a96d60a619e2ddb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--f0RU0nwh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0rx3p8a5v84e6yl4eztj.png)

### 总结

我们让 Rails 应用程序在本地运行进行开发。它的工作方式与本地相同:持久化数据库，运行 rake 任务。像`rails db`、`rails c`这样的命令在容器中也能很好地工作。

主要优点是，我们可以通过更改一行来轻松更改 Postgres 版本或 Ruby 版本，然后重建映像并尝试在新环境中运行。

## Dockerize Node.js(前端服务器)

这里的主要要点是:

*   使用基本官方`node` docker 图像，无需任何调整
*   将`server`服务添加到`amplifr_internal`网络
*   定义`BACKEND_URL`环境变量来映射到后端服务的内部 docker 路径。
*   挂载 Node.js 模块安装路径的`mode_modules`卷

```
version: '3.4'

services:
  app: &app
    image: node:11
    working_dir: /app
    environment:
      - NODE_ENV=development
      - BACKEND_URL=http://backend-server:3000
    volumes:
      - .:/app:cached
      - node_modules:/app/node_modules

  runner:
    <<: *app
    command: /bin/bash
    stdin_open: true
    tty: true

  server:
    <<: *app
    command: bash -c "yarn cache clean && yarn install && yarn start"
    networks:
      default:
      amplifr_internal:
        aliases:
          - frontend-server
    ports:
      - "3001:3001"

networks:
  amplifr_internal:
    external: true

volumes:
  node_modules: 
```

Enter fullscreen mode Exit fullscreen mode

### 用法

前端服务器现在很容易启动，通过运行:

```
docker-compose up server 
```

Enter fullscreen mode Exit fullscreen mode

但是它需要后端先启动，因为前端服务指的是`internal`网络，它在启动后端的同时启动。

### 对接 Logux 服务器

在任何简单的情况下，Logux 服务器具有任何数据库依赖性，并且可以以与前端相同的方式配置。唯一不同的是，Logux 服务有它的环境变量，用来设置与集成服务的交互。

```
docker-compose up server # runs the server 
```

Enter fullscreen mode Exit fullscreen mode

## Dockerizing Golang(链接缩短 web 服务)

主要思想也是一样的:

*   使用`Golang`设置 docker 镜像，在那里安装应用程序源代码，并使用`go run`解释器运行它。
*   与 docker networks 共享服务，以便与 Ruby 后端集成

我们的 web 服务有 Postgres 和 Redis 依赖项。让我们从`Dockerfile`开始描述，整体配置样本可以在[这里找到](https://gist.github.com/dsalahutdinov/47487f319eeaa12cb2026daf9c06f8f0) :

```
FROM golang:1.11

ARG MIGRATE_VERSION=4.0.2

# install postgres client for local development
RUN apt-get update && apt-get install -y postgresql-client

# install dep tool to ensuring dependencies
RUN go get -u github.com/golang/dep/cmd/dep

# install migrate cli for running database migrations
ADD https://github.com/golang-migrate/migrate/releases/download/v${MIGRATE_VERSION}/migrate.linux-amd64.tar.gz /tmp
RUN tar -xzf /tmp/migrate.linux-amd64.tar.gz -C /usr/local/bin && mv /usr/local/bin/migrate.linux-amd64 /usr/local/bin/migrate

ENV APP ${GOPATH}/src/github.com/evilmartians/ampgs
WORKDIR ${APP} 
```

Enter fullscreen mode Exit fullscreen mode

这里有几个有趣的细节:

*   我们为本地开发映像安装 postgres-client。它简化了对数据库的访问，只要你需要:`docker-compose run runner "psql $DATABASE_URL"`。同样，我们在 Ruby 后端码头化
*   我们安装`dep`工具来安装并确保所有的依赖:`docker-compose run runner dep ensure`
*   我们将迁移工具安装到映像，以允许直接从 docker 容器进行数据库迁移:`docker-compose run runner "migrate -source file://migrations/ -database ${DATABASE_URL} up"`

‼️那些工具中的大部分我们不需要用于生产环境的 docker 镜像，因为它将只包含编译后的二进制文件。

我们将使用与 Ruby 服务相同的方式对 Golang 服务进行 dockerizing:

*   提取基本`app`服务和特殊`runner`服务来运行维护任务
*   添加具有持久数据卷的 Postgres 和 Redis 依赖项

以下是`docker-compose.yml`文件的重要部分:

```
services:
  # base service definition
  app: &app
    image: ampgs:0.3.1-development
    build:
      context: .
      dockerfile: docker/development/Dockerfile
    environment:
      REDIS_URL: redis://redis:6379/6
      DATABASE_URL: postgres://postgres:postgres@postgres:5432/ampgs
    volumes:
      - .:/go/src/github.com/evilmartians/ampgs
    depends_on:
      - redis
      - postgres

  runner:
    <<: *app

  web:
    <<: *app
    command: "go  run  ampgs.go"
    ports:
      - '8000:8000'
    networks:
      default:
      amplifr_internal:
        aliases:
          - ampgs-server 
```

Enter fullscreen mode Exit fullscreen mode

# 总结起来

Docker-compose 是简化复杂服务管理的强大工具。
让我回顾一下使用 docker compose 时本地开发 docker 化的主要原则:

*   **将源代码作为文件夹挂载到容器中，而不是用源代码的副本重新构建 docker** 镜像。这对每次本地重启都有很大帮助
*   使用 docker 网络来处理服务之间的通信。这有助于一起测试所有的服务，但是要将它们的环境分开。
*   **服务通过使用`docker-compose`向 docker 容器提供环境变量来相互了解**

就是这样。感谢阅读！
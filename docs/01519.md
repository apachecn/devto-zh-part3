# 使用 docker-compose 缓存 Rails gems

> 原文：<https://dev.to/k_penguin_sato/cache-rails-gems-using-docker-compose-3o3f>

# 简介

让我们假设你有一个像下面这样的`docker-compose.yml`。
每次在你的`Gemfile`上添加/移除宝石时，你都必须运行`docker-compose build`，这非常耗时。
如果我能运行`bundle install`并继续开发应用程序，那就太好了。
嗯...我将在这篇文章中向你展示这一点。

```
version: "3"
services:
  app:
    build: .
    volumes:
      - .:/app
    depends_on:
      - postgres
  postgres:
    image: postgres
    ports:
      - "5432:5432"
    volumes:
      - postgres-data:/var/lib/postgresql/data

volumes:
  postgres-data: 
```

# 设置码头-堆肥. yml

请遵循以下步骤。

*   (1)使用`environment`设置`BUNDLE_PATH`。
*   (2)设置命名卷`bundle_path`并将其添加到顶层`volumes`。

下面是修改后的`docker-compose.yml`。

```
version: "3"
services:
  app:
    build: .
    volumes:
      - .:/app
      - bundle_path:/bundle # New
    environment:
      - BUNDLE_PATH=/bundle/vendor #New
    depends_on:
      - postgres
  postgres:
    image: postgres
    ports:
      - "5432:5432"
    volumes:
      - postgres-data:/var/lib/postgresql/data

volumes:
  bundle_path: # New
  postgres-data: 
```

# 怎么用？

## 创建宝石的缓存

修改完`docker-compose.yml`后，运行下面的命令来创建宝石缓存。

```
$ docker-compose app bundle install 
```

## 添加或删除宝石

*   (1)停止正在运行的容器。

```
$ docker-compose down 
```

*   (2)添加一个新的 gem 并运行下面的命令。

```
$ docker-compose run app bundle install 
```

*   (3)让容器再次运行。

```
$ docker-compose up 
```
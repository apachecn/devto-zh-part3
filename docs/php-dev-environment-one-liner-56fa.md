# PHP 开发环境单行程序

> 原文：<https://dev.to/tonymet/php-dev-environment-one-liner-56fa>

这是让你的 PHP 应用程序运行的最快方法。没有 MAMP，WAMP，阿帕奇或任何废话。

此外，它允许您独立运行多个项目。

我猜你有多克。

### TL；博士；医生

这会运行 php docker 映像，挂载当前目录，并在端口 8086
上启动服务器

```
$ docker run -v $(pwd):/www -it -p8086:8086  php:5.6-alpine sh -c "cd www; php -S 0.0.0.0:8086" 
```

### 完整版

#### 创造你的 index.php

```
$ cat > index.php
<html><body><h1><?php print("Hello World!") ?> </h1></body></html>
CTRL-D 
```

#### 运行服务器

```
$ docker run -v $(pwd):/www -it -p8086:8086  php:5.6-alpine sh -c "cd www; php -S 0.0.0.0:8086" 
```

#### 测试你的服务器

```
$ curl localhost:8086
<html><body><h1>Hello World! </h1></body></html> 
```
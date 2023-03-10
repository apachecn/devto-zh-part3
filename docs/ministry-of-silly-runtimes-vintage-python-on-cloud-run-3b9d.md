# 愚蠢运行时部:云上运行的复古 Python

> 原文：<https://dev.to/googlecloud/ministry-of-silly-runtimes-vintage-python-on-cloud-run-3b9d>

## 引入云运行

今天，谷歌宣布推出 [Cloud Run](http://cloud.google.com/run) ，这是一款非常简洁的产品:

1)你写一个服务/应用/函数/任何东西来监听`$PORT`上的 HTTP 请求；
2)你写一个`Dockerfile`从任何基础镜像继承，安装任何必要的依赖，启动你的服务；
3)您将映像部署到云运行，它可以按需扩展(甚至扩展到零)。

基本上，它是“无服务器 Docker 容器即服务”,这正是我认为 PaaS/FaaS 开发社区现在可以使用的，原因如下:

**最惯用的运行时可能**
这里没有特定于供应商的怪异之处。真的，没有比使用`FROM python:3.7`或者任何你想使用的其他基本图像更习惯的了。

**无需为闲置虚拟机付费的可定制性**
如果您的特定工作负载不符合通常提供的最“常见”运行时的范围，那么在此之前，您的最佳选择是启动一个定制虚拟机，该虚拟机不会扩展到零，并且会花费大量时间处于闲置状态。有了 Cloud Run，你可以使用`Dockerfile`随心所欲地定制你的运行时。

**容器化意味着可移植性**
用标准定义你的运行时`Dockerfile`意味着你可以把它带到任何地方——*甚至本地*——这意味着你不会被某个特定的供应商所束缚，你的应用程序的本地开发是内置的，不需要做任何特殊的事情。

这些都很棒，它们肯定会帮助开发人员做伟大的事情...

...但是如果你想用它做一些真正愚蠢的事情呢？

## 推出首个 Python 1.x 无服务器运行时

当我第一次开始玩 Cloud Run 时，我真的想测试一下我可以用它做什么的极限。我真的可以运行我想要的任何运行时吗？我可以运行一个超现代的 Python 3.8.0a3 alpha 版本吗？(是)。那么，Python 的“复古”版本怎么样呢？我可以将运行在 Python 1.x 上的“Hello World”应用程序部署到 Cloud run 吗？🤔

#### 搜索“复古”图片

第一个问题是为 Python 1.x 找到一个基础图像。官方 Python Docker images 只发布 2.7 及以上版本的图像，所以运气不好。我在其他地方找不到它，所以看起来我不得不自己从头开始构建基础映像。

#### 搜索“年份”来源

从头构建基础映像意味着我必须:

a)找到 Python 1.x 版本的旧源代码；b)让它在现代发行版的基础映像上编译；发布这个新的基础映像。

找到源头有点困难。现代 Python 版本的源代码发表在[https://www.python.org/downloads/source/](https://www.python.org/downloads/source/)上，但这只能追溯到 2001 年 6 月 22 日发布的 Python 2.0.1。

幸运的是，我找到了[https://legacy.python.org/download/releases/src/](https://legacy.python.org/download/releases/src/)，其中包含了远至 Python 1.0.1 的源代码版本，该版本发布于 25 年前的 1994 年 2 月 15 日。

我决定尝试以最新的 Ubuntu 发行版为目标，令人惊讶的是，只需一些小补丁，我就能让 Python 1.0.1、1.1、1.2 和 1.3 在 Ubuntu 18.04 下编译。

#### 发布一些“老古董”图片

我在 [`vintage-python`](https://hub.docker.com/r/dustingram/vintage-python) 库下发布了这些结果构建的 Docker 图像，所以这意味着你现在可以随时启动一个老式的 Python REPL:

```
$ docker run -it dustingram/vintage-python:1.0.1
Python 1.0.1 (Feb 23 2019)
Copyright 1991-1994 Stichting Mathematisch Centrum, Amsterdam
>>> print "Hello world!"
Hello world! 
```

这也意味着我可以在我的`Dockerfile`中做`FROM dustingram/vintage-python:1.0.1`，这是向拥有我愚蠢的运行时迈出的一大步。

#### 搜索 HTTP 服务器

我一心想要 Python 1.0.1，但是在意识到 Python 直到 Python 1.3 才附带 HTTP 服务器之后，我决定使用它，而不是试图将 HTTP 服务器移植到旧版本。

在 Python 1.3 中，我可以这样写:

```
import sys
from BaseHTTPServer import HTTPServer
from SimpleHTTPServer import SimpleHTTPRequestHandler

class MyHandler(SimpleHTTPRequestHandler):
    protocol_version = "HTTP/1.1"

    def do_GET(self):
        self.send_response(200)
        self.send_header("Content-type", "text/html")
        self.end_headers()
        self.wfile.write("Hello from Python " + sys.version)

if __name__ == '__main__':
    print('Running...')
    HTTPServer(('0.0.0.0', 8080), MyHandler).serve_forever() 
```

这将在端口 8080 上设置一个基本的 HTTP/1.1 服务器，用给定的消息响应每个请求。我*真的*想要证明这是一个服务于请求的 Python 的经典版本，所以我在响应中包含了`sys.version`。

#### 将一些复古蟒蛇装箱

在将其保存为`app.py`之后，剩下要做的就是写入`Dockerfile`。因为我的应用程序没有任何依赖关系，所以云运行快速入门中的`Dockerfile`非常接近[的`Dockerfile`](https://cloud.google.com/run/docs/quickstarts/build-and-deploy)

```
FROM dustingram/vintage-python:1.3

# Copy local code to the container image.
ENV APP_HOME /app
WORKDIR $APP_HOME
COPY . .

# Service must listen to $PORT environment variable.
# This default value facilitates local development.
ENV PORT 8080

# Run the web service on container startup.
CMD ["python", "app.py"] 
```

在这里，我们从我的 vintage Python 映像继承，将我的`app.py`复制到容器中，设置`PORT`环境变量(用于本地开发)，然后通过运行`python app.py`启动服务器。

#### 部署一些老式蟒蛇

对于云运行，有一个两步部署:

使用云构建构建并提交您的映像:

```
$ gcloud builds submit --tag gcr.io/my_project_id/helloworld 
```

然后，将提交的映像部署到云运行:

```
$ gcloud beta run deploy --image gcr.io/my_project_id/helloworld 
```

然后就展开了！

请看这里:[https://helloworld-bpzdnjbrsq-uc.a.run.app/](https://helloworld-bpzdnjbrsq-uc.a.run.app/)

## 结论

Cloud Run 绝对足够灵活，可以运行我可以使用的任何运行时——甚至是 Python 最古老的版本之一。显然，我不会鼓励任何人在生产中使用 Python 1.x:任何超过其 EOL 的版本本质上都是不可维护的，并且功能严重受限。

从这里，您可以:

*   关于运行时的更多细节，请参见[`vintage-python`Github repo](https://github.com/di/vintage-python)；
*   在你自己愚蠢的项目中使用 [`vintage-python`图片](https://hub.docker.com/r/dustingram/vintage-python)；
*   参见[云运行快速入门](https://cloud.google.com/run/docs/quickstarts/build-and-deploy)；
*   在 Twitter 上关注我。
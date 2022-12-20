# 使用 Docker 多阶段构建服务于 Gatsby 站点

> 原文：<https://dev.to/nikoheikkila/serving-gatsby-site-with-docker-multi-stage-build-57ep>

开发和部署静态站点可以通过多种技术来管理。如果 Docker 已经是你工具的一部分，你可以把你的内容作为一个容器放到世界上任何一个平台上，不管是 T2 的 Kubernetes T3、T4 的 open shift T5 还是你朋友的笔记本电脑。

在本教程中，我们将使用一个流行的静态站点生成器 *Gatsby* 构建一个多阶段 Docker 构建。下面的方法可以用于几乎任何类型的网站，这些网站需要构建自己的资产，并作为简单的 HTML、JS 和 CSS 文件。

我们将使用继承的 Docker 图像的 Alpine 变体。根据静态内容的数量，这将我们的最终图像大小降低到 50 MB 以下。谈效率！

在继续之前，检查并克隆我的[网站库](https://github.com/nikoheikkila/nikoheikkila.fi),我将用它作为这篇文章的例子。构建和运行 Docker 映像的知识也需要遵循。

## 第一阶段:用 Node 构建盖茨比站点

通过安装依赖项并发出一个`gatsby build`命令，构建一个 Gatsby 站点相对简单。我使用 Yarn 作为依赖管理器，并且我已经设置了前面提到的构建脚本来运行`yarn build`。

通过使用多阶段构建，我们不必担心构建过程中的遗留问题，因为只有生成的内容会被保留。这使得我们的图像更薄，更安全。下面你可以找到我们的建设的第一阶段。让我们一行一行地过一遍。

```
FROM node:11-alpine AS build

RUN apk add --no-cache --virtual .gyp python make g++

WORKDIR /app
ENV NODE_ENV=production

COPY package.json yarn.lock ./
RUN yarn --frozen-lockfile --non-interactive

COPY . .
RUN yarn build 
```

Enter fullscreen mode Exit fullscreen mode

我们首先使用 Node.js 版本 11 的官方 Alpine Linux 映像，标记为`node:11-alpine`。作为参考，我们称之为`build`。

然后我们用 Alpine 自己的包管理器`apk`安装所需的操作系统包。由于我们的构建过程在编译期间依赖于`node-gyp`,如果您想知道为什么我们要将 Python 安装到节点容器中，我们需要准备好这些。

然后，我们将当前工作目录设置为`/app`，在这个阶段的剩余时间里，我们的应用程序将驻留在这里。这可以是我们想要的任何东西，但是我已经为自己制定了一个惯例，总是在容器中使用尽可能简单的文件夹结构。一般来说，`/app`或者`/data`都是不错的选择。

然后，我们通过导出一个环境变量`NODE_ENV=production`，告诉 Node 在一个特殊的*生产*模式下工作。我们希望确保 Gatsby 尽可能地优化资产，以确保最大的站点性能，而这在开发模式中不会发生。每当您为生产构建节点应用程序时，使用它也是一个好主意。

然后，我们将依赖文件`package.json`和`yarn.lock`复制到我们的容器中，并安装它们需要的东西。例如，这利用了 Docker 的层缓存，所以我们不必在每次编辑博客文章时都建立依赖关系。这是一个很大的帮助，因为由于我们的构建中使用了大量的 Gatsby 插件，安装节点包可能需要 2 分钟。

最后，其余的文件被复制过来，我们告诉盖茨比用这些资源建立一个网站。默认情况下，Gatsby 将这些输出到项目根目录下一个名为`public`的文件夹中，我们将在下一阶段使用这个文件夹。

如果您愿意，可以在这里停下来启动容器。应该准备好一个装满编译内容的文件夹。

## 第二阶段:服务现场

下面是我们构建的第二阶段。再一次，让我们从头到尾过一遍。

```
FROM nginx:alpine

COPY nginx /etc/nginx/
COPY --from=build --chown=nginx:nginx /app/public /usr/share/nginx/html
RUN touch /var/run/nginx.pid && chown nginx:nginx /var/run/nginx.pid

USER nginx

EXPOSE 8080
HEALTHCHECK CMD ["wget", "-q", "localhost:8080"] 
```

Enter fullscreen mode Exit fullscreen mode

我们希望用 nginx web 服务器来服务已建成的网站，幸运的是，我们也可以使用它的 Alpine 版本。这一次我们不需要为这个阶段指定名称，因为这将是最后一个阶段。

我已经通过删除`user nginx;`行修改了默认的 nginx 配置。你可以从[这里](https://github.com/nikoheikkila/nikoheikkila.fi/blob/master/nginx/nginx.conf)复制过来。不像人们猜测的那样，当我们出于安全目的以非 root 用户身份运行容器时，不需要这一行。在[实际站点配置](https://github.com/nikoheikkila/nikoheikkila.fi/blob/master/nginx/conf.d/default.conf)中，我为该站点分配了 TCP 端口 8080，因为非 root 用户不能绑定到 1024 以下的端口。在您的主机上，您可以绑定到任何公开的容器端口，所以这并不重要。

接下来，我们将见证多阶段构建的美妙之处。在这里，我们指示 Docker 的`COPY`命令从前面的阶段复制文件，并立即将它们的所有权更改为我们的自定义用户，而无需运行任何额外的脚本。是不是很酷？确保安装一个足够新版本的 Docker 来实现这个功能。

作为非 root 用户运行容器有一个小小的警告:我们需要确保这个用户有权限使用处理正在运行的 nginx 进程的`/var/run/nginx.pid`。否则，容器将无法启动。幸运的是，这只是一个单行的变通方法，不会给我们的构建带来太多的开销。

在这之后，我们告诉 Docker 镜像以包含在官方镜像中的`nginx`用户的身份运行所有的 rest 命令。当然，您可以在这里创建另一个用户，并为其设置文件的所有权，但我选择了更简单的方法。文件夹`/usr/share/nginx/html`是在我们的配置中定义的 nginx 站点的标准目录，并且简单易记。

最后，我们公开端口 8080，供主机绑定并定义一个定制的健康检查命令。对于 Docker 映像来说，健康检查是完全可选的，但是在有几十个容器化服务运行的环境中，健康检查变得非常方便。这里我们告诉健康检查定期运行 shell 命令`wget -q localhost:8080`来获取我们的主页。如果这个命令失败，我们的容器就会死亡，或者根据重启策略，它将被重启。由于 nginx 服务器进程作为主进程(PID 1)运行，因此其中的任何崩溃也会导致我们的容器关闭。这里的 Healthcheck 将确保如果我们的网站因为这样或那样的原因而锁定，也会发生同样的情况。

注意，我们不需要发出 Docker `CMD`命令来启动 nginx，因为它将从基本映像继承而来。

## 组合在一起

我们现在可以构建 Docker 映像，并使用标准的 Docker CLI 从我们的终端运行它。然而，我发现使用 Docker Compose 更令人愉快，即使是像这样的单一服务项目。让我们像这样填充我们的`docker-compose.yml`文件:

```
version: '3'

services:
    web:
        build: '.'
        image: 'nikoheikkila/nikoheikkila.fi'
        container_name: 'web-nginx'
        restart: 'unless-stopped'
        ports:
            - '8080:8080' 
```

Enter fullscreen mode Exit fullscreen mode

这里没什么特别的。我们声明了一个名为`web`的服务，它在我们的项目根中构建了`Dockerfile`,并用一个自定义名称标记它。`unless-stopped`的重启策略将继续重启我们的容器，除非我们明确地暂停它。作为最后一条规则，我们将主机的端口 8080 绑定到容器中公开的同一个端口。

现在我们来运行`docker-compose up --build -d`。在镜像成功构建并且容器运行之后(用`docker-compose ps`检查)，我们应该能够用我们的浏览器访问 [http://localhost:8080](http://localhost:8080) ，这就是我们的站点所在。

## Docker 值得吗

它是，然而它不是；这取决于你。如果您真的要将它部署到生产环境中，您需要将大量 SSL 证书安装到容器中，因为即使是静态站点也不应该在未加密的情况下漫游互联网。这在今天不是一个大问题，因为有了 Let's Encrypt 和 CloudFlare，保护您站点的整个过程变得非常简单。将您自己的证书挂载到容器超出了本教程的范围，但是在互联网上有很多很好的资源。

使用 Docker，您可以完全控制您的运行环境，并且只需做一点点工作就可以将它部署到任何地方。另一方面，我现在使用**ZEIT**进行部署，因为它可以用[一个单一的 JSON 文件](https://github.com/nikoheikkila/nikoheikkila.fi/blob/master/now.json)进行配置，你将立即获得一个免费的 SSL 证书和与 GitHub 或 GitLab 集成的持续部署。代价是除了运行环境之外，你对它一无所知。

作为第三种选择，如果你喜欢用更老派的方式使用 Linux 栈，你可以有一个配置了 nginx 或 Apache 的私有服务器，然后把内容放在那里。对于来自数字海洋的低端液滴，这将是一个非常实惠的解决方案。

天底下的事情不用 Docker 绝对没问题。事实上，今天*无服务器*和 *FaaS* 已经取代了许多 Docker 的用例。即使没有 Docker，持续的、不可变的、极其简单的云部署也非常容易实现，静态呈现的站点是它们的完美用例。
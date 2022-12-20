# 为 Raspberry Pi Zero 构建节点 Docker 映像

> 原文：<https://dev.to/joenas/building-node-docker-images-for--raspberry-pi-zero-2hfm>

在为我的 [Raspberry Pi Zero SMS 网关](https://dev.to/joenas/raspberry-pi-zero-as-sms-gateway-4ihl-temp-slug-4032545)开发 web 应用程序时，我遇到了麻烦。我希望能够在 Docker 中运行该应用程序，但在 RPi0 上从 Docker 文件构建图像非常慢，我没能完成，因为我的 Pi 耗尽了内存。

我想过在本地创建图像，并将其推送到 Docker Hub，但对于小型的个人应用程序来说，这感觉有点大材小用。经过一些思考和谷歌搜索，我意识到你可以[保存](https://docs.docker.com/engine/reference/commandline/save/) docker 图片，然后[将它们加载到另一台电脑上。](https://docs.docker.com/engine/reference/commandline/load/)

有两种方法可以解决这个问题:

1.  在你的计算机上安装`npm`，获取所有的依赖项(`node_modules`，并将它们和代码一起复制到`armv6`映像中。
2.  在具有[多级构建](https://docs.docker.com/develop/develop-images/multistage-build/)的容器中运行`npm`，然后将文件复制到映像。

### 同当地的 NPM

这对于你自己开发的应用程序来说更有用也更快，因为你可能已经安装了`npm`。

像往常一样用`npm install`获取 dep，然后添加一个`Dockerfile`(可能还有一个`.dockerignore`)。

```
# Check for your version: https://hub.docker.com/r/arm32v6/node/tags/
FROM arm32v6/node:8.14.0-alpine
RUN mkdir /app
WORKDIR /app
ENV PATH /app/node_modules/.bin:$PATH

# Either be specific with what to add, docker caches every step
ADD package.json /app/package.json
ADD package-lock.json /app/package-lock.json
ADD node_modules /app/node_modules
ADD app.js /app/app.js

# Or add the whole dir, I always use a .dockerignore file when doing that
ADD . /app

ENV PORT=5000
EXPOSE 5000
CMD ["npm", "start"] 
```

### 多级构建

当你想制作别人项目的图像和/或不想在你的机器上安装`npm`时，这很有用。

由于您不能在您的计算机上运行`arm32v6/node`，您需要首先使用一个工作节点映像来获取/构建您的项目。

```
# Fetch node_modules for backend, nothing here except 
# the node_modules dir ends up in the final image
FROM node:8.14.0-alpine as builder
RUN mkdir /app
WORKDIR /app
ENV PATH /app/node_modules/.bin:$PATH
COPY package.json /app/package.json
RUN npm install

# Add the files to arm image
FROM arm32v6/node:8.14.0-alpine
RUN mkdir /app
WORKDIR /app
ENV PATH /app/node_modules/.bin:$PATH

# Same as earlier, be specific or copy everything
ADD package.json /app/package.json
ADD package-lock.json /app/package-lock.json
ADD . /app

COPY --from=builder /app/node_modules /app/node_modules

ENV PORT=5000
EXPOSE 5000
CMD ["npm", "start"] 
```

### 建筑

然后，您可以构建并保存您的映像...

```
docker build -t my-app -f Dockerfile .
docker save -o my-app.tar my-app
ls
# my-app.tar 
```

...把它传输到你的 RPi(用`scp`或者别的什么)并加载它！

```
# On RPi
docker load -i my-app.tar
docker run --rm -p 5000:5000 my-app 
```

## 自动化

如果你对你的应用程序做了很多改动，这样做可能会很乏味，所以我创建了一个简单的 bash 脚本供你使用:[https://github.com/joenas/node-docker-raspberry-zero](https://github.com/joenas/node-docker-raspberry-zero)

**注意**:在我的 repo 中，我已经将文件命名为`Dockerfile.armv6`，因为我已经有了一个`Dockerfile`用于在不需要特定 docker 映像的其他架构上运行应用程序。我还用后缀`:armv6`标记了这张图片。上面的命令应该是这样的:

```
# Build & save
docker build -t my-app:armv6 -f Dockerfile.armv6 .
docker save -o my-app.tar my-app:armv6

# Load & run
docker load -i my-app.tar
docker run --rm -p 5000:5000 my-app:armv6 
```

### 样品。码头工人

这里有一个示例`.dockerignore`文件，您可以使用它来不包含您的图像中的所有文件。

```
/.git/
.gitignore

docker-compose.yml
Dockerfile*
LICENSE
README.md
*.tar 
```

## 分享是关爱

我希望你对此有用，我也希望看到更多 RPi0/1 的东西，所以如果你有所创造，请在下面评论！🙏
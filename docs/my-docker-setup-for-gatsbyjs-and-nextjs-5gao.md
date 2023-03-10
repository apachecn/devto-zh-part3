# 我的 GatsbyJS(和 NextJS)的 Docker 设置

> 原文：<https://dev.to/stoutlabs/my-docker-setup-for-gatsbyjs-and-nextjs-5gao>

更新:Alpine 做了一些影响这篇文章的改动，下面的一个有用的评论会澄清这个问题。谢谢乔纳森。我还修复了示例代码。

更新#2:在 Windows 中，热模块重载似乎与此相关。如果问题解决了，我会更新的！

更新#3:如果你仍然遇到问题，下面的另一个评论可能会让你回到正轨:[这里](https://dev.to/ivorsco77/comment/ia43)

## Docker 太棒了

在这一点上，我可能迟到了，但我最近开始使用 [Docker](https://www.docker.com/) 作为我开发工作流程的一部分。我绝对喜欢它，我觉得所有的开发人员至少应该在某个地方修补一下基础教程。相信我，这是一个值得探索的兔子洞！

## 但是...有一个小故障

本周，当我浏览我最近的一些 Gatsby 和 NextJS 项目来“整理”它们时，我注意到似乎没有太多的信息可以随时用来实现这一点。

盖茨比确实在他们的回购中提供了一个 Docker 设置(基于 alpine:edge ),但坦率地说:它似乎不必要的复杂和模糊，我无法让它为我的目的工作。(我认为它只是为了服务于一个站点，后期构建。)

因此，我决定弄清楚如何从官方节点创建一个自定义节点:alpine(最新)图像...只是一些用于快速开发环境设置的东西，不管我用的是哪台计算机。(如果我想在 Netlify 之外的地方部署，我可以快速添加一个 Nginx 映像/服务和一个超级基本配置文件。)

## 问题解决了！

经过几个小时的学习、研究和实验(可能还有一些挫折)，我向您展示了我的工作 docker 文件版本！它将创建一个全功能的 Docker 映像，在 Apline Linux(最新)中安装构建 Gatsby(和 NextJS)站点和应用程序所需的所有包。

(注意:对于 NextJS 站点，我只需将最终的 CMD 和端口更改为我用于站点/应用程序的任何内容...平时 8080。)

#### Dockerfile.dev

```
FROM node:alpine

# Also exposing VSCode debug ports
EXPOSE 8000 9929 9230

RUN \
  apk add --no-cache python make g++ && \
  apk add vips-dev fftw-dev --update-cache \
  --repository http://dl-3.alpinelinux.org/alpine/edge/community \
  --repository http://dl-3.alpinelinux.org/alpine/edge/main \
  && rm -fR /var/cache/apk/*

RUN npm install -g gatsby-cli

WORKDIR /app
COPY ./package.json .
RUN yarn install && yarn cache clean
COPY . .
CMD ["yarn", "develop", "-H", "0.0.0.0" ] 
```

Enter fullscreen mode Exit fullscreen mode

#### 码头工-化合物. yml

(注意`GATSBY_WEBPACK_PUBLICPATH`的设置——这似乎修复了我在编辑代码时遇到的任何 HMR 问题。)

```
version: '3'
services:
  web:
    build:
      context: .
      dockerfile: Dockerfile.dev
    ports:
      - "8000:8000"
      - "9929:9929"
      - "9230:9230"
    volumes:
      - /app/node_modules
      - .:/app
    environment:
      - NODE_ENV=development
      - GATSBY_WEBPACK_PUBLICPATH=/ 
```

Enter fullscreen mode Exit fullscreen mode

您可能还想将一个`.dockerignore`文件放入项目目录，以忽略诸如。/节点 _ 模块，。/cache，。/public，any。DS_Store 文件等。(很像一个`.gitignore`文件。)

要运行它，从项目的根目录运行:

```
$ docker-compose up --build 
```

Enter fullscreen mode Exit fullscreen mode

运行之后，我就可以开始工作了...无论我是在 Macbook Pro 上还是在 Windows 桌面上。希望这也能帮助你！
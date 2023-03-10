# 我为什么要关心 Docker？

> 原文：<https://dev.to/raddevon/why-should-i-care-about-docker-2fnp>

## Docker 是什么？

Docker 是一种新的虚拟化方法。如果您了解虚拟化，请随意跳过下一部分。如果没有，在我帮助您理解 Docker 之前，您需要对虚拟化有一个基本的了解。

### 什么是虚拟化？

先打个比喻:假设你拥有一栋房子。你有一个需要倾诉的朋友。如果你想帮助你的朋友，你有几个选择。

1.  把你的朋友搬进你的卧室。这可能会有点紧张。
2.  在你的土地上为你的朋友建一栋新房子。这是一个昂贵的解决方案。
3.  邀请你的朋友住在备用的卧室里。现在我们有所进展了…

第三种选择相当不错。你可以帮助你的朋友，而不用为他们建一栋新房子，但同时保持你们的生活基本上是分开的。你们将共享一些公共资源，如厨房和客厅，但你们可以各自进入自己的卧室，关上房门以保护隐私。

虚拟化就像把你的朋友安置在你的备用卧室里。假设你想在你的电脑上运行一个网络服务器。您希望将它与您自己的操作系统和应用程序分开。为此，您可以运行一个包含 web 服务器的**虚拟机**。它像独立的计算机一样运行，但它使用您计算机的处理器和内存。当您启动虚拟机时，它的整个操作系统会显示在操作系统的一个窗口中。

### Docker 有什么不同？

Docker 是实现虚拟化的一种不同方式。在典型的虚拟机将操作系统与您正在运行的应用程序打包在一起的情况下，Docker 会在您的虚拟化系统之间尽可能多地共享。这使得它们在运行时使用更少的资源，并使它们更容易被运送到其他开发者或你的生产环境中。

> 如果你正在自学 web 开发，很难知道下一步应该学什么。[在 Rad Devon](https://raddevon.com/) 注册一个免费的辅导课程，我们会帮你想出下一步如何过渡到你的 web 开发职业生涯！

## 开发者为什么要用 Docker？

Docker 给了网络开发者一些很酷的超能力。

### 轻松共享开发环境

如果你和我要合作开发一个 Node 应用程序，我们会希望确保我们都安装了 Node，并且**是相同的版本**，这样我们的环境就一致了。我们可以跳过这一步，抱最好的希望，但它*可能*给我们带来可能难以缩小范围的问题。库和我们自己的代码有时会在不同版本的 Node 上表现不同。

解决方案是确保我们都有相同版本的 Node，但是，如果我们每个人的系统上已经有其他项目需要其他版本的 Node，我们可能会希望安装 [NVM](https://github.com/creationix/nvm) ，这允许我们轻松地切换节点版本。然后，我们可以将一个. nvmrc 文件添加到项目的根目录，指定我们想要的公共版本。

我们只需要这样做一次，所以我们的工作现在已经完成了。总而言之，我们要做的是:

1.  决定节点版本。
2.  安装 NVM。
3.  安装我们选择的节点版本。
4.  将. nvmrc 添加到项目目录中，设置正确的节点版本。
5.  启动应用程序。

有用，但是很多。我们必须为任何想加入我们这个项目的人再做一次。即使我们采取了所有这些步骤，**我们仍然不能保证所有开发人员的环境都是一样的**。运行不同操作系统甚至同一操作系统的不同版本的开发人员之间可能会出现问题。

Docker 通过向所有开发人员提供相同的开发环境，让我们解决了所有这些问题。相反，使用 Docker，我们会这样做:

1.  安装 Docker。
2.  写一个 Dockerfile 文件。
3.  运行`docker build -t <image-name>`。图像名称可以是您选择的任何名称。
4.  运行`docker run -p 3000:3000 <image-name>`。“p”选项将容器端口映射到本地端口。这允许您点击计算机上的端口 3000，它将映射到容器上的端口 3000。使用与步骤 3 中相同的图像名称。

这看起来并不比节点/NVM 设置简单多少(事实上也不是)。不过，它确实有一个优势。不管你有多少技术，你只需要安装一次 Docker**。当然，您只需安装 Node 一次(除非您需要多个版本)，但是，当您准备好使用不同堆栈上的应用程序时，您需要安装该堆栈上所需的所有软件。有了 Docker，你只需要写一个不同的 Dockerfile(或者 [Docker Compose](https://docs.docker.com/compose/overview/) file，这取决于你应用的复杂程度)。**

 **Dockerfile 非常简单:它是一个名为“Dockerfile”的文本文件，没有扩展名。让我们来看一个 docker 文件，您可以将它用于一个简单的节点应用程序。

```
# This Docker image will be based on the Node 11.6 image
FROM node:11.6.0

# Install dependencies
COPY package*.json ./
RUN npm install

# Copy the node app from the host into the image at /app
COPY . /app

# Expose port 3000 and start the app
EXPOSE 3000
CMD npm start 
```

Enter fullscreen mode Exit fullscreen mode

这个 Dockerfile 是为一个监听端口 3000 并以`npm start`命令开始的节点应用程序编写的。将此提交到您的项目库中，新开发人员的加入变得非常容易，并且 100%一致:每个开发人员每次都获得相同的环境。

### 在与生产相同的环境下开发

一旦您的应用程序在 Docker 开发环境中启动并运行，您就可以将整个容器直接交付生产。如果你认为处理两个开发人员之间的不一致是一件痛苦的事情，那就等到你编写了在你的机器上运行的代码**之后，才让它*而不是*在生产中运行**。这是极其令人沮丧的。

对于将 Docker 容器部署到生产中，您有大量的选择。以下是一些例子:

*   [AWS ECS](https://aws.amazon.com/ecs/) ( [官方教程](https://aws.amazon.com/getting-started/tutorials/deploy-docker-containers/)
*   [数字海洋](https://www.digitalocean.com/products/one-click-apps/docker/) ( [教程](https://medium.com/@mannycodes/deploying-reactjs-with-docker-ac16728c0896))
*   [Heroku](https://www.heroku.com/deploy-with-docker) ( [官方教程](https://devcenter.heroku.com/articles/build-docker-images-heroku-yml))
*   [sloppy.io](https://sloppy.io/en/) ( [官方教程](https://kb.sloppy.io/getting-started/getting-started-with-the-cli-launch-your-first-dockerized-app/step-1-install-the-cli))

我喜欢 Heroku 的方法，因为这是唯一一个允许你简单地用 docker 文件推进你的项目来运行的方法。其他人则需要更多的步骤，比如将 Docker 映像推送到存储库中。额外的步骤不是世界末日，但也不是必须的。

## 更复杂的 app 呢？

由于 Docker 的理念(每个容器一个进程)，**大多数应用程序将需要多个容器**。例如，一个 WordPress 站点应该包含一个运行 PHP 的 web 服务器容器和一个 MySQL 数据库容器。这意味着，你需要某种方式让容器说话。这被称为**容器编排**。

如果你可以在一台主机上运行你所有的容器，那么 [**Docker Compose**](https://docs.docker.com/compose/overview/) 可能会满足你的编排需求。安装 Docker 的时候就包含了，简单易学。它允许您同时启动多个容器，并在它们之间建立网络，这样它们就可以相互通信。这是编排多个容器的最快和最简单的方法。

如果您需要协调分布在多个主机上的容器，那么最流行的解决方案是。许多支持 Docker 部署的主机都提供了用于编排的 Kubernetes。

## 从理解 Docker 中快速取胜

现在看起来可能不重要，但是当你第一次遇到由环境差异引起的问题时，把这些知识归档。你*不会*希望它再次发生。通过学习 Docker，**你将能够为你的应用**确保一个一致的环境，不管它在哪里运行或者谁在运行它。这意味着**一致的结果**，你、你的客户和你的雇主都可以信赖。**
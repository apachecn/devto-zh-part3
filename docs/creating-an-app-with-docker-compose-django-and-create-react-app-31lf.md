# 使用 Docker Compose、Django 和 Create React App 创建应用程序

> 原文：<https://dev.to/englishcraig/creating-an-app-with-docker-compose-django-and-create-react-app-31lf>

如果你想跳过文本，或者迷失在一些参考文献中，可以在 [GitHub](https://github.com/cfranklin11/docker-django-react) 上找到本教程的最终代码。

*更新: [ohduran](https://dev.to/ohduran) 已经基于这个教程创建了一个 [cookiecutter 模板](https://github.com/ohduran/cookiecutter-react-django)，如果你想要一个快速简单的方法来获得代码。*

受体育数据网站 [Squiggle](https://squiggle.com.au/) 和 [Matter of Stats](http://www.matterofstats.com/) 的启发，在构建包含 [Tipresias](https://github.com/cfranklin11/tipresias) (我的无足轻重的机器学习模型)的应用程序时，我想包含一个适当的前端，其中包含指标、图表和逐轮提示。我已经知道我必须对这个东西进行 dockerize，因为我正在跨 Python 和 R 处理多个包，如果不使用 Docker，在远程服务器环境中管理这样复杂的依赖关系是非常困难的(并且不可能在像 Heroku 这样的现成服务上运行)。我本可以通过使用基本的 Django 视图(即静态 HTML 模板)来构建我的页面，从而避免加剧我的复杂性问题，但是在使用了混杂的古老 Rails 视图，并在其上嫁接了 React 组件来增加一点交互性(然后是大量的交互性)之后，我更喜欢从前端和后端之间的清晰分离开始。更重要的是，我想专注于机器学习、数据工程和服务器端逻辑(更不用说我无法从湿纸袋中设计出自己的路)，所以我聪明可爱的妻子同意帮我做前端，她不可能满足于在 10 年前的范式环境中编码。这将是一个现代的网络应用架构，否则我将不得不填充自己的 div。

将 Docker、Django 和 React 结合起来的问题是，我以前从来没有设置过这样的东西，而且，虽然我最终弄明白了，但我必须从多个指南/教程中拼凑出我的解决方案，这些指南/教程做了我想做的某个方面，而没有涵盖全部。特别是，我发现的教程倾向于构建 Django 可以在其视图中使用的静态 Javascript 资产。这对于生产来说很好，但是没有热重新加载(即让文件更改自动重启服务器，以便它们在浏览器中加载的相关页面中得到反映)的工作是开发的难题:起初你认为你可以忍受轻微的不适，但持续的瘙痒会让你疲惫不堪，成为你清醒时所有想法的全部焦点，让你分心，质疑你生活中的所有选择。想象一下，每当您更改一行代码时，运行一个构建命令可能需要一分钟的时间。副业并不要求最佳的生产力，但是，不像工作，如果他们成为工作的痛苦，很容易就放弃了。

## 我们要做什么

1.  创建一个运行在 Docker 容器中的 Django 应用程序。
2.  使用名副其实的 Create React 应用程序创建一个 React 应用程序，它运行在 Docker 容器中。
3.  在 Docker Compose 中将这些 Docker 化的应用程序实现为服务。
4.  将前端服务连接到一个基本的后端 API，它可以从中获取数据。

**注意:**本教程假设读者具备 Docker、Django 和 React 的工作知识，以便重点关注在开发环境中让这三者协同工作的细节。

## 1。创建一个 dockerized Django 应用程序

让我们首先创建一个名为 what you want 的项目目录，然后是一个带有`requirements.txt`的`backend`子目录，它只是暂时添加了`django`包。这将允许我们在一个 Docker 镜像中安装并运行 Django，这个镜像是用下面的`Dockerfile` :
构建的

```
# Use an official Python runtime as a parent image
FROM python:3.6

# Adding backend directory to make absolute filepaths consistent across services
WORKDIR /app/backend

# Install Python dependencies
COPY requirements.txt /app/backend
RUN pip3 install --upgrade pip -r requirements.txt

# Add the rest of the code
COPY . /app/backend

# Make port 8000 available for the app
EXPOSE 8000

# Be sure to use 0.0.0.0 for the host within the Docker container,
# otherwise the browser won't be able to find it
CMD python3 manage.py runserver 0.0.0.0:8000 
```

Enter fullscreen mode Exit fullscreen mode

在终端中，运行以下命令来构建映像，创建一个名为 hello_world 的 Django 项目，并运行 app:

```
docker build -t backend:latest backend
docker run -v $PWD/backend:/app/backend backend:latest django-admin startproject hello_world .
docker run -v $PWD/backend:/app/backend -p 8000:8000 backend:latest 
```

Enter fullscreen mode Exit fullscreen mode

注意，我们为`backend`目录创建了一个卷，因此由`startproject`创建的代码将出现在我们的机器上。create 命令末尾的`.`将把所有 Django 文件夹和文件放在我们的后端目录中，而不是创建一个新的项目目录，这会使管理 Docker 容器中的工作目录变得复杂。

打开浏览器进入`localhost:8000`，验证应用是否已启动并运行。

## 2。创建一个 dockerized Create React App (CRA)应用程序

虽然我是从编写前端 Javascript 开始的，但我发现我的职业是在后端系统上工作。因此，由于我自己的失职和前端工具和技术的快速变化，我还没有准备好从零开始建立一个现代前端应用程序。但是，我完全有能力安装软件包和运行命令。

与 Django 应用程序不同，我们不能一次用 CRA 应用程序创建 Docker 映像，因为我们首先需要一个带有 node 的`Dockerfile`，这样我们可以初始化 CRA 应用程序，然后我们将能够添加常用的`Dockerfile`命令来安装依赖项。因此，创建一个带有`Dockerfile`的`frontend`目录，如下所示:

```
# Use an official node runtime as a parent image
FROM node:8

WORKDIR /app/

# Install dependencies
# COPY package.json yarn.lock /app/

# RUN npm install

# Add rest of the client code
COPY . /app/

EXPOSE 3000

# CMD npm start 
```

Enter fullscreen mode Exit fullscreen mode

一些命令目前被注释掉了，因为我们没有引用一些文件，但是我们以后会需要这些命令。在终端中运行以下命令来构建映像、创建应用程序并运行它:

```
docker build -t frontend:latest frontend
docker run -v $PWD/frontend:/app frontend:latest npx create-react-app hello-world
mv frontend/hello-world/* frontend/hello-world/.gitignore frontend/ && rmdir frontend/hello-world
docker run -v $PWD/frontend:/app -p 3000:3000 frontend:latest npm start 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们将新创建的应用程序目录的内容移到前端目录并删除它。Django 默认给了我们这样做的选项，但是除了创建自己的目录之外，我找不到任何迹象表明 CRA 会做任何事情。围绕这种嵌套结构工作是一种痛苦，所以我发现更容易的方法是将所有东西都移到 docker-service 层，并从那里开始工作。将浏览器导航至`localhost:3000`以确保应用程序正在运行。此外，您可以取消对`Dockerfile`中剩余命令的注释，以便在下次重新构建映像时安装任何新的依赖项。

## 3。docker-组合成服务

现在我们有了两个 Docker 映像，并且能够在它们各自的 Docker 容器中运行应用程序，让我们用 Docker Compose 简化运行它们的过程。在`docker-compose.yml`中，我们可以定义我们的两个服务`frontend`和`backend`，以及如何运行它们，这将允许我们将多个`docker`命令及其多个参数合并成更少的`docker-compose`命令。配置文件如下所示:

```
version: "3.2"
services:
  backend:
    build: ./backend
    volumes:
      - ./backend:/app/backend
    ports:
      - "8000:8000"
    stdin_open: true
    tty: true
    command: python3 manage.py runserver 0.0.0.0:8000
  frontend:
    build: ./frontend
    volumes:
      - ./frontend:/app
      # One-way volume to use node_modules from inside image
      - /app/node_modules
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=development
    depends_on:
      - backend
    command: npm start 
```

Enter fullscreen mode Exit fullscreen mode

我们已经将 docker 命令的各种参数转换为配置文件中的键值对，现在我们只需执行`docker-compose up`就可以运行我们的前端和后端应用程序。这样，你应该可以看到它们在`localhost:8000`和`localhost:3000`并行运行。

## 4。将两端连接到一个应用程序中

当然，这篇文章的目的不是为了好玩而学习如何运行独立的 React 和 Django 应用程序。我们在这里建立一个单一的，集成的应用程序，它有一个动态的，现代的前端，从一个强大的后端 API 输入数据。为了实现这个目标，在保持应用程序尽可能简单的同时，让前端向后端发送文本，后端将返回文本中的字符数，然后前端将显示这些字符。

### 设置 Django API

让我们从创建一个 API 路由供前端调用开始。您可以通过在终端中运行以下命令来创建一个新的 Django 应用程序(这是 Django 项目架构中的一个子应用程序/模块):

`docker-compose run --rm backend python3 manage.py startapp char_count`

这在`backend`中为您提供了一个名为`char_count`的新目录，我们可以在其中定义路由及其相关逻辑。

我们可以用下面的代码在`backend/char_count/views.py`中创建 API 响应，它将返回提交文本的字符数:

```
from django.http import JsonResponse

def char_count(request):
    text = request.GET.get("text", "")

    return JsonResponse({"count": len(text)}) 
```

Enter fullscreen mode Exit fullscreen mode

现在，为了让 Django 项目知道我们的新应用程序，我们需要通过将`"char_count.apps.CharCountConfig"`添加到列表中来更新`backend/hello_world/settings.py`中的`INSTALLED_APPS`。为了将计数响应添加到可用的 URL 中，我们用 char_count 视图更新了`backend/hello_world/urls.py`，如下所示:

```
from django.contrib import admin
from django.urls import path
from char_count.views import char_count

urlpatterns = [
    path('admin/', admin.site.urls),
    path('char_count', char_count, name='char_count'),
] 
```

Enter fullscreen mode Exit fullscreen mode

因为我们正在更改项目设置，所以我们需要停止 Docker 编写过程(在单独的选项卡中或者 ctl+c 或者`docker-compose stop`),然后用`docker-compose up`重新开始。我们现在可以去`localhost:8000/char_count?text=hello world`，看到它有 11 个字符。

### 连接对 API 做出反应

首先，让我们再添加一点可爱的配置，以确保我们不会遇到与我们不想处理的网络相关的无声错误。我们的 Django 应用程序目前不能在除了`localhost`之外的任何主机上运行，但是我们的 React 应用程序只能通过 Docker 服务名`backend`访问它(它做一些神奇的主机映射工作)。所以，我们需要在`backend/hello_world/settings.py`的`ALLOWED_HOSTS`中加上`"backend"`，在`package.json`中加上`"proxy": "http://backend:8000"`。这将允许两个服务互相对话。此外，我们将需要使用 npm 包`axios`来进行 API 调用，所以将它添加到`package.json`中，并用下面的代码重新构建映像:

```
docker-compose run --rm frontend npm add axios
docker-compose down
docker-compose up --build 
```

Enter fullscreen mode Exit fullscreen mode

诚然，我的前端开发技能很差，但请记住，下面的小组件并不反映我对 React(甚至 HTML)的了解。为了简单起见，我删除了 CRA 的样板文件，代之以一个输入、一个按钮、一个点击处理程序和一个标题。

```
import React from 'react';
import axios from 'axios';
import './App.css';

function handleSubmit(event) {
  const text = document.querySelector('#char-input').value

  axios
    .get(`/char_count?text=${text}`).then(({data}) => {
      document.querySelector('#char-count').textContent = `${data.count} characters!`
    })
    .catch(err => console.log(err))
}

function App() {
  return (
    <div className="App">
      <div>
        <label htmlFor='char-input'>How many characters does</label>
        <input id='char-input' type='text' />
        <button onClick={handleSubmit}>have?</button>
      </div> 
      <div>
        <h3 id='char-count'></h3>
      </div>
    </div>
  );
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

现在，当我们在输入中输入文本并单击按钮时，文本的字符数显示如下。最棒的是:我们在球场上上下下都热重装！你可以在前端添加新的组件，在后端添加新的类，当你工作的时候，你所有的改变(除了配置和依赖)都会反映在应用的运行中，而不需要手动重启服务器。

## 总结

最后，设置所有这些并不太复杂，但是有许多小问题，其中许多并没有给出一个好的错误消息来查看堆栈溢出。此外，至少在我的案例中，一开始我真的很难想象这些部分如何一起工作。React 应用会像 Rails 中的`webpacker`一样放在 Django 应用中吗？如果这两个应用是独立的 Docker Compose 服务，如何将它们连接起来？最后，我们学会了如何:

*   在 Docker 容器中设置 Django。
*   在 Docker 容器中设置 Create React 应用程序
*   用 Docker Compose 配置这些容器
*   使用 Docker Compose 的服务名(例如`backend`)和`package.json`的`"proxy"`属性将 React 的 HTTP 调用指向 Django 的 API 并显示响应。
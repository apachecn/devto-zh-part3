# Docker，Django，React:建立资产并部署到 Heroku

> 原文：<https://dev.to/englishcraig/docker-django-react-building-assets-and-deploying-to-heroku-24jh>

结合 Docker、Django 和 React 的系列文章的第 2 部分。这是基于第 1 部分的开发设置，所以你可能想先看看。如果想跳到最后或者需要参考，可以在回购的 [`production-heroku`](https://github.com/cfranklin11/docker-django-react/tree/production-heroku) 分支上看到最终版本的代码。

*更新: [ohduran](https://dev.to/ohduran) 已经基于这个教程创建了一个 [cookiecutter 模板](https://github.com/ohduran/cookiecutter-react-django)，如果你想要一个快速简单的方法来获得代码。*

* * *

现在，我们的应用程序在我们的本地环境中就像 69 年的野马 Shelby GT500 一样嗡嗡作响，在停车场上到处都是热重装甜甜圈，是时候部署那个坏男孩了，这样全世界都可以找到他们最喜欢的短语中有多少个字符。为了将此应用程序部署到生产环境中，我们需要执行以下操作:

*   设置 Django 使用 WhiteNoise 为生产中的静态资产提供服务。
*   创建一个产品`Dockerfile`,将我们的前端和后端结合成一个应用程序。
*   创建要部署到的新 Heroku 应用程序。
*   配置我们的应用程序，将 Docker 映像部署到 Heroku。

## 使用 WhiteNoise 服务我们的前端资产

### 针对不同环境更新设置

因为我们只想在生产中使用 WhiteNoise，所以我们必须改变 Django 应用程序的设置，以区分开发和生产环境。有不同的方法可以做到这一点，但其中一个似乎提供了最大的灵活性，对我来说已经足够好了，就是为每个环境创建一个设置文件，所有这些都继承自一些基本设置，然后确定哪个设置文件用于环境变量。在`backend/hello_world`，也就是我们的项目目录中，创建一个`settings`文件夹(像往常一样，里面有一个`__init__.py`使其成为一个模块)，将已有的`settings.py`移入其中，并重命名为`base.py`。这将是所有环境都将继承的基础应用程序设置的集合。为了确保我们不会意外地使用不安全的设置进行部署，从`base.py`中剪切以下代码，并将其粘贴到新创建的`development.py` :

```
# Quick-start development settings - unsuitable for production
# See https://docs.djangoproject.com/en/2.2/howto/deployment/checklist/ 
# SECURITY WARNING: keep the secret key used in production secret! SECRET_KEY = "<some long series of letters, numbers, and symbols that Django generates>"

# SECURITY WARNING: don't run with debug turned on in production! DEBUG = True

ALLOWED_HOSTS = ["backend"] 
```

Enter fullscreen mode Exit fullscreen mode

现在仔细检查:那些代码行从`base.py`开始就消失了吗？很好。我们不太容易被黑客攻击。在文件的顶部，添加行`from hello_world.settings.base import *`。从`base`导入的`*`所做的是使所有那些已经在我们的基础中定义的设置在`development`中也可用，在那里我们可以根据需要自由地覆盖或扩展它们。

由于我们通过将设置文件移动到`settings`子目录中，将它们嵌入到项目中更深的位置，我们还需要更新`base.py`中的`BASE_DIR`以指向正确的目录，该目录现在高了一级(相对而言)。您可以在另一个`os.path.dirname`调用中包装该值，但是我发现下面的内容更容易阅读:

```
BASE_DIR = os.path.abspath(os.path.join(os.path.dirname(__file__), "../../")) 
```

Enter fullscreen mode Exit fullscreen mode

Django 用环境变量`DJANGO_SETTINGS_MODULE`决定运行应用程序时使用哪个模块，这应该是我们想要使用的设置的模块路径。为了避免错误，我们将`backend/hello_world/wsgi.py`中的默认值更新为`'hello_world.settings.base'`，并将以下内容添加到我们在`docker-compose.yml`中的`backend`服务中:

```
environment:
  - DJANGO_SETTINGS_MODULE=hello_world.settings.development 
```

Enter fullscreen mode Exit fullscreen mode

### 添加带有白化的生产设置

我们希望在生产中使用 [WhiteNoise](http://whitenoise.evans.io/en/stable/) 而不是 Django 现成的任何东西的原因是，在默认情况下，Django 为前端资产提供服务非常慢，而 WhiteNoise 相当快。没有专业级的 CDN-AWS-S3-bucket-thingy 快，但对我们的目的来说足够快了。

首先，我们需要通过将`whitenoise`添加到`requirements.txt`来安装 WhiteNoise。

接下来，因为我们有特定于开发人员的设置，所以让我们用它自己的设置来创建`production.py`。首先，我们将添加我们已有的开发设置的生产变化，它应该看起来像这样:

```
import os
from hello_world.settings.base import *

SECRET_KEY = os.environ.get("SECRET_KEY")
DEBUG = False
ALLOWED_HOSTS = [os.environ.get("PRODUCTION_HOST")] 
```

Enter fullscreen mode Exit fullscreen mode

我们将在 Heroku 上设置一个应用程序后添加允许的主机。请注意，您可以在设置文件中对允许的主机进行硬编码，但是如果您部署到不同的环境，使用环境变量会更容易更改。`SECRET_KEY`可以是您想要的任何字符串，但是出于安全原因，它应该是一些长的随机字符串(我只是用了一个密码生成器)，并且您应该将它保存为一个环境/配置变量，远离残酷的盗窃世界。不要将其签入源代码管理！。

为了使 WhiteNoise 能够服务于我们的前端资产，我们将以下内容添加到`production.py` :

```
INSTALLED_APPS.extend(["whitenoise.runserver_nostatic"])

# Must insert after SecurityMiddleware, which is first in settings/common.py MIDDLEWARE.insert(1, "whitenoise.middleware.WhiteNoiseMiddleware")

TEMPLATES[0]["DIRS"] = [os.path.join(BASE_DIR, "../", "frontend", "build")]

STATICFILES_DIRS = [os.path.join(BASE_DIR, "../", "frontend", "build", "static")]
STATICFILES_STORAGE = "whitenoise.storage.CompressedManifestStaticFilesStorage"
STATIC_ROOT = os.path.join(BASE_DIR, "staticfiles")

STATIC_URL = "/static/"
WHITENOISE_ROOT = os.path.join(BASE_DIR, "../", "frontend", "build", "root") 
```

Enter fullscreen mode Exit fullscreen mode

上面的大部分内容来自 Django 中实现的 [WhiteNoise 文档](http://whitenoise.evans.io/en/stable/django.html),以及一些尝试和错误，以确定使用哪些文件路径来查找 React 构建的资产(更多内容见下文)。令人困惑的是所有变量都指向稍微不同的与前端资产相关的目录。

*   `TEMPLATES`:带有模板的目录(如 Jinja)或 html 文件
*   Django 可以找到 html、js、css 和其他静态资产的目录
*   Django 将这些静态资产移动到的目录，当应用程序运行时，Django 将从这个目录为这些资产提供服务
*   `WHITENOISE_ROOT`:white noise 可以找到所有**非 html** 静态资产的目录

### 为生产添加主页网址

除了更改设置，我们还必须让 Django 知道路径`/`，因为现在它只知道`/admin`和`/char_count`。因此，我们将不得不更新`/backend/hello_world/urls.py`，看起来像下面这样:

```
from django.contrib import admin
from django.urls import path, re_path
from django.views.generic import TemplateView
from char_count.views import char_count

urlpatterns = [
    path("admin/", admin.site.urls),
    path("char_count", char_count, name="char_count"),
    re_path(".*", TemplateView.as_view(template_name="index.html")),
] 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们添加了一个 regex 路径(`.*`)，它对 Django 说，“任何您没有明确指示的请求，只需通过发送它们`index.html`来响应即可”。这在实践中是如何工作的，在开发环境中，React 的 Webpack 服务器将仍然处理对`/`(以及除了上面定义的两个路径之外的任何路径)的调用，但是在生产中，当没有 Webpack 服务器时，Django 将只是耸耸肩，从静态文件目录中提供`index.html`(如上面的设置中所定义的)，这正是我们想要的。我们使用`.*`而不是特定路径的原因是，它允许我们自由定义尽可能多的路径供前端处理(例如使用 React 路由器),而不必更新 Django 的 URL 列表。

这些改变都不会改变我们的应用在本地的功能，所以试着运行`docker-compose up`来确保没有任何问题。

## 创建生产文档文件

为了让 WhiteNoise 能够服务于我们的前端资产，我们需要将它们包含在与 Django 应用程序相同的映像中。有几种方法可以做到这一点，但我认为最简单的是复制构建我们后端映像的 docker 文件，并在其中添加我们前端依赖项的安装，以及我们资产的构建。因为这个映像将包含一个包含前端和后端的应用程序，所以把它放在项目根目录下。

```
FROM python:3.6

# Install curl, node, & yarn
RUN apt-get -y install curl \
  && curl -sL https://deb.nodesource.com/setup_8.x | bash \
  && apt-get install nodejs \
  && curl -o- -L https://yarnpkg.com/install.sh | bash

WORKDIR /app/backend

# Install Python dependencies
COPY ./backend/requirements.txt /app/backend/
RUN pip3 install --upgrade pip -r requirements.txt

# Install JS dependencies
WORKDIR /app/frontend

COPY ./frontend/package.json ./frontend/yarn.lock /app/frontend/
RUN $HOME/.yarn/bin/yarn install

# Add the rest of the code
COPY . /app/

# Build static files
RUN $HOME/.yarn/bin/yarn build

# Have to move all static files other than index.html to root/
# for whitenoise middleware
WORKDIR /app/frontend/build

RUN mkdir root && mv *.ico *.js *.json root

# Collect static files
RUN mkdir /app/backend/staticfiles

WORKDIR /app

# SECRET_KEY is only included here to avoid raising an error when generating static files.
# Be sure to add a real SECRET_KEY config variable in Heroku.
RUN DJANGO_SETTINGS_MODULE=hello_world.settings.production \
  SECRET_KEY=somethingsupersecret \
  python3 backend/manage.py collectstatic --noinput

EXPOSE $PORT

CMD python3 backend/manage.py runserver 0.0.0.0:$PORT 
```

Enter fullscreen mode Exit fullscreen mode

上面的 Dockerfile 安装了运行 Django 和 React 应用程序所需的一切，然后构建前端资产，然后为 WhiteNoise 收集这些资产以服务它们。由于`collectstatic`命令对文件进行了修改，我们希望在构建步骤中运行它，而不是作为一个单独的命令在部署过程中运行。在某些情况下，您可能会选择后者，但是我在部署到 Heroku 时遇到了问题，因为他们会丢弃自由层 dynos 上的部署后文件更改。

另外，请注意将静态文件从`/app/frontend/build`移动到`/app/frontend/build/root`的命令，将`index.html`留在原处。WhiteNoise 需要将除 HTML 文件以外的所有内容放在单独的子目录中。否则，它会弄不清哪些文件是 HTML，哪些不是，最终什么都不会加载。许多博萨人为给我们带来这个信息而牺牲。

## 在 Heroku 上创建 app

如果你是 Heroku 的新手，他们的[入门指南](https://devcenter.heroku.com/articles/getting-started-with-python)将带你了解创建一个通用的、非 dockerized 的 Python 应用的基础。如果你还没有，安装 [Heroku CLI](https://devcenter.heroku.com/articles/getting-started-with-python#set-up) 。我们可以通过在项目中运行`heroku create`来创建一个 Heroku 应用程序。一旦你创建了新的 Heroku 应用，复制命令显示的 URL，并将其添加到`settings.production`中的`ALLOWED_HOSTS`。就像在 dev 上向我们允许的主机添加`backend`一样，我们需要这个来确保 Django 愿意响应我们的 HTTP 请求。(在部署到新环境时，由于忘记将主机名添加到`ALLOWED_HOSTS`中，我甚至无法开始计算我反复刷新的空白屏幕的数量，其中夹杂着困惑和绝望)。如果你想保密，或者想要更大的灵活性，你可以添加`os.environ.get("PRODUCTION_HOST")`到允许的主机，然后添加你的 Heroku 应用的 URL 到它的配置变量。我不确定包含或省略哪些 URL 元素有多严格，但是`<your app name>.herokuapp.com`绝对有效。

对于生产中的环境变量，我们可以使用 Heroku CLI 来设置对公众隐藏的安全配置变量。Heroku 有办法用`heroku.yml`添加这些变量，但我总是很难让它工作，所以在这种情况下我选择手动方式。这有额外的好处，不必担心哪些变量可以提交给源代码控制，哪些变量需要保密。要设置配置变量，请在终端中运行以下命令:

```
heroku config:set PRODUCTION_HOST=<your app name>.herokuapp.com SECRET_KEY=<your secret key> DJANGO_SETTINGS_MODULE=hello_world.settings.production 
```

Enter fullscreen mode Exit fullscreen mode

如前所述，`PRODUCTION_HOST`是可选的(取决于你是否直接将 app URL 添加到`ALLOWED_HOSTS`)。`DJANGO_SETTINGS_MODULE`将确保应用程序在 Heroku 上运行时使用我们的生产设置。

## 部署到 Heroku

有几种不同的方法可以将 Dockerized 应用程序部署到 Heroku，但我喜欢`heroku.yml`，因为像`docker-compose.yml`一样，它将所有的应用程序配置和命令都放在一个地方。Heroku 有一个[很好的介绍](https://devcenter.heroku.com/articles/build-docker-images-heroku-yml)它是如何工作的，但是对于我们的目的，我们只需要以下:

```
build:
  docker:
    web: Dockerfile
run:
  web: python3 backend/manage.py runserver 0.0.0.0:$PORT 
```

Enter fullscreen mode Exit fullscreen mode

我们还需要在终端中运行`heroku stack:set container`来告诉我们的 Heroku 应用程序使用 Docker，而不是 Heroku 的特定语言构建包。现在，部署就像运行`git push heroku master`一样简单(如果您在`master`分支；否则，运行`git push heroku <your branch>:master`。

一旦 Heroku 完成了我们的映像构建和部署，我们就可以打开一个浏览器来计算 CLOOOOOUUUUUD 上的字符数了！！！

## 总结

从概念上讲，将前端和后端放在一个我们可以部署到 Heroku 的应用程序中是非常简单的，但是在配置和文件结构中有太多的小问题(更不用说当一个人出错时缺乏有意义的错误消息)，我发现让它全部工作起来非常困难。在写本教程时，我甚至经历了第二次这个过程，我在这里忘记了一些东西，在那里添加了错误的东西，并花了几个小时试图回忆我第一次是如何让它工作的，以及我可能犯了什么可怕的罪，导致编码神现在惩罚我。

但是现在，我们刚刚完成了以下工作:

*   为 Django 配置特定于环境的设置。
*   建立 WhiteNoise 服务于生产中的静态资产。
*   创建一个包含前端和后端代码及依赖项的生产 docker 文件。
*   创建一个 Heroku 应用程序，并使用`heroku.yml`和容器栈在上面部署我们的代码。
# 使用 Dokku 部署 Django

> 原文：<https://dev.to/jonathans/deploying-django-using-dokku-18fj>

*这个帖子也在[我的个人博客](https://www.argpar.se/posts/programming/deploy-django-using-dokku/)T3 上结束了。*

## 我想在网上看到我的网站！

这是我多次想到的，但是部署东西的想法通常看起来非常复杂(除非你使用 heroku，它以牺牲你的钱包为代价隐藏了大部分复杂性)。运营的世界是广阔的，有时很难把握从哪里开始实现你想要的；部署您构建的站点。

因此，本指南应该为你提供敲门砖，同时感受到最终有所成就的回报，因为花几个小时却看不到任何结果并不好玩。我们开始吧！

## Dokku 入门

除非你已经有了可以开发你的项目的服务器，否则我建议你试试 scaleway。一旦你在你的服务器上安装了类似 debian 的东西。首先要做的是配置 [fail2ban](https://www.scaleway.com/en/docs/protect-server-fail2ban/) 。如果你不这样做，你的服务器肯定会立刻被黑客攻击，我在一个数字海洋水滴中学到了惨痛的教训。你会惊讶于经常有人试图登录你的服务器。

现在你已经准备好安装 Dokku 了，请按照[官方说明](http://dokku.viewdocs.io/dokku/getting-started/installation/#installing-the-latest-stable-version)进行操作。一旦您执行了最新版本的 bash 命令，设置好您的 *public* SSH 密钥并为您的服务器 IP 地址添加了一个 [A 记录](https://my.bluehost.com/hosting/help/whats-an-a-record)，您就准备好部署您的应用程序了。

## 配置 Dokku 进行部署

如果你还没有想要部署的 Django 项目，我建议你使用下面的模板。对于任何 django 项目来说，这都是一个很好的起点，并且已经有了合理的默认值。

一旦执行了以下命令，您就可以在本地运行 django 了。这也是我使用 Dokku 部署 Django 的基础。

```
pip3 install cookiecutter
cookiecutter https://gitlab.com/thiras/cookiecutter-docker-django 
```

SSH 进入您的服务器并运行以下命令

```
# this is how you will refer to your app when using dokku
dokku apps:create your-django-app

# install the postgres plugin to be able to use postgres with dokku
sudo dokku plugin:install https://github.com/dokku/dokku-postgres.git

# creates a database for your app.
dokku postgres:create djangodb

# link the above database to your app
dokku postgres:link djangodb your-django-app

# configure which domain you'll be using for your app
# I'd recommend using a fully qualified domain
domains:add your-django-app subdomain.yourdomain.com 
```

如果您正在使用来自 cookiecutter 模板的 django 应用程序，您会注意到它不包含任何`Procfile`。多库确实需要一份档案。所以继续添加并提交一个名为`Procfile`的文件，其中包含:

> web: gunicorn appname.wsgi:应用程序日志文件

您现在已经设置好了，可以尝试部署您的 django 应用程序了！

## 调试部署问题

Dokku 承诺您将能够使用 git 进行部署。那不是一种享受吗？在 [dokku 文档](http://dokku.viewdocs.io/dokku/deployment/application-deployment/#deploying-to-subdomains)中，它详细描述了如何将遥控器添加到 git 中。这些命令是

```
# if you're using a subdomain
git remote add dokku dokku@yourdomain.com:subdomain

# if you're deploying to the root domain
git remote add dokku dokku@dokku.me:dokku.me

git push dokku master 
```

**问题**:部署失败。因为主机无法识别。

```
fatal: 'yourapp' does not appear to be a git repository
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists. 
```

**解决方案**:如果你正面临这个问题，也许是因为你使用了一个子域，你可以改变遥控器来包含你的公共服务器 IP。所以遥控器看起来会像这样

**问题**:即使你添加了一个，它也找不到`Procfile`。

**解决方案**:你可能有一个名为`.dockerignore`的文件。如果在那里命名了 procfile，容器将不会获取您的 procfile。移除它应该可以解决这个问题。

我们再次尝试部署，现在它实际上应该部署成功了。

问题:当它成功部署时，你唯一看到的是 400 错误请求。当你浏览你的网站时。多奇怪啊！

**解决方案**:在`settings.py`中你需要把`ALLOWED_HOSTS`改成你自己的域。一旦你做到了这一点，你现在可以再次部署你的网站，这个特殊的问题就会消失。

**问题**:你尝试查看网站。所有静态文件(如 CSS 文件)返回 404。这个网站看起来很可怕。

**解决方案**:开发服务器为您提供静态文件。gunicorn 服务器没有。有一个管理命令`collectstatic`，您需要在每次部署时运行它。

创建一个名为`app.json`的文件。在那里，您可以定义在部署之前应该运行的命令。你可以在 [dokku 文档](http://dokku.viewdocs.io/dokku/advanced-usage/deployment-tasks/#deployment-tasks)T3】中了解更多信息

```
{  "scripts":  {  "dokku":  {  "predeploy":  "python manage.py collectstatic --noinput"  }  }  } 
```

这将把您的静态文件移动到您在 [django 设置](https://docs.djangoproject.com/en/2.2/howto/static-files/#managing-static-files-e-g-images-javascript-css)中定义的静态文件夹中。

如果你再次尝试部署，以为这样就够了，其实不然。这些文件也需要被提供。Nginx 通常服务于这些文件。所以让我们实现它吧！

每次部署 [Dokku 都会在`~home/dokku/appname`中生成](http://dokku.viewdocs.io/dokku/configuration/nginx/#customizing-via-configuration-files-included-by-the-default-tem)一个`nginx.conf`文件。所以我们真的不能编辑那个文件。然而，如果我们看看这个文件，它包含以下内容:

```
include /home/dokku/appname/nginx.conf.d/\*.conf; 
```

因此，如果我们将任何 conf 文件放在文件夹`nginx.conf.d`中，它应该可以工作。我们将放置一个包含以下内容的文件`static.conf`:

```
location /static {
    autoindex on;
    alias   /home/dokku/appname/static;
} 
```

然而，上面的路径并不是生成静态文件的地方。它们是在我们的容器中产生的。如果我们的`static.conf`在源代码控制中，那不是很好吗？你可以用 dokku 插件 [`supply-config`](https://github.com/dokku-community/dokku-supply-config) 来完成。

换句话说，我们需要一种方法来访问容器中生成的静态文件。我们可以通过挂载静态文件所在的卷来实现这一点。

```
dokku docker-options:add your-django-app deploy "-v /home/dokku/appname/static:/app/appname/static" 
```

冒号后的路径是您运行命令收集静态文件后静态文件所在的位置，冒号前的路径与`static.conf`中的路径相同。

这次部署时，即使静态文件也应该可以工作。不过，我会告诉你一个秘密。除了摆弄 nginx，你还可以使用 django 包[whiten noise](http://whitenoise.evans.io/en/stable/)，这可能是一个更合适的解决方案。

但是如果我直接告诉你，你就不会知道所有其他的 Nginx 配置，这些配置可能在以后的某一天会有用。

作为补充阅读，我推荐阅读这篇关于使用 Dokku 部署 django 的指南
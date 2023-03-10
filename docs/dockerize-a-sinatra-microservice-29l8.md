# Dockerize a Sinatra 微服务

> 原文：<https://dev.to/markoa/dockerize-a-sinatra-microservice-29l8>

Sinatra 是一个简洁而有趣的框架，用 Ruby 构建简单的 web 应用和微服务。传统上，开发人员会部署到 Heroku 或自管理服务器这样的平台上。但是，如果您的团队使用多种语言，并且需要一种统一的方式将东西部署到云上，该怎么办呢？这当然是 Docker 的一个很好的用例。

假设你的 Sinatra 应用有一个简单的结构:

```
.
├── Gemfile
├── Gemfile.lock
├── README.md
├── app.rb
├── config.ru
└── spec
    ├── app_spec.rb
    └── spec_helper.rb 
```

Enter fullscreen mode Exit fullscreen mode

要在 Docker 中打包它，创建一个`Dockerfile` :

```
FROM ruby:2.5

RUN apt-get update -qq && apt-get install -y build-essential

ENV APP_HOME /app
RUN mkdir $APP_HOME
WORKDIR $APP_HOME

ADD Gemfile* $APP_HOME/
RUN bundle install --without development test

ADD . $APP_HOME

EXPOSE 4567

CMD ["bundle", "exec", "rackup", "--host", "0.0.0.0", "-p", "4567"] 
```

Enter fullscreen mode Exit fullscreen mode

注意`RUN bundle install --without development test`行。我假设您正在构建一个用于部署目的的容器，并且我们不需要我们的开发依赖。

如果您想在 Docker 中运行所有的开发任务和测试，您有两个选择:

1.  捆绑所有的东西，在生产中使用稍微臃肿的容器映像
2.  在使用`Dockerfile`构建生产映像之前，创建一个`Dockerfile.dev`并将其用于开发和测试。

无论哪种方式，您都可以在本地构建和运行容器:

```
$ docker build -t sinatra-demo .
$ docker run-p 80:4567 sinatra-demo
$ curl localhost
> hello world 
```

Enter fullscreen mode Exit fullscreen mode

太好了，现在我们有了在标准容器中运行的应用程序。

我们如何部署它？今天运行 Docker 容器的最佳平台是 Kubernetes。关于部署的详细教程，请参见我的另一篇文章:

[![markoa image](img/fded043eaf4dbceeb727380e471e4424.png)](/markoa) [## 数字海洋 Kubernetes 上微服务的 CI/CD

### 马尔科·阿纳斯塔索夫 3 月 13 日 19 时 12 分阅读

#kubernetes #tutorial #devops #cicd](/markoa/cicd-for-microservices-on-digitalocean-kubernetes-1dg1)
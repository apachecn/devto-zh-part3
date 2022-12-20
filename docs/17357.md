# 使用多级 docker 文件

> 原文：<https://dev.to/jmarhee/using-multi-stage-dockerfiles-3cfj>

Docker 支持使用 Dockerfile 声明在称为多阶段构建的多个步骤中执行映像构建。

不久前，我构建了一个 Ruby 应用程序,它在 DigitalOcean 上提供并部署了一个基于 Docker 的 VPN 服务器，该服务器本身运行在 Docker 容器中。然而，在 Kubernetes 上运行它之前，我在预先映像的主机上运行了它，这些主机具有内置的依赖关系，这使得创建新实例非常快。

我在 Docker 中有相同的功能，可以在早期构建一个基本映像(顺便提一下，使用与我的基本实例映像相同的 CI/CD 工具)，所以当我去部署它时，我可以使用这些工具，而不必在部署时执行整个构建。然而，最初，我并不知道这一点，并有一个 docker 文件，看起来像这样:

```
FROM ruby:2.2.4

ADD app.rb /app/app.rb
ADD Gemfile /app/Gemfile
ADD views/index.erb /app/views/index.erb
ADD views/confirmation.erb /app/views/confirmation.erb
ADD environment.rb /app/environment.rb
WORKDIR /app
RUN bundle install

ENTRYPOINT ["ruby","app.rb","-o","0.0.0.0"] 
```

它完成了工作，但是每次我部署重建的映像都要花很长时间，因为每次映像被修改时，我都要经历更多的步骤。

因此，我创建了一个基本映像，它只负责首先安装 Gem 依赖项，作为它自己的步骤:

```
FROM ruby:2.2.4 as rb-base

ADD Gemfile /root/Gemfile
WORKDIR /root
RUN bundle install 
```

我将通过上面给它的别名`rb-base`引用这个图像中的资源，并继续移动到我将最终推送的图像，因此我将把以下内容附加到 docker 文件中:

```
FROM ruby:2.2.4 as rb-app
MAINTAINER Joseph D. Marhee <joseph@marhee.me>
WORKDIR /app
COPY --from=rb-base /usr/local/bundle/ /usr/local/bundle/
... 
```

复制我在前一阶段安装的 gems，然后继续处理应用程序的其余部分:

```
...
ADD app.rb /app/app.rb
ADD Gemfile /app/Gemfile
ADD views/index.erb /app/views/index.erb
ADD views/confirmation.erb /app/views/confirmation.erb
ADD environment.rb /app/environment.rb
ENTRYPOINT ["ruby","app.rb","-o","0.0.0.0"] 
```

为了完成我最终要运行的图像。

接下来，除非您需要更新您的基本映像(这允许您作为一项单独的任务来处理)，否则您可以针对新的仅应用程序映像阶段`rb-app`(即，在构建并推送至您的注册表的 CI 系统中):

```
docker build --target rb-app -t coolregistryusa.biz/jmarhee/app:latest . 
```

并从注册表中获取图像。
# 在生产中对 Webpacker 做出反应并进行跟踪

> 原文：<https://dev.to/jmoore53/react-and-rails-with-webpacker-in-production-pnf>

我已经写了一手 Rails 应用程序，通常我在部署时使用资产管道和 coffeescript 以及 bootstrap 之类的东西。从前端的角度来看，大多数项目都比较简单，但这并不是说它们在服务器端没有功能。我通常不会很难将项目从开发转移到生产，大多数工作都是琐碎的配置细节。

在我最近的 rails 项目中，我希望在注册时添加一个 react 表单。我发现了一些与 react 一起工作的 ruby gems，我决定挑选出 [reactjs/react-rails](https://github.com/reactjs/react-rails) 。我在启动和运行开发环境方面没有任何问题，但是当我试图将项目转移到测试环境，然后生产时，webpacker 给了我一个半小时的时间。我最终放弃了这个项目，转向新的东西。

有没有人有类似的倾倒副业的经历？在割断绳子继续前进之前，你通常会在一个问题上做多少工作？
# 推送至 Heroku 后构建您的应用程序

> 原文：<https://dev.to/cadoangelus/building-your-application-after-pushing-to-heroku-4m1o>

当将他们的应用程序部署到 Heroku 时，大多数人会为他们的应用程序运行开发脚本。这将极大地增加应用程序的加载时间。相反，你可以在将你的应用程序推送到 Heroku 之后，利用你的**包中的 **heroku-postbuild** 脚本来构建你的应用程序的生产就绪版本**
# 如何在最新的 Django RestAPI 中管理本地 vs 开发 vs 生产 settings.py？

> 原文：<https://dev.to/thammuio/how-to-manage-local-vs-dev-vs-production-settings-in-latest-django-restapi-28n6>

**Django 最佳实践**

因为我们在本地、开发和生产设置中有一些共同的设置，并且也有不同于本地、开发和生产的设置；例如数据库连接、安装的应用程序等...我想用自定义设置启动我的 Django 应用程序 _。py 文件，这样我们可以在设置中有更好的可视性，也不会导致 git 冲突....
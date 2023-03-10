# 第 2 部分——关于部署具有静态文件服务的 Django 应用程序的深入视频教程

> 原文：<https://dev.to/ppshobi/part-2--an-in-depth-video-tutorial-on-deploying-a-django-application-with-static-file-serving-6ia>

Django 是目前最流行的基于 Python 的 web 框架。Django 是强大的，健壮的，充满能力的，被一个支持的社区所包围。

在这个视频中，我们将看看如何在我们的应用程序中设置静态文件服务，如 CSS，javascript，图像等...在我们简单的 Django 应用程序中。我们在本系列的第 1 部分中构建并部署了它

Django 上的静态文件应该由`nginx`直接提供，而不是我们的 Django 应用程序。所以您需要在您的`nginx`配置中添加几行代码，来告诉您的静态文件在哪里。我已经试着在这个视频中解释清楚了。如有任何疑问，请尽管提出。

让我知道评论中的建议、想法和疑问。订阅该频道并与您的朋友和同事分享

链接到已经设置了静态文件的示例应用程序报告:[https://github . com/ShobiExplains/AWS demo/releases/tag/v 0 . 0 . 2](https://github.com/ShobiExplains/AwsDemo/releases/tag/v0.0.2)

Nginx 配置参考:[https://github . com/ShobiExplains/AWS demo/blob/master/django . conf](https://github.com/ShobiExplains/AwsDemo/blob/master/django.conf)

[https://www.youtube.com/embed/_TBw7ALJp0Y](https://www.youtube.com/embed/_TBw7ALJp0Y)
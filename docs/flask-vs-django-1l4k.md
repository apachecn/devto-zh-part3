# 弗拉斯克对姜戈？

> 原文：<https://dev.to/imronlearning/flask-vs-django-1l4k>

因此，我打算在一周内学习完*之后，在本周做一个 Flask 教程，但是我决定用 Flask 和 Django 做一个比较，因为我已经有了使用这两个 python web 框架的实践经验。原因是我可以帮助你选择学习哪一个框架，因为学习一个框架可能非常耗时——但当然非常有趣！*

注意:我只花了一周时间使用 Flask，而过去三年我一直使用 Django。对此不能全信😅

## 相似之处

先说相似点。

### 1) Flask 和 Django 都是 **python web 框架**。

它们使您能够使用 python 作为后端逻辑，html 和 css 作为前端，或者任何您喜欢的前端框架来创建 web 应用程序。

### 2) **可以使用 ORM 或对象-关系-映射。**

这基本上意味着，您可以使用 SQLITE、MYSQL、PostgreSQL 和其他利用 ORM 的数据库。

- **Django** 默认使用 ORM。
- **烧瓶**默认没有 ORM 功能。需要的话需要手动添加。

### 3) **模板化**。

- **Django** 使用类似 Jinja 的模板引擎(实际上 Jinja 的灵感来自 Django 自己的模板系统)

[![djmoch profile image](img/a5d2a87b4e9b2bb005173bf76b0e48b9.png) ](/djmoch) [ Daniel Moch ](/djmoch) • [<time datetime="2019-02-16T23:43:59Z"> Feb 16 '19 </time>](https://dev.to/djmoch/comment/8nkf) 

小评:Django 不用 Jinja，但是它自己的模板引擎。它们看起来很相似，因为正如 Jinja 的网站所说，“它的灵感来自 Django 的模板系统。”

- **Flask** 使用 Jinja 作为其模板引擎。

Jinja 基本上类似于 python for html(python+html = Jinja ),可以让你在 html 中添加逻辑，比如 if/else，for-loops，以及我非常喜欢的模板扩展！

弗拉斯克和姜戈还有很多相似之处，但对我来说，这是最主要的三个。

## 差异

当你把 Flask 和 Django 放在一起时，差别是相当大的。

### 有了长颈瓶，你会得到**难以置信的灵活性**。

你创建 app.py(它就像你的应用程序的主要逻辑)，models.py(它是你存储你的模型的地方，它为你的数据库创建表格)，模板等等。对于如何构建应用程序的结构，你拥有绝对的控制权。

### 有了姜戈，**这一切都是为你打造的！**

settings.py(类似于应用程序的配置文件)、models.py、urls.py(存储所有路由的地方)、模板等。

这就是为什么 Django 的标签是，“**有期限的完美主义者的网络框架。**“因为一切都是内置的，所以你不需要费心创建文件，也不需要考虑如何构建你的应用。**一切都为你准备好了**和**你可以立即开始构建你的应用**。

## 结论

Flask 和 Django 都有他们的长处和短处。每个都有自己的用途和目的。

如果你要做一个简单的应用程序，不要太复杂，那么 Flask 是个不错的选择。

但是如果你是一个初学者，并且想用 python 进行 web 开发，我建议你从 Flask 开始。你在 Flask 学到的东西可以应用到 Django 上。有了烧瓶就简单多了。

恭喜🎉🎉！你成功了！我希望这能帮到你！另外，告诉我你是否想看我在 Youtube 上做《Flask》或《Django》的教程。
谢谢你！休息一下，放松一下🍫。

感谢所有给我反馈并增加我关于 Flask 和 Django 知识的人！让他们来吧，✌🏽

*在[推特](https://twitter.com/heyimprax)和 [Instagram](https://www.instagram.com/heyimprax/?hl=en) @heyimprax 上关注我。*
*订阅我的频道在[编码 Prax](https://www.youtube.com/channel/UCZi44WflNC257aYE317PbLg) 。*
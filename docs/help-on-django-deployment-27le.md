# 关于 Django 部署的帮助

> 原文：<https://dev.to/dcruz1990/help-on-django-deployment-27le>

一个多月来，我一直在用 Django 编写一个简单的 app 来管理我公司的客户端文件，我有 Deepin 操作系统 15.9。由于我是新手，我犯了一个错误，没有创建一个虚拟的 env，所以现在当我试图在 Debian 9 容器中部署应用程序时，我遇到了 WeasyPrint 错误，cairocffi 似乎根本无法安装...尽管在安装 WeasyPrint 时得到了令人满意的 pip 消息。
然后，又累又失望，我改变了 xhtml2pdf 库的 2 个视图，我也没有成功，因为它在使用 Latin-1 时显示了编码错误，我试图尝试 UTf-8，但它得到了不支持的类型错误。
周一我会试着在服务器上安装 Deepin，然后再试试 WeasyPrint...
有什么解决办法吗？？？？
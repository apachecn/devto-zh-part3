# 使用 ngrok 使用 webhooks

> 原文：<https://dev.to/0xshetty/working-with-webhooks-using-ngrok-38a1>

当使用外部 API 时，用模拟请求测试应用程序是一种方法。所以当我想用一个实时 API 进行测试时，我不得不把它放在公开的地方(其实不是！).一个简单的解决方案是使用 [ngrok](https://ngrok.com/) ，这基本上是一个隧道/端口转发软件，它的设置非常简单。

我们要做的就是[下载](https://ngrok.com/download) - >运行`ngrok http 5000`。就是这样！！

我们所做的只是将端口`5000`通过`http`暴露给外部。一旦您运行该命令，它将为外部世界(互联网)创建一个端点，如下所示

`http://9e844545.ngrok.io`

它能做的不止这些。要了解更多信息，你可以从这些[文档](https://ngrok.com/docs)开始。

现在您有了一个可以与外界共享的端点，我们可以使用这个 URL 并设置 webhooks，这样外部应用程序就可以将数据发送到这个端点。

-照片由来自 Pexels 的 Burak K 拍摄
-最初发布在 [Bitsmonkey](http://bitsmonkey.in/blog/2019/04/23/ngrok)
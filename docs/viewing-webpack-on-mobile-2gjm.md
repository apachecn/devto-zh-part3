# 在手机上查看 WebPack

> 原文：<https://dev.to/bhison/viewing-webpack-on-mobile-2gjm>

所以，你好，世界，或者你好 DEV——我想找个借口在这里发表我的第一篇文章，所以我将从今天早上开始快速解释我在开发方面的最新小喜悦。希望能对某个人有用！

在过去的一个月左右的时间里，我一直在使用 [WebPack](https://www.npmjs.com/package/webpack) ,并取得了相当大的成功，以至于我真的不再去想它了，它就是在工作。如果你还没有发现 WebPack 的成功之处，我强烈推荐[这个来自 Academind](https://www.youtube.com/watch?v=GU-2T7k9NfI&list=PL55RiY5tL51rcCnrOrZixuOsZhAHHy6os) 的关于它的 YouTube 系列，它教会了我所需要的一切，并从评论区得到了一点帮助(是的，有时它实际上是有用的，而不是令人恼火的)

如果您已经使用了 WebPack，那么您可能还会使用它的伙伴， [Webpack-Dev-Server](https://www.npmjs.com/package/webpack-dev-server) ，它的运行就像一个永远在线的构建，让您可以看到源文件中的实时更改，就像它们已经被构建一样。到目前为止，这对我来说非常有效，但是直到昨天我被要求确保所有东西都可以在移动设备上运行，我才开始在桌面上开发。

我过去对这类事情有过一点经验——我在局域网上制作过 nodemon 和 express expose 文件。事实证明，你可以在这里做同样的事情。一些谷歌搜索把我带到了 WebPack-Dev-Server GitHub 库上的这个已关闭的问题，它泄露了需要输入到命令行或 package.json 启动脚本中的魔法...

```
$ webpack-dev-server --port 8080 --hot --host 0.0.0.0 
```

Enter fullscreen mode Exit fullscreen mode

通过将 IP 设置为 0.0.0.0，您可以将您的机器作为目标，并将您的服务器暴露给本地网络。然后，你可以通过本地网络上的任何设备浏览你的网站，方法是导航到你的计算机的本地 IP 地址(how to:[windows](https://www.techwalla.com/articles/how-to-find-a-lan-ip)/[MAC](http://osxdaily.com/2010/08/08/lan-ip-address-mac/))，然后是`:8080`或任何你选择设置的端口。在我的例子中是`http://192.168.1.51:8080`

您还会注意到上面使用的`--hot`选项。这意味着，当你在你的机器上进行更新时，它将触发你正在监控的设备上的刷新——或者至少这是它应该做的。理论上，我发现它有点零碎，这意味着我必须重新启动服务器或像农民一样在我的 iPad 上点击刷新。

如果你有任何其他与 webpack 相关的技巧，请告诉我，我是这方面的新手，我真的很有兴趣听到任何其他我可能还没有探索过的酷功能。
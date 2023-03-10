# 让我们离线:确定互联网连接

> 原文：<https://dev.to/carmalou/let-s-take-this-offline-determine-internet-connection-4a7g>

*[本帖最初出现在 carmalou.com](https://carmalou.com/lets-take-this-offline/2017/10/13/lets-take-this-offline-pt-2.html)T3 上*

超过 10%的美国人没有家庭网络。因此，体谅互联网不稳定的用户非常重要。[没有互联网的用户实际上被困在孤岛上，无法与外界沟通。](https://carmalou.com/series/2017/09/24/lets-take-this-offline-pt1.html)

我们能做的最简单的事情之一就是检查网络连接。为此我们可以使用:`navigator.onLine`。这个函数内置在 JS 规范中，并且[具有很好的跨浏览器兼容性](https://caniuse.com/#search=navigator.on)——根据用户是否有网络连接返回一个布尔值。它可能看起来像这样:

```
if(navigator.onLine) {
  console.log("User is online!");
} else {
  console.log("User is not online. :(");
} 
```

这真的很方便，因为我们可以在运行任何 ajax 调用之前测试这一点，并为用户提供更好的体验，因为他们不会失去连接并突然遇到一系列连接错误。

我们可以设置一个事件监听器并持续监控网络连接。

```
window.addEventListener('offline', function(event) {
  console.log("We are offline! :(");
});

window.addEventListener('online', function(event) {
  console.log("We are online! :)");
}); 
```

虽然没有网络连接意味着您不在互联网上，但真实值可能会产生误导。仅仅因为你在一个网络上并不意味着你有互联网连接-例如，你可以在一个内部网络上，但该网络没有外部互联网连接。这可能返回 true。

那么我们能做些什么来检验我们的真理是否真的是真的呢？如果我们真的做到了，我们可以测试我们是否能访问图像。

```
fetch('some/img/url/here').then(
    function(response) {
      console.log(response);
    })
    .catch(function(err) {
      console.log(err);
    }); 
```

当我们查看响应时，我们可以看到状态代码。如果没有连接，catch 将返回类似于`TypeError: Failed to fetch`的错误。

现在你知道了！您可以使用`navigator.onLine`检查网络连接，为了确保您确实在线，您可以使用`fetch`提取图像并检查状态代码。

*[本帖最初出现在 carmalou.com](https://carmalou.com/lets-take-this-offline/2017/10/13/lets-take-this-offline-pt-2.html)T3 上*
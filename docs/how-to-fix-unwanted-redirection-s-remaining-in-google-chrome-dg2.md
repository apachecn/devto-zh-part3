# 如何修复谷歌浏览器中多余的重定向

> 原文：<https://dev.to/nabbisen/how-to-fix-unwanted-redirection-s-remaining-in-google-chrome-dg2>

### 简介

Google Chrome 有时会自动重定向到一个不需要的方向，而你的 web 服务器中没有这样的配置。
是因为谷歌 Chrome 在缓存中存储了 301 重定向历史。
该问题可以通过浏览器中的“开发人员工具”来修复，只需在禁用缓存的情况下创建一次请求。

### 如何

1.  在你的电脑上运行谷歌浏览器。
2.  通过以下任一方式打开“开发工具”:
    *   按 F12。
    *   使用浏览器菜单:“更多工具”-“开发者工具”。
3.  打开“网络”标签。
4.  选中“禁用缓存”。

    [![Google Chrome's Developer tools](img/343419a746160d287ba51cba96fcbf8f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gpMNWgEI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/veb0hjwozw655ihaa2e4.png)

5.  连接到您不想重定向到另一个的 FQDN。

    *   你会看到你想要的网站。
6.  取消选中“禁用缓存”以重置。

### 结论

当然，这是我的案子，我的失败。我把我的网络服务器配置成从 FQDN-a 到另一个 FQDN-b 的 301 重定向，进行临时测试。
然后我陷入了痛苦的状态，因为我所有与 FQDN 连接的尝试都把我带到了 FQDN😵
也许，我应该使用 302 临时重定向进行“测试”...🙂

感谢您的阅读。
快乐计算。
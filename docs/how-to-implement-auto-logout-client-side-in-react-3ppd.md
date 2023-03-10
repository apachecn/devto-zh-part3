# 如何在 react 中实现自动注销客户端

> 原文：<https://dev.to/skptricks/how-to-implement-auto-logout-client-side-in-react-3ppd>

帖子链接:[如何在 react](https://www.skptricks.com/2018/12/how-to-implement-auto-logout-client-side-in-react.html) 中实现自动注销客户端

本教程介绍了如何在 react 应用程序的客户端系统中实现自动注销系统。如今，大多数 web 应用程序都实现了自动注销系统，这有助于用户在忘记注销 web 应用程序会话的情况下，保护他们的安全数据免受未经授权的访问。

所以在本教程中，我们将分享 React 源代码来实现自动注销系统。在这个例子中，我们为用户会话维护两个时间戳。第一个时间戳有助于在用户 16 秒不活动时通知警报消息。
第二个时间戳有助于在用户 30 秒不活动时启动注销模块。
如何在 react 中实现自动注销客户端

[如何在 react 中实现自动注销客户端](https://www.skptricks.com/2018/12/how-to-implement-auto-logout-client-side-in-react.html)

[![](img/ad51eca2130f79dbb00302359cb779fb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jlM2bcJV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://4.bp.blogspot.com/-9Ewcb1fEJkQ/XA8yumRD2PI/AAAAAAAACQ4/er8qHRKoeQ0Y4ymzTQyXYc5jn8vJzZsmACLcBGAs/s640/logout.png)
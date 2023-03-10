# 一个完整的 PHP 项目的“实时重新加载”功能

> 原文：<https://dev.to/felipperegazio/a-complete-live-reload-feature-for-php-projects-in-a-single-class-380m>

> 警告:自从这篇文章发表以来，这个项目已经发生了很大的变化。它仍然在单个类中提供热重载特性，但是现在采用了 SSE(服务器发送事件)方法。更多信息请查看 github 文档

最近我制作了这个 PHP 类，用于我的个人项目和我工作的公司。我真的很喜欢 Vue(和其他框架)的热重新加载功能，我变得有点不习惯这个新功能，所以我写了这个类来以一种非常简单的方式为任何 php 项目添加一个动态重新加载功能。它允许你在编码时看到页面的动态变化，而不需要每次变化都刷新浏览器。默认情况下，该脚本将使您的项目选项卡对包含/必需文件、与打开的选项卡相关的 css 和 js 文件中的更改做出反应。每个浏览器，每个项目，一个文件。

用法非常简单:您必须在想要自动响应更改的源上调用 HotReloader。这些反应将发生在 js、css 和其他与当前页面相关的 php 文件上。你必须有一个布局文件，普通页脚或类似这样的东西在你的样板文件中。否则，您需要手动将 HotReloader()放入代码中。这里的例子必须放在你的页脚部分或你的标签后的某个地方。

最简单的开始方式是:需要这个类，实例化它，调用 init 方法。现在，在编码时保持页面打开，并且只编码。

```
require "../hotreloader.php";
$reloader = new HotReloader();
$reloader->init(); 
```

Enter fullscreen mode Exit fullscreen mode

您可以更改、扩展和调整此默认行为，并且可以根据您的需要配置该类以获得更好的体验。与我在 php 中见过的其他实时重载器不同，默认情况下，这不会帮助你的整个目录，而只会帮助与你当前页面相关的文件。我在大型项目和框架内部使用过这个类，就像 Cake Php 一样，它的行为真的很好接受，也很轻量级。该项目是一个测试版，要下载或阅读文档，请参见 Github 页面。贡献者真的很受欢迎；)

[https://github.com/felippe-regazio/php-hot-reloader](https://github.com/felippe-regazio/php-hot-reloader)

希望你喜欢！
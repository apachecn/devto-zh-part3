# PHP 依赖注入最简单的方法

> 原文：<https://dev.to/joshualjohnson/php-dependency-injection-the-easy-way-15al>

嘿伙计们，

大约 6 或 7 年前，我写了一个小小的 PHP 库，最终成为我为一个项目构建的框架的核心部分。我希望我的项目有一个好的依赖注入引擎，但是对当时可用的依赖注入引擎不满意。所以我决定做一个非常简单易懂的 DI 引擎。

[https://github.com/joshualjohnson/ulfberht](https://github.com/joshualjohnson/ulfberht)

我只是想分享一下，因为我已经有一段时间没有分享了。如果你正在为你的下一个 PHP 项目寻找一个好的 DI 引擎，这是一个好的解决方案。

我在考虑把它转移到我的组织([https://github.com/ua1-labs](https://github.com/ua1-labs))并再次正式支持它。这将取决于其他人是否认为有必要这样做。

未来功能更新:

*   添加一个 put($className，$object)方法，允许您在 DI 容器中放置和覆盖实例。
*   更新项目，使其成为消防族的一部分。将其重命名为 FireDI。
*   用 FireTest 添加单元测试。

你在阿迪需要什么特色？
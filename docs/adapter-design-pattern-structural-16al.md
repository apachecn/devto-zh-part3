# 适配器设计模式[结构]

> 原文：<https://dev.to/itscoderslife/adapter-design-pattern-structural-16al>

动机:当我们需要修改一个框架，但是我们不能或者不想修改它的接口，但是我们想使用它。适配器的目的是获取不兼容的接口，并使其适应我们需要的接口。

*   适配器应该被用来使设计好的东西工作。
*   仅当功能可用但接口不正确时，适配器才是合适的。
*   如果不能更改不兼容类型的源代码，请选择适配器。
*   适配器实际上是使不兼容的接口适应我们需要的接口。
*   如果可以修改实现，就不要使用适配器。
*   您还可以将适配器对象包装在一个专用的适配器类中，该类公开了我们需要的 API。

> 适配器模式将现有接口转换为另一个接口，这是使它与软件系统的其余部分兼容所必需的。

当我们需要集成一个与系统中的其他组件提供相似功能的组件，但该组件带有不兼容的接口时，这种模式非常有用。

示例:当我们的笔记本电脑上有一个 HDMI 端口，我们需要将其连接到带有 VGA 的显示器时，我们该怎么办？我们使用适配器或转换器。

比方说，你需要建立一个照片分享应用程序，编辑照片并在所有社交媒体上分享——但每个应用程序的界面因参数不同而不同。脸书有自己的参数列表，Twitter 有不同的设置，Tumblr 有不同的设置。因此，我们编写一个公共(适配器)接口，而不是为每个接口使用不同的接口。这可以保持你的应用程序代码的整洁和可读性。

**总结**:适配器模式将不兼容的接口转换成我们需要的接口。当集成不兼容的接口时，适配器可以使我们免于大量的重构工作。通过使用适配器，我们不必为了使用适配器(框架)的不兼容接口而重构代码。

我们犯的一个常见错误是试图修改一个不提供所需功能的组件。
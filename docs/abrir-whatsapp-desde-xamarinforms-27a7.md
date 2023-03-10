# Abrir WhatsApp desde Xamarin。形式

> 原文：<https://dev.to/luciomsp/abrir-whatsapp-desde-xamarinforms-27a7>

[![](img/0a1bf41e3a24cdb4248139a0cfa1dc0d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vN2W7ujz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2ACiJFlY5L1Wg1gLE2.jpg)

75%以上的用户或消费者使用智能手机，重点是通过 Whatsapp 进行交谈，因此，据了解，目前的应用程序往往在其联系方式中包括通过这种方式与某人互动的选项，或者更准确地说，在 WhatsApp 中包括 bot。

无论您选择哪一个选项，本文都会介绍我们在 xamarin . forms 上开发的应用程序如何在 WhatsApp 上打开一个聊天窗口，以便向特定号码发送消息。

对于此示例，我们将使用插件[xamparin . forms . openwhatsapp](https://www.nuget.org/packages/Xamarin.Forms.OpenWhatsApp)。

[![](img/1ac09a4925ca508cbfa2dc385169472f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eaSoaNNQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/589/1%2AifoseEvkfA-bQCQ9KCVe_g.png)

首先，我们将在 VS 2019 中生成一个新的空白表单项目:

[![](img/da60e0d98dd7ad031a8b131574d41054.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5H3611Fu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/498/1%2A2YMaWBcCWU6u00dxKgoy0g.png)

我们为其命名为 waxf

[![](img/ec40864100bc3e37b2da32944e32c4eb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8Tr7eTtX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/499/1%2Av8LFttzPrLYbWsUhJmCMDA.png)

我们选择了生成解决方案的路径:

[![](img/a69ae8e436aaec2bc92816ab1c288416.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4AYaskNe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/543/1%2ASXmCxcep5xP0Q0tJ66QF-g.png)

创建项目后，我们需要添加 nuget 软件包，方法是右键单击该解决方案，然后选择“添加”->“添加 nuget 软件包...”

[![](img/7c698665b7ee4b08be46635ba6d4597a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7I1MYkgJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/589/1%2A33fITBkIjmCwj_7tvv8qvA.png)

在弹出窗口中，键入∞并选择插件，如下图所示:

[![](img/cd65a6d283beb29d7de333b3bd70131b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--v4OI4Kbv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/468/1%2AUHXGn0CH68hSbDwoF6QbPA.png)

我们点击按钮

**XAML** 中的一个

在 UI 中，我们添加了一个具有 Clicked 属性的按钮:

**C#**

添加相应的用法:

使用 Xamarin。Forms . OpenWhatsApp

接下来，我们将使用聊天方法，打开作为电话号码和消息传递的参数。

private async void open whatsapp(object sender，EventArgs e)

{

尝试

{

聊天。Open("+52 55 X3 X8 XX 4X "，" envía este texto…VGGL ")；

}

catch(异常 ex)

{

await display alert(" Error "，ex。消息，“OK”)；
T17)}
T19】}

**IOS**

如果我们希望在 Apple 品牌的设备上同样起作用，则需要在 Info.plist 文件中添加以下键:**lsapplicationquecerchemes**。

lsapplicationquerieschemes

whatsapp

[![](img/86c339b78bc0e8bf7ae9e038ef04fd1b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DwBSXEX8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/425/1%2AbpNDlfGrr25OIZr6x4GpXQ.png)

到此为止，我们保存、汇编和测试。

**结果数据**

[![](img/1292ea1d28d114c0deb1de607c4f6c97.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--InanHlhB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/367/1%2ApExCtigWPcxCTePwShXRRw.png)

descar ga El código complete de mi[GitHub](https://github.com/LucioMSP/Xamarin.Forms.Examples/tree/master/WAXF)。

[![](img/64209aee6c977cfc980a0290171f81cb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xtnkUHzD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/318/1%2A4H6zBA4fXfVQYm7AqXpJWg.png)

下一个！

* * *
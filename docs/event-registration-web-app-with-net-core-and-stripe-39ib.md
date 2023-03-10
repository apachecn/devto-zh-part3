# 事件注册 Web 应用程序。网芯和条带

> 原文：<https://dev.to/ianknighton/event-registration-web-app-with-net-core-and-stripe-39ib>

(*我确实计划在某个时候回到我的[唱片目录](https://dev.to/ianknighton/lets-build-a-record-catalog-introduction-347)帖子。我在等着。我的生活最近变得很疯狂。这个项目落在我的盘子里，我想这将是一个好主意，因为我找不到任何类似的文件和分享。*)

这个项目的解决方案可以在我的 [GitHub](https://github.com/IanKnighton/RegistrationPage) 账户上找到。

我在爱达荷州的 Lava Hot Springs 经营一家老式大众野营车。在过去的 6 年里，它已经从我的几个朋友聚在一起的借口变成了这个州最大的聚会之一。由于年复一年的增长，我们已经开始为该活动进行预注册，以确定我们的人数并确保我们有空间。

# 问题

我需要一个简单的 web 表单，它可以接受一些输入，计算价格，处理支付过程，然后将数据存储在我可以访问的地方。过去，我们使用谷歌表单和贝宝发票的组合。它可以工作，但是非常笨拙，而且每一端都需要大量的手动输入。这还需要通过 PayPal 的系统，对于没有账户的人来说，这并不总是最容易的。

我听说过 [Stripe](https://stripe.com/) ，知道他们有一个. Net 库，并认为我可以让剩下的工作。

# 解

我从空的开始。Net Core 2.2 web app。

```
dotnet new web --name RegApp 
```

Enter fullscreen mode Exit fullscreen mode

这会创建一个空的应用程序。我真的更喜欢 MVC 模型，但我讨厌所有默认 MVC 项目附带的额外的 crud，所以我添加了库和必要的配置后记。

```
dotnet add package Microsoft.AspNetCore.Mvc 
```

Enter fullscreen mode Exit fullscreen mode

然后编辑`Startup.cs`使用默认的 MVC 路线。

```
namespace RegApp
{
    public class Startup
    {
        // This method gets called by the runtime. Use this method to add services to the container.
        // For more information on how to configure your application, visit https://go.microsoft.com/fwlink/?LinkID=398940
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddMvc();
        }

        // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
        public void Configure(IApplicationBuilder app, IHostingEnvironment env)
        {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }

            app.UseMvcWithDefaultRoute();
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

*向[我是培根](https://www.iambacon.co.uk/blog/create-a-minimal-asp-net-core-2-0-mvc-web-application)大喊，以获得如何做到这一点的指导。我提到这一点的次数比我愿意承认的要多。*

现在应用程序知道我们将使用 MVC 路线，我们可以添加`Models`、`Views`和`Controllers`文件夹。在`Views`文件夹下，创建一个名为`Home`的文件夹，并在其中创建一个名为`Index.cshtml`的文件。在`Controllers`文件夹中，创建一个名为`HomeController.cs`的文件。

减去必要的代码位，我们现在在 MVC 应用的架构上。

## 添加条纹

此时，您需要设置一个条带帐户。这是您获取 API 密钥的地方。你得到了一个测试密钥和一个活动密钥，这很好，因为你可以运行一系列迭代，确保一切正常。

目前，我们只需要将库添加到我们的项目中，并将配置添加到`Startup.cs`文件中。

```
dotnet add package Stripe.Net 
```

Enter fullscreen mode Exit fullscreen mode

然后更新`Startup.cs`文件以包含`using`语句和配置。

```
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Stripe;

namespace RegApp
{
    public class Startup
    {
        // This method gets called by the runtime. Use this method to add services to the container.
        // For more information on how to configure your application, visit https://go.microsoft.com/fwlink/?LinkID=398940
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddMvc();
        }

        // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
        public void Configure(IApplicationBuilder app, IHostingEnvironment env)
        {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }

            StripeConfiguration.SetApiKey("<YOUR TEST KEY>");

            app.UseMvcWithDefaultRoute();
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 楼出

### 车型

对于我的情况，我需要知道每晚将参加的成人和儿童的数量，以及他们的团队将带来的车辆数量。露营地向每位成人收费，但其他信息很重要，因此我们可以确保我们正确地组织了空间。

我们将创建一个名为`RegistrationModel.cs`的模型来处理这些数据，并允许 is 安全地传递这些数据。这个相同的模型将利用内置的`DataAnnotations`来给我们一些验证。

```
using System.ComponentModel.DataAnnotations;

namespace RegApp.Models
{
    public class RegistrationModel
    {
        [Required]
        public string Name { get; set; }

        [Required]
        [EmailAddress]
        public string Email { get; set; }

        [Required]
        [Range(0, 15)]
        public int FridayAdults { get; set; }

        [Required]
        [Range(0, 15, ErrorMessage = "Please enter a valid number!")]
        public int FridayChildren { get; set; }

        [Required]
        [Range(1, 15, ErrorMessage = "Please enter a valid number!")]
        public int Vehicles { get; set; }

        [Required]
        [Range(0, 15, ErrorMessage = "Please enter a valid number!")]
        public int SaturdayAdults { get; set; }

        [Required]
        [Range(0, 15, ErrorMessage = "Please enter a valid number!")]
        public int SaturdayChildren { get; set; }

        public int PerAdultCost { get; set; }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

为了处理支付，我们还需要能够来回传递一个 ID(稍后将详细介绍)。为了处理这个问题，我还创建了一个名为`PaymentModel.cs`的模型，它只有一个属性。是的，这本来可以用不同的方式处理，但是有了它，我就有能力在需要的时候移动东西。

```
namespace RegApp.Models
{
    public class PaymentModel
    {
        public string ChargeID { get; set; }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 观点

现在我们已经完成了模型，我们可以连接一些简单的视图来显示和修改数据。

首先，我们将处理好`Index.cshtml`，因为它将是工作完成的地方。我喜欢尽可能依靠 Razor/C#来让这个更干净。

```
@model RegApp.Models.RegistrationModel

<h2>Welcome to the Registration Page.</h2>
<h4>Please use this page to pre-register for this years event.</h4>
<p>The cost is $7 per adult, per night.</p>

@using (Html.BeginForm("Index", "Home", FormMethod.Post))
{
    <div>
        <label>Name</label>
        @Html.TextBoxFor(m => m.Name)
        @Html.ValidationMessageFor(m => m.Name)
    </div>
    <div>
        <label>Email Address</label>
        @Html.TextBoxFor(m => m.Email)
        @Html.ValidationMessageFor(m => m.Email)
    </div>
    <div>
        <label>Friday Night Adults</label>
        @Html.TextBoxFor(m => m.FridayAdults)
        @Html.ValidationMessageFor(m => m.FridayAdults)
    </div>
    <div>
        <label>Friday Night Children</label>
        @Html.TextBoxFor(m => m.FridayChildren)
        @Html.ValidationMessageFor(m => m.FridayChildren)
    </div>
    <div>
        <label>Saturday Night Adults</label>
        @Html.TextBoxFor(m => m.SaturdayAdults)
        @Html.ValidationMessageFor(m => m.SaturdayAdults)
    </div>
    <div>
        <label>Saturday Night Children</label>
        @Html.TextBoxFor(m => m.SaturdayChildren)
        @Html.ValidationMessageFor(m => m.SaturdayChildren)
    </div>
    <div>
        <label>Total Vehicles</label>
        @Html.TextBoxFor(m => m.Vehicles)
        @Html.ValidationMessageFor(m => m.Vehicles)
    </div>
    <div>
        <input type="submit" value="Submit" />
    </div>
} 
```

Enter fullscreen mode Exit fullscreen mode

我们还将添加一个名为`Payment.cshtml`的视图来处理到 stripe 的重定向。如果一切按计划进行，这个页面应该只是简单显示，所以我们不会投入太多。

```
@model RegApp.Models.PaymentModel

<script src="https://js.stripe.com/v3/"></script>

Redirecting to Stripe...

<script>
    var stripe = Stripe('<YOUR_STRIPE_PK_KEY>');
    stripe.redirectToCheckout({
        sessionId: '@Model.ChargeID',
    }).then(function (result) {
        result.error.message = "Oops! Looks like something went wrong. Please try again later."
    });
</script> 
```

Enter fullscreen mode Exit fullscreen mode

基本上这个页面只是使用 JavaScript 来传递，我主要是从 Stripe 快速入门指南中获取的。

### 控制器

这是所有繁重工作发生的地方。控制器处理所有适当的业务逻辑，以计算成本并传递数据。我将把`HomeController.cs`文件分解，但如果你想看完整个文件，可以在 [Git Repo](https://github.com/IanKnighton/RegistrationPage) 中找到。

控制器需要做的第一件事是能够处理初始登录页面加载。

```
public IActionResult Index()
{
    RegistrationModel model = new RegistrationModel();
    return View(model);
} 
```

Enter fullscreen mode Exit fullscreen mode

现在它有了一个“空”模型，并且向用户显示了我们的索引页面，用户可以填写表单并点击 submit。

这个控制器动作处理提交。

```
[HttpPost]
public IActionResult Index(RegistrationModel model)
{
    if (ModelState.IsValid)
    {
        List<SessionLineItemOptions> items = new List<SessionLineItemOptions>();
        if (model.FridayAdults > 0)
        {
            items.Add(new SessionLineItemOptions
            {
                Name = "Friday Night Adults",
                Description = "The amount of adults that will be camping with us on Friday Night.",
                Amount = 700,
                Currency = "usd",
                Quantity = model.FridayAdults
            });
        }
        if (model.FridayChildren > 0)
        {
            items.Add(new SessionLineItemOptions
            {
                Name = "Friday Night Children",
                Description = "The amount of children that will be camping with us on Friday Night.",
                Amount = 1,
                Currency = "usd",
                Quantity = model.FridayChildren
            });
        }
        if (model.SaturdayAdults > 0)
        {
            items.Add(new SessionLineItemOptions
            {
                Name = "Saturday Night Adults",
                Description = "The amount of adults that will be camping with us on Saturday Night.",
                Amount = 700,
                Currency = "usd",
                Quantity = model.SaturdayAdults
            });
        }
        if (model.SaturdayChildren > 0)
        {
            items.Add(new SessionLineItemOptions
            {
                Name = "Saturday Night Children",
                Description = "The amount of children that will be camping with us on Saturday Night.",
                Amount = 1,
                Currency = "usd",
                Quantity = model.FridayChildren
            });
        }
        if (model.Vehicles > 0)
        {
            items.Add(new SessionLineItemOptions
            {
                Name = "Vehicles",
                Description = "The amount of Vehicles you plan on bringing",
                Amount = 1,
                Currency = "usd",
                Quantity = model.Vehicles
            });
        }

        var options = new SessionCreateOptions
        {
            CustomerEmail = model.Email,
            PaymentMethodTypes = new List<string>
            {
                "card"
            },
            LineItems = items,
            SuccessUrl = "<YOUR SUCCESS PAGE>",
            CancelUrl = "<YOUR CANCEL PAGE>"
        };

        SessionService service = new SessionService();
        Session session = service.Create(options);

        PaymentModel paymodel = new PaymentModel();
        paymodel.ChargeID = session.Id;

        return View("Payment", paymodel);
    }

    return View(model);
} 
```

Enter fullscreen mode Exit fullscreen mode

这里发生了很多事情，所以我就把它分解一下。

首先要做的是确保模型是有效的。如果不是，我们将返回带有验证错误的索引页面，并显示给用户来纠正它们。这使我们不必处理(或试图处理)丢失数据的请求。

接下来，我们浏览模型并设置一个`SessionLineItemOptions`列表。这些基本上是行项目，当结帐完成时，显示在发票上给用户和您看。这里需要注意的一点是，Stripe 确实要求所有内容都有一个最小值$.01 才能通过。在我们的情况下，这不是一个问题。我写了一篇博客解释这件事，到目前为止，还没有人对这几枚硬币的丢失有异议。如果你想让它持平，你可以用逻辑从成人成本中减去。

最后，我们创建会话并提交给条带化。假设一切顺利，我们将从会话中收到带有`Id`的响应。将用于处理付款。一旦我们有了它，我们就返回支付视图，这个视图(正如我们前面看到的)只是将它们重定向到 stripe 来接受支付。

从这里开始，我们就无能为力了。用户去 Stripe 处理他们的付款，如果一切顺利，钱和信息将出现在你的 Stripe 账户中。

希望这有帮助！我知道当我寻找一个“快速”走查时，我找不到任何相近的东西。

稍后我会试着回来谈谈我是如何主持并发布它的。

**更新:**我写了另一篇文章，讲述了我如何用 Nginx 在 Linux 服务器上托管这个应用。这里可以找到。
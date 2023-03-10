# 5 个插件让你的 NativeScript 应用程序赚钱

> 原文：<https://dev.to/progress/5-plugins-to-monetize-your-nativescript-app-4bh2>

开发一个移动应用程序充满了乐趣和游戏，但最终，你必须得到报酬。虽然我们中的许多人都在为客户开发收费应用程序，或者作为我们公司战略移动计划的一部分，但仍有很大一部分开发人员在独自行动，追逐 Flappy Bird 2.0 的梦想。

除了从应用商店按下载次数收费之外，让我们来看看用各种 [NativeScript 插件](https://market.nativescript.org/)轻松赚钱的一些方法:

## 广告

我知道，我知道！没人喜欢广告！它们[威胁着要毁掉移动网络](http://developer.telerik.com/featured/the-webs-cruft-problem/)，并且对一些移动应用程序的侵入性太强。然而，我在这里建议，有一些方法可以在不破坏用户体验的情况下显示嵌入式广告。

以 iOS 上的 [Truecaller](https://itunes.apple.com/us/app/truecaller-spam-identification/id448142450?mt=8) 广告体验为例:

[![truecaller ads](img/e95cefdb929f6e6aec3b24f0ef86728e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--liNkgzzB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/rdlauer/articles/master/nativescript/monetize/truecaller.png)

*广告非常醒目，但不会影响你使用该应用的任何体验。如果你对广告感兴趣，你可以点击它。*

当然，通过使用 [NativeScript AdMob 插件](http://plugins.nativescript.org/plugin/nativescript-admob)，同样可以在 NativeScript 上完成。

例如，要安装 AdMob 插件，只需在 NativeScript 项目中发出`tns plugin add nativescript-admob`命令。创建横幅广告就像这段代码一样简单:

```
var admob = require("nativescript-admob");

admob.createBanner({
    // if this 'view' property is not set, the banner is overlayed on the current top most view
    // view: ..,
    testing: true, // set to false to get real banners
    size: size, // anything in admob.AD_SIZE, like admob.AD_SIZE.SMART_BANNER
    iosBannerId: "ca-app-pub-XXXXXX/YYYYYY", // add your own
    androidBannerId: "ca-app-pub-AAAAAAAA/BBBBBBB", // add your own
    // Android automatically adds the connected device as test device with testing:true, iOS does not
    iosTestDeviceIds: ["yourTestDeviceUDIDs", "canBeAddedHere"],
    margins: {
        // if both are set, top wins
        //top: 10
        bottom: 50
    }
}).then(
    function() {
      console.log("admob createBanner done");
    },
    function(error) {
      console.log("admob createBanner error: " + error);
    }
) 
```

Enter fullscreen mode Exit fullscreen mode

> 您可以在这个可克隆的 GitHub 库中找到额外的代码示例。

我们之前已经[写了大量](https://www.nativescript.org/blog/monetize-your-nativescript-apps-with-admob-(part-1---ios))关于使用 AdMob 的文章，Nic Raboy 也有关于使用 AdMob 与[普通 JavaScript NativeScript 应用](https://www.thepolyglotdeveloper.com/2016/03/monetize-with-google-admob-in-a-nativescript-mobile-app/)和 [Angular NativeScript 应用](https://www.thepolyglotdeveloper.com/2016/11/google-admob-nativescript-angular-2/)的指南。

## 应用内购买

当我们看到消费者回避一次性初始应用下载费用时，我们看到原生应用内购买平台的使用增加了。有什么比使用一个[跨平台应用内购买插件](http://plugins.nativescript.org/plugin/nativescript-purchase)更好的方式来利用苹果和谷歌的本地平台呢！？

[![in-app purchase and billing](img/33d63153100cd3228c388c03cf9fe620.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8lV9wOg5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/rdlauer/articles/master/nativescript/monetize/in-app-purchase.png)

安装带有`tns plugin add nativescript-purchase`的`nativescript-purchase`插件，在这段代码中，您可以看到将您提供的“产品”作为应用内购买服务的一部分是多么容易:

```
import { Product } from "nativescript-purchase/product";

purchase.getProducts().then((products: Array<Product>) => {
    products.forEach((product: Product) => {
        console.log(product.productIdentifier);
        console.log(product.localizedTitle);
        console.log(product.priceFormatted);
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

...购买具有以下特征的产品:

```
if (purchase.canMakePayments()) {
    // NOTE: 'product' must be the same instance as the one returned from getProducts()
    purchase.buyProduct(product);
} 
```

Enter fullscreen mode Exit fullscreen mode

> 请务必查阅[插件文档](http://plugins.nativescript.org/plugin/nativescript-purchase)，获取如何完成应用内购买的示例。

## 收取款项

如果您有产品或功能要销售，另一个选择是直接与在线支付提供商集成，如 PayPal、MolPay 或 Stripe:

[![paypal logo](img/1403c8327018e48bbe70fbb9a64df4ee.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MUyTY7to--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/rdlauer/articles/master/nativescript/monetize/paypal.png)

自 90 年代末以来，在线支付之王 [PayPal](https://www.paypal.com/) 使请求和接收支付变得很容易。通过使用用于 NativeScript 的 [PayPal 插件](http://plugins.nativescript.org/plugin/nativescript-paypal)，你可以将 PayPal 体验添加到你的 NativeScript 应用中。

创建 PayPal 购物再简单不过了:

```
function buyProduct(args) {
    // configure
    var payment = PayPal.newPayment()
        .setDescription('My product')
        .setAmount(59.79);

    // start checkout / payment
    payment.start(function(cbResult) {
        switch (cbResult.code) {
            case 0:
                // SUCCESS
                // pay key is stored in 'cbResult.key'
                break;
            case 1:
                // operation was CANCELLED
                break;
            case -1:
                // checkout FAILED
                break;
            case -2:
                // "unhandled exception"
                break;
        }
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

> 请注意，在撰写本文时，PayPal 插件仅支持 Android，但 iOS 实现[正在积极开发中](https://github.com/mkloubert/nativescript-paypal/issues/1)。

[![stripe logo](img/2dab5aafdfc84843279493ab5ce4f615.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TKC3a7tl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/rdlauer/articles/master/nativescript/monetize/stripe.png)

作为(相对)新的支付网关之一， [Stripe](https://stripe.com/) 因其无与伦比的用户体验而备受青睐。最近一个[全新的 NativeScript 插件为 Stripe](https://www.npmjs.com/package/nativescript-stripe) 发布了！

这个插件允许您使用`<stripe:CreditCardView>`元素将信用卡组件集成到您的 NativeScript 视图中。

或者，您可以直接使用 [Stripe 的 JavaScript API](https://stripe.com/docs/stripe.js?) ,因为它没有浏览器 DOM 依赖性。

[![molpay logo](img/107ca05869bbf5937d753e83c4fea815.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--A1auVSJ5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/rdlauer/articles/master/nativescript/monetize/molpay.png)

东南亚的 NativeScript 开发者可能最熟悉 [MOLPay](http://www.molpay.com/v3/) ，这是一个快速增长的支付网关，可以轻松接受来自多个国家和多种货币的支付。

用于 NativeScript 的 [MOLPay 插件提供了一种与 MOLPay 接口的简单方法，以及非常详细的文档。](http://plugins.nativescript.org/plugin/molpay-mobile-xdk-nativescript)

## 总结

NativeScript 插件生态系统每天都在增长。有了这样的插件，你可以从收集虚拟内裤，到？，立即获利！

*特别感谢[南方公园地精](https://www.youtube.com/watch?v=tO5sxLapAts)给我们所有人的启发:)*

[![south park underpants](img/377a1e329182fd69b515649c7a6ad6e8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yDOT9ChS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/rdlauer/articles/master/nativescript/monetize/underpants.jpg)
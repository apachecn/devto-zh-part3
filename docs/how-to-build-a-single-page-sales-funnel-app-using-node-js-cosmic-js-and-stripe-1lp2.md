# 如何使用 Node.js、Cosmic JS 和 Stripe 构建单页销售漏斗 App

> 原文：<https://dev.to/mtermoul/how-to-build-a-single-page-sales-funnel-app-using-node-js-cosmic-js-and-stripe-1lp2>

# 如何用 Node.js、Cosmic JS、Stripe 搭建单页销售漏斗 App

[![](img/3aecd26f4ab084eeb0c1237098680ce5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xWs7PSkb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cosmic-s3.imgix.net/fba84950-7534-11e9-97c3-f3f149e9cf49-smartmockupsmain.jpg%3Fw%3D1000)

什么是销售漏斗？为什么它很重要？销售漏斗是你网站的访问者在购买产品之前选择的一条路径。如果你不了解你的销售漏斗，你就无法优化它。建立一个伟大的销售漏斗可以影响访客如何通过漏斗，以及他们是否最终转化。在这个小演示中，我将向您展示如何使用 Node JS、Cosmic JS 和 Stripe 构建一个简单的单页销售漏斗来处理信用卡支付。

## TL；速度三角形定位法(dead reckoning)

*   [演示](https://cosmicjs.com/apps/single-page-sales-funnel)
*   [源代码](https://github.com/cosmicjs/nodejs-sales-page)
*   [条纹](https://stripe.com/docs/stripe-js/elements/quickstart/)
*   [宇宙 JS](https://cosmicjs.com/)

## 我为什么挑宇宙 JS

如果你需要快速自由地建立一个销售页面，你可以简单地注册一个免费的宇宙 JS 帐户，复制我的应用程序，定制它，你就大功告成了。你会在几分钟内准备好你的销售漏斗页面。如果您需要添加一个部分或特性，那么您可以简单地下载源代码，并更改该应用程序背后的任何标记或 JavaScript。

## 如何克隆自己版本的 app:

为了克隆此应用程序并拥有自己的自定义副本，您需要执行以下步骤:

*   注册一个免费的宇宙 JS 账户
*   登录宇宙 JS 账号
*   转到[桶](https://cosmicjs.com/buckets)页面
*   点击页面右上角的`Add New Bucket`按钮
*   选择`Choose an app below option`并滚动至应用程序列表
*   尝试找到`Sales Funnel`应用程序
*   打开应用程序后，点击`Install Free`按钮
*   您可以保留相同的标题，并点击`Install App`按钮进行确认
*   然后，您将被带到应用程序存储桶
*   然后你需要克隆 github repo。打开终端窗口并键入:

```
git clone https://github.com/cosmicjs/nodejs-sales-page
cd crowd-pitch
npm install 
```

*   编辑和更改位于`/crowd-pitch/.env.local`文件中的 Cosmic JS 和 Stripe API 密钥

```
COSMIC_BUCKET=your-bucket-slug
COSMIC_READ_KEY=your-cosmic-read-key
COSMIC_WRITE_KEY=your-cosmic-write-key
STRIPE_KEY=your-stripe-api-key 
```

*   在本地机器上运行应用服务器

```
# start the app
npm run server 
```

*   在 [从浏览器打开应用程序 http://localhost:3000](http://localhost:3000)

## 如何定制应用内容

要更改文本、图像和应用程序内容，您需要遵循以下步骤:

*   登录[宇宙 JS 仪表板](https://cosmicjs.com/buckets)
*   转到桶->众筹
*   进入页面->谷歌提款机
*   通过编辑网页每个部分的文本(如网页标题、页眉....
*   通过添加您自己的图像来更改页面图像。确保保持相同的图像标题和 slug。
*   点击保存并发布
*   这部分工作就像任何 CMS 系统，在那里你作出改变的后端和网站可以立即改变。

## 如何给这个应用添加新功能

这部分和下面将解释如何开发应用程序前端，以及如何深入挖掘定制更多的选项，如布局，css，颜色，以及从用户收集哪些字段。这个应用程序主要使用 Node JS 和 Stripe API 构建。所以让我们看一下 server.js 文件
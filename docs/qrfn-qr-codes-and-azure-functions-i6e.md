# qrfn : QR 码和 Azure 功能

> 原文：<https://dev.to/heyayush/qrfn-qr-codes-and-azure-functions-i6e>

### **qrfn:二维码和 Azure 功能**

只需在 [*查询参数中传递一段文字，即可生成*](https://en.wikipedia.org/wiki/Query_string) *[**二维码**](https://en.wikipedia.org/wiki/QR_code) 。*

[![](img/24adf6b0c704fcfe7749f91da628b9e4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0J8rXck---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2A3EQRtUmQCcOvzkwG) 

<figcaption>照片由[米娅·伊万诺夫](https://unsplash.com/@aka_opex?utm_source=medium&utm_medium=referral)于 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

在这篇博文中，我们将看到我到目前为止是如何使用二维码的。

*   了解 Azure Functions 如何成为你的建筑和业务中的一块乐高积木。
*   没有《权力的游戏》剧透，放心看每一部；p
*   请启用图像。

### **TL；博士**

> 1.  QR code.js —快速响应代码
>     
>     
> 2.  光栅化 HTML —可下载图像
>     
>     
> 3.  从查询参数中读取文本，并发送 QR 码作为响应。
>     
>     
> 
> 没听懂？浏览博客。

### 二维码？

我认为二维码是从“现实世界”到“互联网”的门户。我们看到二维码正以多种方式使用，如电影票、活动门票、支付、WIFI 网络登录等。

但是 QR 上没有完整形式的博客算什么呢？

被称为快速反应码。

[![](img/fe13ed4208ebd24e092e9570fc407780.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pVVJToD_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AUsxWbnYbgMGtOFw9-Dvi4g.jpeg) 

<figcaption>QChain - QR 启用钥匙扣</figcaption>

我们在一个实验中制作了 **QChain** ，这个实验是**启动一周**。我们在 **12 小时**想到一个主意，并在整个**周**执行它。

只要有一个带有失主有用信息的 QR，Qchain 就能给你找到丢失钥匙的希望。

为了聚集顾客，我们通过**众筹**筹集资金，并在一周后开始交付他们的产品。

我们曾经用 **3D 打印**表壳，把二维码打印在贴纸页上，剪下来贴在表壳上。我们不能自动化硬件，但是软件，当然我有 **Azure 功能**。

在人们过去向我们付费并提交信息的表格中，我将提交内容发送到 QRFn，以生成他们对活动的 QR(真棒 r8)。

[![](img/c0c3d9828a549ea36154f8034bd97291.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--L-INnL9R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/726/1%2A-kKYgluyWQkbFkOMs-lXLg.png)

此外，当我构建[**Cliniq**](https://cliniq.ml)* * * *时，我在构建患者预约服务时使用了 QRfn。Cliniq 背后的想法是跳过长时间等待看医生。

患者门户网站可以生成二维码与我们的数据库中使用的主键，并可以有一个预定时间的预约簿。

医生可以扫描 QR 码，并在字段中写下症状、说明和处方。医生门户只是在 QChain 中使用主键插入数据。

类似地，化学家可以扫描 QR，使用主密钥检索处方并提供药物。

QRfn 被认为是必不可少的。

### **天蓝色功能**

Azure Functions 是一种[无服务器](https://azure.microsoft.com/solutions/serverless/)计算服务，它使您能够按需运行代码，而不必显式地供应或管理基础架构。我们可以使用 Azure 函数运行一个脚本或一段代码来响应各种事件。

Azure function 很容易被想象成一个乐高积木，一个有助于用大象建造建筑物或船只的部件。当不存在任何相同的 API 时，它可以作为满足所有堆栈需求的关键 API。

此外，这个乐高积木可以是一艘船，表演性的，你不需要付费，直到你使用它(按执行模式付费)。

### 好了，让我们开始准备 QRfn 吧

你也可以使用并复制粘贴 github repo 中的代码，因为 images 不允许你这样做；p[*https://github.com/heyayushh/qrfn*T3】](https://github.com/heyayushh/qrfn)

### 1。编写 HTML 用模板来响应

*需要*中的东西

*   QRCode.js 制作 QR 码
*   栅格化 HTML——用 HTML [制作图像 https://medium . com/media/08638 F4 f 37 ed 6 e 9422 f 9 BC 72 B1 ed 61d 6/href](https://medium.com/media/08638f4f37ed6e9422f9bc72b1ed61d6/href)

**HTML 的*的***

 *div 占位符会拿着王座的国王(此处二维码)。屏幕只是占位符的容器。我还添加了一个下载按钮，它在单击时执行一个名为 download()的脚本，该脚本将下载文件。

[https://medium.com/media/d1dc26b9d223af040a1570ec8dad6b5e/href](https://medium.com/media/d1dc26b9d223af040a1570ec8dad6b5e/href)

#### **剧本**

*   [**QR code . js**](https://davidshimjs.github.io/qrcodejs/)[https://medium . com/media/2833 fa 2 f 9 b5 d 064 f 32 D1 ee 210 b 47 D2 Fe/href](https://medium.com/media/2833fa2f9b5d064f32d1ee210b47d2fe/href)

这会在 HTML 标签中生成一个 QR 码。

点击 可以了解更多[二维码. js *。*](https://davidshimjs.github.io/qrcodejs/)

[![](img/2806b8b4592c5436bac59c1120a79f1e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dmTk9AiO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/632/1%2A6scrQmwXQod3FLodbxHjLA.png)

#### *将 HTML 转换成 JS 字符串*

当我们从 JS 发送一个 HTML 页面给客户端时，我们应该转换它。由于 html 内容包含大量的'，"，

正确提及路径和文件名。

在 mac OS/ Linux 中，在与 HTML 页面相同的目录中运行命令。或者您也可以复制粘贴控制台输出。

```
node convertHtmlToString.js | cat >> var.txt 
```

现在，保存 var.txt/output 的内容供以后使用。

### Azure 函数

***要求***

*   Azure 门户

#### 1。创建功能应用程序

[![](img/e1749ca43fe7097bf0e8e2bc79ee1511.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YPGj3YFY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A1qkawESD4VCj3P_nvSQOlQ.png) 

<figcaption>1 步——创建一个 Azure 功能 App</figcaption>

#### 2。使用门户内开发

[![](img/e60f16c1bef134749fbf2fb52a0dee98.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FsKKlW70--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AFQbtLsJ-Zadw02nzpCUGuQ.png) 

<figcaption>第二步使用门户内开发</figcaption>

#### 3。创建一个 HTTP 触发器/ Webhook / API 函数

[![](img/efbcee48df5fb81397c7bcd64b9aab0d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VVwUJpOE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A5hrwum90TWnjlomJ7SR7Vg.png) 

<figcaption>第三步——创建函数</figcaption>

#### 4。表演时间到了。

[![](img/3859bcdd255f0400d2050829a7156a28.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LaNvIE1J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ANwu9gBbOf7tWzjD53ocBJg.png) 

<figcaption>第四步——写代码。</figcaption>

[![](img/6c98b65a6e913956ad36cf830231da95.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6HjhCeo5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AAh12fSfztxEFj4G9dWbOdA.png) 

<figcaption>代码😍</figcaption>

这段代码要说的东西太多了，只有 10 行。

***qrSite*** 是我们之前保存的 html 代码的变量。

***【genQr(text)***函数返回前面传递的文本的一个 Qr 作为参数(和我女朋友一样)。

***context.res*** 是一个成功请求的 azure 函数的响应体(json)。

**保存**和**运行**测试段中的*代码*或**邮递员**添加如下文本:

*【https://<****【你的功能应用程序】***>。azure sites . net/API/<***你的功能名称* ***文字****= thanku 4 阅读 mylogloplclap***

并且它会用一个带有*二维码生成器*的 **HTML** ***网站*** 来响应。

[![](img/2f7a062e95d40d585a122e489c33bfbb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7aH1TGcp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A3plRNv0lCBrX-nXezUSDow.png)

我们开始吧，我们有七国之王，WiFi 密码的保护者， **QRfn** 。

了解更多信息:

[https://functions.azure.com](https://functions.azure.com)/

[https://imaginecup.microsoft.com/en-us/Events?id=0](https://imaginecup.microsoft.com/en-us/Events?id=0)

这是给 [QRfn](https://github.com/heyAyushh/qrfn) 的暗号，愿 4 号与你同在。

[https://medium . com/media/D4 c80c 9 c56 F3 a 23 D8 d290 ce 9 C7 fc 71 B7/href](https://medium.com/media/d4c80c9c56f3a23d8d290ce9c7fc71b7/href)*
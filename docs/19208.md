# 如何集成 Paystack 支付系统

> 原文：<https://dev.to/ijsucceed/how-to-integrate-paystack-payment-system-with-php-5a8m>

在本指南中，您将学习如何在您的网站上集成 Paystack 支付系统。

本指南内容全面，因此您应该仔细阅读每一条信息以获得最大产出。

Paystack 是一个面向非洲的现代在线和离线支付平台。

在开始整合 Paystack 之前，你需要一个 [Paystack 账户](https://dashboard.paystack.co/#/signup)。如果你还没有的话，现在就创建一个[免费账户](https://dashboard.paystack.co/#/signup)。

Paystack API 让您可以访问几乎所有您可以在帐户仪表板上使用的功能，并允许您在应用程序中扩展这些功能。它力求 RESTful，并围绕您将与之交互的主要资源进行组织——除了几个明显的例外。

创建帐户后，下一步是登录您的新 Paystack 帐户。在你的仪表盘上，你会找到你的 ***公开*** 和 ***秘密*** 钥匙。对于本指南，我们将使用 ***公钥*** 和 ***私钥*** 。

## 让鱼跃进来

Paystack 有三种主要的方法来整合他们的 API，并在您的网站上收取费用。但是本指南只解释了两种方法。第三种方法是纯 JavaScript，我将在另一篇文章中完全讨论。

1.  [内联薪酬体系](#inline)
2.  [工资标准](#standard)

使用两者中的任何一个取决于你想做什么。每一个都有自己的用户和开发者体验。

## 行内工资单

这种方法提供了一种简单、安全、方便的网上支付流程。它可以与一行代码集成，从而使它成为开始接受付款的最简单的方式。它还可以在同一页面上开始和结束支付流程，从而消除重定向疲劳。

现在，下面的代码显示了一个 Paystack 支付按钮。

```
<form>
  <script src="https://js.paystack.co/v1/inline.js"></script>
  <button type="button" onclick="payWithPaystack()"> Pay </button> 
</form> 
```

Enter fullscreen mode Exit fullscreen mode

付费按钮还没有达到你的预期。所以如果你点击它，什么也不会发生。为了让它工作，你需要在表单下面添加`payWithPaystack()` Javascript 函数。

下面是 Paystack 提供的`payWithPaystack`函数。

```
<!-- place below the html form -->
<script>
  function payWithPaystack(){
    var handler = PaystackPop.setup({
      key: 'paste your key here',
      email: 'customer@email.com',
      amount: 10000,
      ref: ''+Math.floor((Math.random() * 1000000000) + 1), // generates a pseudo-unique reference. Please replace with a reference you generated. Or remove the line entirely so our API will generate one for you
      metadata: {
         custom_fields: [
            {
                display_name: "Mobile Number",
                variable_name: "mobile_number",
                value: "+2348012345678"
            }
         ]
      },
      callback: function(response){
          alert('success. transaction ref is ' + response.reference);
      },
      onClose: function(){
          alert('window closed');
      }
    });
    handler.openIframe();
  }
</script> 
```

Enter fullscreen mode Exit fullscreen mode

您需要用 Paystack 帐户设置中的公钥替换`key:`值“*将您的密钥粘贴到此处*”。如果登录，你可以在这里找到钥匙[。](https://dashboard.paystack.com/#/settings/developer)

> 请注意，与 inline 一起使用的密钥是公钥，而不是私钥

如果你做得正确，点击支付按钮，一个漂亮的 Paystack 支付界面将弹出。因为您正在测试支付 API，所以您应该使用测试卡。

> 要使用测试卡，应使用 ***测试公钥*** 代替。并且永远不要忘记在直播网站上用 ***直播公钥*** 替换 ***测试公钥*** 。

[![paystack payment form](img/3bedeed6b4b32ad57bcc04b7bc2ffc45.png)](https://res.cloudinary.com/practicaldev/image/fetch/s----xMo72e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ijsucceed.com/uploads/paystack_ui.png)

当支付成功时，您的浏览器将显示一个提示，指示交易成功，并带有一个参考键。

[![paystack payment form](img/842652abb12ff59edef9382ff793e381.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YREm20aX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ijsucceed.com/uploads/paystack_integration_ui_testcard.png)

使用这种内联方法，一切都在一个页面上进行。另外，请注意`callback`和`onClose`对象关键点。如果支付成功，`callback`允许您在回调函数中控制用户体验。例如将用户重定向到感谢页面。

## 工资单标准

这是在您的 web 应用程序上收款的标准方式。标准方法是集成到 php web 应用程序中的更好、更安全的方法。

现在，为了让这种方法在您的服务器上工作，您需要确认您的服务器可以结束 TLSv1.2 连接。大多数最新的服务器都有这种能力。如果您在 web 服务器上，如果遇到任何 SSL 错误，请联系您的服务提供商寻求指导。

对于这种方法，您需要创建两个新文件。

```
 initialize.php
   callback.php 
```

Enter fullscreen mode Exit fullscreen mode

## 初始化一笔交易

将下面的代码粘贴到`initialize.php`和
中

```
<?php
$curl = curl_init();

$email = "your@email.com";
$amount = 30000;  //the amount in kobo. This value is actually NGN 300

// url to go to after payment
$callback_url = 'myapp.com/pay/callback.php';  

curl_setopt_array($curl, array(
  CURLOPT_URL => "https://api.paystack.co/transaction/initialize",
  CURLOPT_RETURNTRANSFER => true,
  CURLOPT_CUSTOMREQUEST => "POST",
  CURLOPT_POSTFIELDS => json_encode([
    'amount'=>$amount,
    'email'=>$email,
    'callback_url' => $callback_url
  ]),
  CURLOPT_HTTPHEADER => [
    "authorization: Bearer sk_test_36658e3260b1d1668b563e6d8268e46ad6da3273", //replace this with your own test key
    "content-type: application/json",
    "cache-control: no-cache"
  ],
));

$response = curl_exec($curl);
$err = curl_error($curl);

if($err){
  // there was an error contacting the Paystack API
  die('Curl returned error: ' . $err);
}

$tranx = json_decode($response, true);

if(!$tranx['status']){
  // there was an error from the API
  print_r('API returned error: ' . $tranx['message']);
}

// comment out this line if you want to redirect the user to the payment page
print_r($tranx);
// redirect to page so User can pay
// uncomment this line to allow the user redirect to the payment page
header('Location: ' . $tranx['data']['authorization_url']); 
```

Enter fullscreen mode Exit fullscreen mode

`initialize.php`将使用 paystack API 初始化您的客户交易，并将用户重定向到 paystack 支付页面。

> 在实时服务器上，用您自己的实时密钥替换测试密钥。查找带有注释*‘用您自己的测试密钥替换它’*的行，并将 sk_test_xxxxxxxxx 删除到您的秘密密钥中。

请注意，`$email`和`$amount`是客户的电子邮件地址和他们要支付的金额，而`$callback_url`是客户付款后将被重定向到的 URL。

将客户带回你的网站是标准方法的重要部分，所以不要忘记将`$callback_url`改为你的应用程序。

电子邮件和金额可以通过表格或任何你想要的方式收集。

> `$amount`在尼日利亚 Kobo，所以在你向顾客收取的任何金额上都要加上两个零。例如 1000 英镑兑换 100000 英镑

你可以使用这个[货币工具](https://walletinvestor.com/converter/ngn/kobocoin/)来精确计算复杂的金额。

当客户输入他们的卡详细信息时，Paystack 将验证卡并从卡中收费。成功后，它将重定向回您在初始化交易时设置的`callback_url`或您仪表板上的:[https://dashboard.paystack.co/#/settings/developer](https://dashboard.paystack.co/#/settings/developer)。

> 如果您的`callback_url`未设置，您的客户会看到一条“交易成功”消息，没有任何重定向。

### 验证交易

现在，因为回调是在代码中指定的，所以您需要设置 callback.php。

在`callback.php`里面输入下面的代码

```
<?php

$curl = curl_init();
$reference = isset($_GET['reference']) ? $_GET['reference'] : '';
if(!$reference){
  die('No reference supplied');
}

curl_setopt_array($curl, array(
  CURLOPT_URL => "https://api.paystack.co/transaction/verify/" . rawurlencode($reference),
  CURLOPT_RETURNTRANSFER => true,
  CURLOPT_HTTPHEADER => [
    "accept: application/json",
    "authorization: Bearer SECRET_KEY",
    "cache-control: no-cache"
  ],
));

$response = curl_exec($curl);
$err = curl_error($curl);

if($err){
    // there was an error contacting the Paystack API
  die('Curl returned error: ' . $err);
}

$tranx = json_decode($response);

if(!$tranx->status){
  // there was an error from the API
  die('API returned error: ' . $tranx->message);
}

if('success' == $tranx->data->status){
  // transaction was successful...
  // please check other things like whether you already gave value for this ref
  // if the email matches the customer who owns the product etc
  // Give value
  echo "<h2>Thank you for making a purchase. Your file has bee sent your email.</h2>";
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你按照正确的步骤。你会得到下面的结果。

[!['paystack standard demo'](img/afd90c8410ad875841a271f88ab5bff6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--abCZDZKk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgflip.com/3d97so.gif)

如果你遇到下面的错误。

```
API returned error: Transaction reference not found 
```

Enter fullscreen mode Exit fullscreen mode

然后确保`callback.php`中的 SECRET_KEY 与`initialize.php`中使用的 SECRET _ KEY 相同，并且回调 URL 应该是一个活域。

恭喜你，你刚刚将 paystack 支付集成到你的应用程序中。

> 有什么问题，你可以发表意见。如果事情紧急，你可以在推特上给我发消息。

## 提示

转到仪表板>设置> webhook/keys 获取您的公开密钥和秘密密钥，用于实时和测试。

实时密钥用于生产目的。而公开是为了测试的目的。

要在 paystack 上启用实时模式，您需要提交您的业务详情。
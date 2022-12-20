# PHP 到 XML API

> 原文：<https://dev.to/mutale85/php-to-xml-api-5hg3>

大家好，
我有 php 数据要发送给我的 API 提供者，他要求数据采用 XML 格式，如下所示:

我从有“立即支付”按钮的表单中获取数据。

if(is set($ _ POST[' pay _ now ']){
$ userid = $ _ POST[' user-id ']；

$ total _ amount = $ _ POST[' total _ amount ']；

$ currency = $ _ POST[' currency ']；

$ first name = $ _ POST[' first name ']；

$ last name = $ _ POST[' last name ']；

$ email = $ _ POST[' email ']；

$ phone = $ _ POST[' phone ']；

$ reasons = $ _ POST[' reasons ']；

$ application _ id = $ _ POST[' application _ id ']；

$ order _ id = PASSWORD _ hash($ application _ id，PASSWORD _ DEFAULT)；

$ token = " XXXXX

$ payUrl = "[https://secure1.sandbox.directpay.online/payv2.php?ID= "。$ token](https://secure1.sandbox.directpay.online/payv2.php?ID=%22.%24token)；
$ company ref = " XXXXX "；

$ company refunique = " olord 11 of 2273 host "；

$ redirect URL = "[www.mydomain.com/example?success=".$CompanyRefUnique."&恭喜=。$CompanyRef。"&用户= "$ userid](http://www.mydomain.com/example?success=)；

www.mydomain.com/example?paymentdecline=".$CompanyRef."&用户= "。$ userid；

$PTLtype = 20。"分钟"；

$ DefaultPayment = " MO

$ ServiceDescription = $ reasons

$ ServiceType = " 5525

$ service date = date("yⓜ️d:hⓜ️”)

$datainput = '

'。$total_amount'

```
 <PaymentCurrency>'.$currency.'</PaymentCurrency> 
```

Enter fullscreen mode Exit fullscreen mode

。$RedirectURL。

。$DeclinedURL。

```
 <CompanyRefUnique>'.$CompanyRefUnique.'</CompanyRefUnique>

                        <CompanyRef>'.$CompanyRef.'</CompanyRef> 
```

Enter fullscreen mode Exit fullscreen mode

。$PTLtype。

```
 <customerFirstName>'.$firstname.'</customerFirstName>

                        <customerLastName>'.$lastname.'</customerLastName> 
```

Enter fullscreen mode Exit fullscreen mode

。$电子邮件。

```
 <DefaultPayment>'.$DefaultPayment.'</DefaultPayment> 
```

Enter fullscreen mode Exit fullscreen mode

’。$ServiceType。
’。$ServiceDescription。
’。$ServiceDate。

```
</Services> 
```

Enter fullscreen mode Exit fullscreen mode

；

```
 $ch = curl_init($payUrl);

            curl_setopt($ch, CURLOPT_URL, $payUrl);

            curl_setopt($ch, CURLOPT_HTTPAUTH, CURLAUTH_ANY);

            curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, false);

            curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, 0);

            curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);

            curl_setopt($ch, CURLOPT_SSLVERSION,6);

            curl_setopt($ch, CURLOPT_HTTPHEADER, array('Content-Type: text/xml'));

            curl_setopt($ch, CURLOPT_POSTFIELDS, $datainput);

            $response = curl_exec($ch);

            curl_close($ch); 
```

Enter fullscreen mode Exit fullscreen mode

}

我没有得到任何错误，但没有得到一个**重定向到沙箱**的网址。我怎样才能让 Paynow 按钮这样工作呢？
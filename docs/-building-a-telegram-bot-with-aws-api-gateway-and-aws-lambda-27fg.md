# ðç¨ AWS API ç½å³å AWS Lambda æå»ºçµæ¥æºå¨äºº

> åæï¼<https://dev.to/nqcm/-building-a-telegram-bot-with-aws-api-gateway-and-aws-lambda-27fg>

[![Telegram bot with AWS Lambda and AWS API Gateway](img/ac7c3f87f6a9efb2ab1aff555ceae841.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9-9iaVoy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.naveeraashraf.com/img/telegram-bot-with-aws-lambda.png)

å¨è¿å»çä¸å¹´éï¼æå¶é äºå¾å¤æºå¨äººãæä½¿ç¨ Chatfuel å Flowxo ç­æå¡å¶ä½äºæºå¨äººï¼æç¨ Python å Nodejs ä»å¤´å¼å§å¶ä½äºæºå¨äººï¼æè¿ä½¿ç¨äºåå¾®è½¯ Bot Framework è¿æ ·çæ¡æ¶ãæè¿ä¸ºæçä»å¤´ç¼ç çæºå¨äººè¯éªäºä¸åçé¨ç½²éé¡¹ï¼åæ¬ VPSãweb æå¡å¨åæ æå¡å¨æ¶æã

å¨ææè¿äºéé¡¹ä¸­ï¼æä¸æ­åå°[æ æå¡å¨](https://en.wikipedia.org/wiki/Serverless_computing)é¨ç½²ãå¨è®¸å¤æåµä¸ï¼ä½¿ç¨æ æå¡å¨è®¡ç®æ¶æä¸ä»éå¸¸ç»æµï¼èä¸ç»´æ¤ææ¬ä¹éå¸¸ä½ãä½ æ ¹æ¬ä¸ç¨æå¿ç»´æ¤ä¸ä¸ªæå¡å¨ãåªè¦åä¸ä½ çä»£ç ï¼ä¸ä¼ å°äºç«¯ï¼ç¶åä½ å°±å¯ä»¥æå®å¿å¾ä¸å¹²äºåäºã

## å¸¦æ AWS API ç½å³å AWS Lambda ç Echo Telegram bot

å¨æ¬æåä¸­ï¼æå°æ¼ç¤ºä½¿ç¨ AWS API Gateway å AWS Lambda å¶ä½ä¸ä¸ªç®åçæ æå¡çµæ¥æºå¨äººï¼å®å¯ä»¥ååºç¨æ·è¾å¥çææåå®¹ãä½æ¯é¦åï¼æä»¬å°ä½¿ç¨ä¸äºæå¡ã

### ä»ä¹æ¯ API ç½å³

Amazon API Gateway æ¯ä¸ä¸ªå¬å±å¯ç¨çç«¯ç¹ï¼ç¨äºè¿è¡å¨ AWS LambdaãAmazon EC2 æå¶ä»å¯å¬å¼å¯»åç web æå¡ä¸çä»£ç ãå®å¸®å©æä»¬ä»¥å®å¨åå¯æ©å±çæ¹å¼äº¤ä»ç§»å¨å web åºç¨ç¨åºåç«¯ã

### ä»ä¹æ¯ AWS Lambda

AWS Lambda æ¯ä¸é¡¹æå¡ï¼å®è®©æä»¬å¨äºä¸­åå»º[æ ç¶æ](https://en.wikipedia.org/wiki/Stateless_protocol)åè½(æä»£ç å),å¯ä»¥éè¿å¤§éè§¦åå¨è°ç¨(æè¿è¡)ãé¤äº AWSï¼è¿æè®¸å¤å¶ä»æå¡è®©æä»¬åå»ºäºåè½ï¼æ¯å¦[è°·æ­äºåè½](https://cloud.google.com/functions/)å [Azure åè½](https://cloud.google.com/functions/)ã

æºå¨äººæèå¤©æºå¨äººæ¯è¿ç§è®¡ç®æå¡ççæ³åéï¼å ä¸ºæ¬è´¨ä¸èå¤©æºå¨äººåªæ¯ä¸æ®µéè¿ç¨æ·äº¤äºè§¦åçä»£ç ãè®¸å¤æµè¡çèå¤©æºå¨äººå¶ä½å¹³å°å·²ç»å¨ä½¿ç¨äºåè½ä½ä¸ºå¶æå¡çåºç¡ã

ä»ç»å®æ¯ï¼è®©æä»¬å¼å§å§ï¼

### å¨çµæ¥ä¸æ³¨åä¸ä¸ªæºå¨äºº

æç§è¿ä¸ªæåï¼ä½ éè¦ä¸ä¸ªçµæ¥æºå¨äººãå¨ Telegram ä¸­æ³¨åä¸ä¸ªæ°ç bot ççå¾ç®åãåªéå Telegram èªå·±ç bot [BotFather](https://t.me/BotFather) åéä¸ä¸ªâ/newbotâå½ä»¤ï¼ç¶åæç§æç¤ºæä½å³å¯ãä½ ä¼æ¶å°ä¸ä¸ªæºå¨äººä»¤çï¼å¤å¶è¿ä¸ªä»¤çï¼å¹¶æå®æ¾å¨æè¾¹ã

æ¨å¯ä»¥èªå®ä¹æºå¨äººçæè¿°åâå³äºâé¨åï¼ä»¥åéè¿æºå¨äººç¶äº²æ´æ¹æºå¨äººçä¸ªäººèµæå¾çãä¸æ¦ä½ å¯¹ä½ çæºå¨äººçè®¾ç½®æ»¡æï¼ä½ å°±å¯ä»¥ç»§ç»­äºã

Telegram bot çå·¥ä½æ¹å¼æ¯ï¼å½ç¨æ·åæä»¬ç bot å¸æ·åéæ¶æ¯æ¶ï¼Telegram ä¼å°è¯¥æ¶æ¯åéç»æä»¬çä»£ç ãæä»¬çä»£ç å¤çè¯¥æ¶æ¯ï¼å¨å®æææéè¦åçäºæåï¼å®åçµæ¥æå¡å¨ååä¸æ¡æ¶æ¯ï¼ç¶åè¯¥æ¶æ¯è¢«åéç»ç¨æ·ã

æä»¬æä¸¤ç§æ¹æ³å¯ä»¥ä» Telegram æå¡å¨è·å¾æ°çæ´æ°ã

1.  é¿è½®è¯¢ï¼æä»¬çä»£ç ä¸æ­è¯¢é®çµæ¥æå¡å¨æ¯å¦ææ°çæ´æ°ã
2.  è®¾ç½® webhooksï¼å¶ä¸­æä»¬è¦æ±çµæ¥æå¡å¨éè¿ HTTP è¯·æ±åæä»¬åéæ¶å°çä»»ä½æ°æ¶æ¯/æ´æ°ã

è®¾ç½® webhooks æ¯æ¥æ¶æ°æ´æ°çæèµæºåå¥½åå¯ä¼¸ç¼©çæ¹å¼ãè¿å°±æ¯æä»¬å°è¦åçãä½ä¸ºæ­¤ï¼æä»¬éè¦ä¸ä¸ªå®å¨çç½åï¼çµæ¥æå¡å¨å¯ä»¥æ¨éæææ´æ°ãè¾å¥ AWS API ç½å³ã

### åå»º API ç½å³

å¦æä½ æ²¡æäºé©¬éè´¦æ·ï¼æ³¨åä¸ä¸ªãä½ éè¦æä¾ä½ çä¿¡ç¨å¡è¯¦ç»ä¿¡æ¯ï¼ä½äºé©¬éæä¸ä¸ªéå¸¸å¥½çåè´¹å±ï¼æç§è¿ä¸ªæååºè¯¥ä¸ä¼è±è´¹ä½ ä»»ä½ä¸è¥¿ã

ç»å½å° [AWS æ§å¶å°](https://aws.amazon.com)å¹¶ä»æå¡ä¸­æç´¢ API ç½å³ã

ç¹å»âåå»º APIâ

[![Create new API](img/5693635c26704b2c66374482cfeff0c4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mZ0yp3Sl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.naveeraashraf.com/img/telegram-aws-1.jpg)

ç»ä½ ç API ä¸ä¸ªåç§°åæè¿°ï¼ç¶åç¹å»åå»º APIã

[![Settings of the new API](img/07cc47272ffb475b09e88e8fe94996a9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ILhHqlra--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.naveeraashraf.com/img/telegram-aws-2.jpg)

å¨ä¸ºè¿ä¸ª API å®ä¹ä»»ä½èµæºä¹åï¼è®©æä»¬é¦ååå»ºæä»¬ç Lambda å½æ°ã

### åå»º lambda å½æ°

ç»å½å° [AWS æ§å¶å°](https://aws.amazon.com)å¹¶ä»æå¡ä¸­éæ© Lambdaãç¹å»âåå»ºåè½âã

[![Click Create Function](img/2b1d9c6f78f4ea3d096d6d2c9b954dd0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IuLryTie--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.naveeraashraf.com/img/telegram-aws-3.jpg)

ç°å¨éæ©âä»å¤´å¼å§åä½âéé¡¹ãä¸æ¦ä½ ä¸ç´éµå¾ªè¿ä¸ªæåï¼ä½ å¯ä»¥å°è¯æ¢ç´¢äºé©¬éæä¾çä¸åèå¾ã

[![Choose author from scratch](img/b45401a236ef422f4835d7e279cb2953.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2pEE88cA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.naveeraashraf.com/img/telegram-aws-5.jpg)

ç»ä½ çå½æ°èµ·ä¸ªåå­ï¼éæ©ä½ æåæ¬¢çè¿è¡æ¶(å¨æ¬æåä¸­éæ© Python3.6)ã

[![Settings for Lambda function](img/448dddb29c89f7a8abf43feeaf6f938b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NRQv3iQ4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.naveeraashraf.com/img/telegram-aws-4.jpg)

æ¯ä¸ª Lambda å½æ°é½æä¸ä¸ªä¸ä¹å³èç IAM è§è²(æ§è¡è§è²)ãå¨åå»º Lambda å½æ°æ¶æå® IAM è§è²ãæ¨æäºè¯¥è§è²çæéå³å®äº AWS Lambda å¨æ¿æè¯¥è§è²æ¶å¯ä»¥åä»ä¹ãä½ å¯ä»¥å¨è¿ééè¯»æ´å¤å³äºç®¡çæé[çåå®¹ã](https://docs.aws.amazon.com/lambda/latest/dg/intro-permission-model.html#lambda-intro-execution-role)

åºäºæ¬æåçç®çï¼æä»¬å°ä»ä¸æèåä¸­éæ©âä»æ¨¡æ¿åå»ºæ°è§è²âãä¸ºæ¨çè§è²å½åï¼ç¶åå¨âç­ç¥æ¨¡æ¿âä¸æåè¡¨ä¸­æç´¢âåºæ¬âãéæ©âåºæ¬ Lambda @ Edge æéâãç¶åç¹å»âåå»ºåè½âã

[![Select lambda role](img/607542ae367eeb1ff1970e104367ff6d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zRnQMCvS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.naveeraashraf.com/img/telegram-aws-13.jpg)

è¿å°å¸¦ä½ å°ä½ çåè½ãåä¸æ»å¨ï¼çæä¸åçéé¡¹ãæ¨éè¦ç¹å«æ³¨æå ä¸ªéé¡¹:

1.  ç¯å¢åé-ä»£ç ä½¿ç¨çä»»ä½ç¯å¢åéé½å¿é¡»å¨è¿éå£°æã
2.  åºæ¬è®¾ç½®ï¼æ¨å¯ä»¥å¨å¶ä¸­æ´æ¹åéçåå­åè¶æ¶ãè¯·æ³¨æï¼æ´æ¹è¿äºè®¾ç½®å¯è½ä¼å½±åææ¬ãä½ å¯ä»¥å¨è¿éäºè§£æ´å¤å³äº Lambda å®ä»·çä¿¡æ¯ã

[![Basic settings for Lambda](img/b79ad1db15d317b620976edf3b7db4a1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0q_nInm9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.naveeraashraf.com/img/telegram-aws-6.jpg)

å¨æä»¬ç»å½æ°æ·»å ä»»ä½ä»£ç ä¹åï¼è®©æä»¬åç»å®æ·»å ä¸ä¸ªè§¦åå¨ãæä»¬å¸ææ¯æ¬¡æäººå¨ Telegram ä¸åæä»¬çæºå¨äººåéæ¶æ¯æ¶ï¼Telegram æå¡å¨åºè¯¥å¨æç®¡æä»¬çä»£ç æ¶å°æ¶æ¯æ¨éå°æä»¬ç URLãæ¢å¥è¯è¯´ï¼æä»¬æ³è¦ä¸ä¸ª HTTP GET/POST è¯·æ±æ¥è§¦åæä»¬çä»£ç ï¼æä»¬å°éè¿æä»¬ç API ç½å³æ¥å®ç°è¿ä¸ç¹ã

æ¨ä¹å¯ä»¥ä» Lambda èªå·±çé¡µé¢å Lambda å½æ°æ·»å è§¦åå¨ï¼ä½ç°å¨æä»¬å°åå¾ API ç½å³é¡µé¢ï¼å° Lambda å½æ°ä¸ API è¿æ¥èµ·æ¥å¹¶éç½® APIã

### å° Lambda å½æ°è¿æ¥å° API ç½å³

ç»å½å° [AWS æ§å¶å°](https://aws.amazon.com)å¹¶ä»æå¡ä¸­æç´¢ API ç½å³ã

éæ©æä»¬ä¹ååå»ºç APIãæä»¬éè¦ç»è¿ä¸ª API æ·»å ä¸äºæ¹æ³ãä»ä¸æâæä½âåè¡¨ä¸­éæ©âåå»ºæ¹æ³â

[![Select Create method](img/e7ed5b3774c96cdf1b5415c9d08ef68f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9ZIVyoMJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.naveeraashraf.com/img/telegram-aws-7.jpg)

æ¨å°çå°å¦ä¸ä¸ªä¸æåè¡¨åºç°å¨â/âä¸ãå¨è¿éï¼æ¨å¯ä»¥æ·»å è¯¸å¦âGETâåâPOSTâä¹ç±»çæ¹æ³ãå ä¸ºæä»¬æ­£å¨å¶ä½ä¸ä¸ªéå¸¸ç®åçæºå¨äººï¼æä»¥æä»¬ä¸æ³è¦ä¸å ä¸åçæ¹æ³ãå æ­¤ï¼ä¸ºäºç®åèµ·è§ï¼æä»¬å°åªéæ©âä»»ä½âæ¹æ³ãæææ¯ä¸ç®¡åè¿ä¸ª URL åéä»ä¹æ ·çè¯·æ±ï¼é½ä¼ä¸ç´è§¦åç¸åçä»£ç ã

[![Select ANY method](img/74f778d41f3556d7a36e5d4e713e932b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SVaGnMcN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.naveeraashraf.com/img/telegram-aws-8.jpg)

ç¹å»âANYâæè¾¹çå°å¤éæ è®°ï¼å®ä¼å°æ¨å¸¦å°ä¸ä¸ªæ°çå±å¹ï¼å¨è¿éæ¨å¯ä»¥éæ©ä¸æ¦è¯¥ç«¯ç¹æ¥æ¶å°ä»»ä½ç±»åçè¯·æ±æ¶è¦åä»ä¹ãä»è¿ééæ©âLambda Functionâä½ä¸ºéæç±»åï¼å¹¶ä»åè¡¨ä¸­éæ© Lambda å½æ°çåç§°ãè¿è¦æ£æ¥âä½¿ç¨ Lambda ä»£çéæâéé¡¹ï¼è¿å°ä½¿æä»¬è½å¤è®¿é® Lambda å½æ°ä¸­çè¯·æ±ç»èã

[![Configure your endpoint](img/26977eabb3160dcb5967e826729ca2f3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zYfPHsO2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.naveeraashraf.com/img/telegram-aws-9.jpg)

åå»ä¿å­ãå¨ä¸ä¸ä¸ªå±å¹ä¸æµè¯æ¨ç APIï¼æ£æ¥æ¯å¦ä¸åæ­£å¸¸ã

[![Test your API](img/a4373ddf7b16e56ff59965b070aea8a3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Mhn8mO_M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.naveeraashraf.com/img/telegram-aws-10.jpg)

ç¶åä»âæä½âåè¡¨ä¸­åå»âé¨ç½² APIâã

[![Select Deploy API](img/c0c5848a21cd16468d2d80c2384be67f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--k2yOuQhs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.naveeraashraf.com/img/telegram-aws-11.jpg)

éæ©ä¸ä¸ªèºåï¼å¦âV1âæâè´å¡âä½ä¸ºèºåãä¸æ¦é¨ç½²äº APIï¼æ¨å°è·å¾ä¸ä¸ªè°ç¨ URLãå¦æä½ ç¹å»è¿ä¸ª URLï¼ä½ åºè¯¥å¾å°ä¸ä¸ªç®åçç½é¡µï¼ä¸é¢åçâä½ å¥½ï¼æ¥èª Lambdaï¼âãè®°ä¸è¿ä¸ªç½åã

### è®¾ç½® Webhooks

æä»¥ç°å¨æä»¬æäºä¸ä¸ªå®å¨ç URLï¼å®è¿æ¥å°æä»¬å¨äºå½æ°ä¸­çä»£ç ãé·ï¼æä»¬å¯ä»¥è®© Telegram å°æä»¬çæºå¨äººå°æ¥æ¶å°çä»»ä½æ¶æ¯åéå°è¿ä¸ªå°åã

è®¾ç½® webhook éå¸¸ç®åï¼åªéå¨æµè§å¨ä¸­é®å¥ä»¥ä¸åå®¹ï¼ç¶åæåè½¦é®ã(ç¡®ä¿æ¿æ¢å ä½ç¬¦ææ¬)

```
"https://api.telegram.org/bot<your-bot-token>/setWebHook?url=<your-API-invoke-URL>" 
```

ä½ å°ä» Telegram æ¶å°ä¸æ¡ç¡®è®¤æ¶æ¯ï¼ä»ç°å¨å¼å§ï¼ä»»ä½åéå°ä½ çæºå¨äººçæ¶æ¯é½å°è¢«æ¨éå°è¿ä¸ª URLã

### ç» Lambda å½æ°æ·»å ä»£ç 

ç»§ç»­ï¼è¯çä»ä½ èªå·±ççµæ¥è´¦æ·åæºå¨äººåéä¸æ¡æ¶æ¯ã

æä»¥æ¾ç¶ä»ä¹é½æ²¡åçð¤è¿æ¯å ä¸ºæä»¬çä»£ç è¿æ²¡æåä»»ä½äºæãä½æ¯å¦ææ¨æ³ç¡®ä¿æ¨çå½æ°æ¯éè¿æ¥æ¶ webhook è§¦åçï¼æ¨å¯ä»¥éè¿æ£æ¥ CloudWatch æ¥å¿æ¥å®ç°ãéæ© Lambda å½æ°é¡µé¢ä¸çâMonitoringâéé¡¹å¡ï¼ç¶åéæ©âView logs in CloudWatchâã

[![logs in CloudWatch](img/3e1c396016e2ab106f1a274858a523b0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KA1YZmoa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.naveeraashraf.com/img/telegram-aws-12.jpg)

æä»¬å°ç¼åä¸äºéå¸¸åºæ¬çä»£ç ï¼è¿äºä»£ç å°è¿åç¨æ·åéçæ¶æ¯ãç¨ä»¥ä¸ä»£ç æ¿æ¢ Lambda å½æ°çå¯è§åç¼è¾å¨ä¸­çä»£ç :

```
import json
from botocore.vendored import requests

TELE_TOKEN='<YOUR-BOT-TOKEN>'
URL = "https://api.telegram.org/bot{}/".format(TELE_TOKEN)

def send_message(text, chat_id):
    final_text = "You said: " + text
    url = URL + "sendMessage?text={}&chat_id={}".format(final_text, chat_id)
    requests.get(url)

def lambda_handler(event, context):
    message = json.loads(event['body'])
    chat_id = message['message']['chat']['id']
    reply = message['message']['text']
    send_message(reply, chat_id)
    return {
        'statusCode': 200
    } 
```

å¦ä½ æè§ï¼è¿æ¯ä¸æ®µéå¸¸ç®åçä»£ç ãæä»¬åªæ¯ä»äºä»¶å¯¹è±¡ä¸­æåæ¶æ¯åèå¤© idï¼å¹¶éè¿æä»¬çä»¤çå¨ HTTP GET è¯·æ±ä¸­å°å®ä½ä¸ºæ¥è¯¢å­ç¬¦ä¸²åèå¤© id ä¸èµ·åéåç»[https://api.telegram.org](https://api.telegram.org)ãæä»¬è¿è¿åä¸ä¸ª JSON å¯¹è±¡ï¼å¶âstatusCodeâä¸ºâ200 â,è¿æ ·çµæ¥æå¡å¨å°±å¯ä»¥ç¥éè¯·æ±è¢«æåæ¥æ¶äºã

ä½ å¯ä»¥å¨è¿ééè¯»æ´å¤å³äºä¸çµæ¥ API [äº¤äºçåå®¹ã](https://core.telegram.org/bots/api)

ç»§ç»­ç»ä½ çæºå¨äººåéä¸äºæ¶æ¯ï¼å®åºè¯¥ä¼è¿ååæ ·çæ¶æ¯ã

[![Telegram bot](img/9cdd879daddb127364cd6d29d9f1001f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--x2hUC-Lv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.naveeraashraf.com/img/telegram-aws-14.jpg)

å½ç¶ï¼å¯¹äºçäº§ botï¼æä»¬å°åä¸äºä¸åçäºæï¼ä¾å¦æ·»å  bot ä»¤çä½ä¸ºç¯å¢åéåæ·»å å®å¨å±ãä½æ¯è¿è¶åºäºæ¬ç®åæç¨çèå´ã

### ç»è®º

è¿å°±ææä»¬å¸¦å°äºæ¬æåçç»å°¾ãæä»¬å­¦ä¼äºå¦ä½ä¸º Telegram åå»ºä¸ä¸ªç®åç echo botï¼ä½æ´éè¦çæ¯ï¼æä»¬å­¦ä¼äºå¦ä½å¨äºä¸­å®å¨æ æå¡å¨å°æç®¡å®ã

ä¼ ç»ä¸ï¼è¦è¿è¡è¿ç§æºå¨äººï¼æä»¬éè¦ç¼åä»£ç ï¼èä¸æ¯ç®åçèæ¬ï¼èæ¯å¨å Flask è¿æ ·çå¾®æå¡æ¡æ¶åãæä»¬éè¦å web æå¡å¨è¿æ ·çåºç¡æ¶ææ¥æä¾ä»£ç ï¼SSL è¯ä¹¦æ¥ä¿è¯æä»¬çè¿æ¥å®å¨ç­ç­ãä½æ¯ç±äºå¼ºå¤§ç Lambda å½æ°å API ç½å³ï¼æä»¬çä»»å¡åå¾ç®åå¤äºã

* * *

*å¾çæ¥æº* [ç± Freepik](https://www.freepik.com/free-vector/chatbot-concept-background-with-happy-robot_2411604.htm) è®¾è®¡
# é¸é ð:ç¯å¢åé

> åæï¼<https://dev.to/gdotdesign/mint-environment-variables-24gf>

*è¿æ¯å±ç¤º Mint ç¹æ§ç³»åçä¸ä¸ç¯æç« ï¼ä½ å¯ä»¥å¨è¿éæ¾å°ä¹åçæç« :*

*   *[èè·ð:å¥é¨](https://dev.to/gdotdesign/getting-started-with-mint-3k2o)T3ã*
*   *[èè·ð:ç»ä»¶](https://dev.to/gdotdesign/components-in-mint-4a4n)T3ã*
*   *[èè·ð:ç»ä»¶çäºä»¶åç¶æ](https://dev.to/gdotdesign/mint-events-and-state-of-components-3j3a)*
*   *[èè·ð:å¤ç HTTP è¯·æ±](https://dev.to/gdotdesign/mint-handling-http-requests-2ep3)T3ã*
*   *[èè·ð:é ååç´ ](https://dev.to/gdotdesign/mint-styling-elements-295o)*
*   *[èè·ð:åå»ºå](https://dev.to/gdotdesign/mint-creating-a-packages-5e9b/)*
*   *[èè·ð:è·¯ç±](https://dev.to/gdotdesign/mint-routing-2h69/)*

å¨è¿ç¯æç« ä¸­ï¼æå°åä½ å±ç¤ºå¦ä½ä½¿ç¨ç¯å¢åéã

* * *

å¨ä»»ä½åºç¨ç¨åºä¸­ï¼è½å¤å®ä¹ç¹å®äº[é¨ç½²ç¯å¢](https://en.wikipedia.org/wiki/Deployment_environment)çåéæ¯å¿è¦çãåè®¾æ¨å¯è½å¸æå¨å¼åæé´è¿æ¥å°æ¬å° API ç«¯ç¹ï¼èå¨çäº§æ¶è¿æ¥å°è¿ç¨ APIã

### å®ä¹ç¯å¢åé

Mint ä½¿ç¨`.env`æä»¶æ¥å­å¨ç¹å®äºç¯å¢çåéï¼éå¸¸æ¯è¿æ ·ç:

```
ENDPOINT=http://localhost:3001
WSENDPOINT=ws://localhost:3001
GATRACKINGID=google-analytics-tracking-id 
```

Enter fullscreen mode Exit fullscreen mode

è¿éæä»¬å£°æäºä¸ä¸ªåé`WSENDPOINT`ã`ENDPOINT`å`GATRACKINGID`ï¼æä»¬å¸æå¨æä»¬çä»£ç ä¸­ä½¿ç¨å®ä»¬ã

### ä½¿ç¨ç¯å¢åé

å¨ Mint ä¸­ï¼æ¨å¯ä»¥ä½¿ç¨ at ( `@`)ç¬¦å·ï¼åè·åéåç§°æ¥å¼ç¨å®:

```
module Main {
  fun render : Html {
    <div>
     <{ @ENDPOINT }>
    </div>
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

æ¬è´¨ä¸ï¼åéå¼å°å¨ç¼è¯æé´ç¨ç±»å`String`åèã

å¨å¦ä¸ä¸ªä¾å­ä¸­ï¼ä½ å¯ä»¥çå°å¨æåºè¯·æ±æ¶å¦ä½ä½¿ç¨å®:

```
...

response =
 @ENDPOINT + "/api/planets"
 |> Http.get()
 |> Http.send()

... 
```

Enter fullscreen mode Exit fullscreen mode

å¦æåºç¨ç¨åºä¸­æ²¡æå®ä¹ç¯å¢åéï¼åä¼æ¾ç¤ºä¸æ¡å¾å¥½çéè¯¯æ¶æ¯:

[![Error](img/52d769b76e3792a3bb2894970791dcfe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BE2giT5y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ff6g2spc8eyy8i37rmfr.png)

### ä½¿ç¨ä¸åç`.env`æä»¶

é»è®¤æåµä¸ï¼å è½½åºç¨ç¨åºæ ¹ç®å½ä¸ç`.env`æä»¶ï¼ä½æ¯æ¨å¯ä»¥ä½¿ç¨`--env`(æ`-e`)æ å¿æå®ä¸ä¸ªä¸åçæä»¶ï¼å¦ä¸æç¤º:

```
mint build --env .env.production 
```

Enter fullscreen mode Exit fullscreen mode

ä»å¤©å°±å°è¿éï¼è°¢è°¢ä½ çéè¯»ð

* * *

å¦æä½ æ³äºè§£æ´å¤å³äº Mint çç¥è¯ï¼è¯·æ¥ç[æå](https://guide.mint-lang.com)ð

å¨ä¸ä¸é¨åï¼æå°åè¯ä½ å³äºååºçäºæðå¨é£éè§ð
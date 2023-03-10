# ðå¼åäººåçç¨æ·æ¥èªåªéï¼

> åæï¼<https://dev.to/biros/where-are-dev-users-coming-from-4l53>

æ¨å¯è½å·²ç»ç¥éï¼dev.to æä¸ä¸ªè·åæç« åç¨æ·ç APIã

è®©æä»¬ç¨ææåæ¬¢ç HTTP å®¢æ·ç«¯æ¥è¯è¯å§ï¼ [httpie](https://httpie.org/) :

```
http https://dev.to/api/users/1 
```

Enter fullscreen mode Exit fullscreen mode

```
{  "github_username":  "benhalpern",  "id":  1,  "joined_at":  "Dec 27, 2015",  "location":  "Brooklyn, NY",  "name":  "Ben Halpern",  "profile_image":  "https://res.cloudinary.com/practicaldev/image/fetch/s--DOU9qJSH--/c_fill,f_auto,fl_progressive,h_320,q_auto,w_320/https://thepracticaldev.s3.amazonaws.com/uploads/user/profile_image/1/f451a206-11c8-4e3d-8936-143d0a7e65bb.png",  "summary":  "A Canadian software developer who thinks heâs funny.",  "twitter_username":  "bendhalpern",  "type_of":  "user",  "username":  "ben",  "website_url":  "http://benhalpern.com"  } 
```

Enter fullscreen mode Exit fullscreen mode

æé¦åæ³¨æå°çæ¯`id`ä¼¼ä¹æ¯èªå¨éå¢çã
ç»è¿ä¸äºå°è¯ï¼æå¾å¿«åç° dev.to:ä¸çç¨æ·æ»æ°å¤§çº¦å¨`119000`å·¦å³ã
è¿ç»äºææ¶éææç¨æ·å¬å¼æ°æ®å¹¶å°è¯ä½¿ç¨å®ä»¬çæ³æ³ã

è®©æä»¬è¯è¯è¿ä¸ªå½ä»¤:

```
for i in {1..119000}; do http https://dev.to/api/users/$i >> users.dev.to.json && echo "," >> users.dev.to.json && echo "https://dev.to/api/users/$i"; done 
```

Enter fullscreen mode Exit fullscreen mode

å¨åå°è¿è¡äºå ä¸ªå°æ¶åï¼æç»äºè·å¾äºæç JSON æä»¶ä¸­çææç¨æ·ã

æåï¼æä½¿ç¨ [NoSQLBooster](https://nosqlbooster.com/home) å¨ mongodb éåä¸­å¯¼å¥ JSON æä»¶ã

ç¨æ·æ¡£æ¡ä¸­çä¿¡æ¯ä¸å¤ï¼ä½æä¸¤æ¡å¼å¾ä¸ç:`joined_at`å`location`ãæä¸ä¸ªå¾è¡¨æ¾ç¤ºæ³¨åéæ¶é´çè¿å±å¯è½ä¼éå¸¸æè¶£ãä¹è®¸æä¸å¤©æä¼è¿æ ·åï¼ä½ç°å¨ï¼è®©æä»¬è®¡ç®ä¸äºç¨æ·ä½ç½®çç»è®¡æ°æ®ã

æä½¿ç¨äºä¸¤ä¸ªæ¥è¯¢æ¥è®¡ç®ç»è®¡æ°æ®ãè¿ä¸ªå¸®å©æå¾å°äºäººä»¬ä½ç½®çå¤§è¶å¿:

```
db.getCollection("users.dev.to").aggregate(
    {"$group": {_id: "$location", count:{$sum:1}}}
).sort("-count") 
```

Enter fullscreen mode Exit fullscreen mode

ç¶åï¼æä½¿ç¨å¦ä¸ä¸ªæ¥è¯¢æ¥è·å¾æ¯ä¸ªä½ç½®çäººæ°:

```
db.getCollection("users.dev.to").find({"location": /\bdublin\b/i}) 
```

Enter fullscreen mode Exit fullscreen mode

ç»è®ºå¦ä¸:

å¤§å¤æ°ç¨æ·(90%)æ²¡æå¡«åä»ä»¬çä½ç½®ã
16.3%çå¡«åä½ç½®çç¨æ·æ¥èª`USA`ã
5.8%æ¥èª`India`ã4.9%æ¥èª`UK`ã
4%æ¥èª`Japan`ã3.8%æ¥èª`Germany`ã

è®©æä»¬ççä¸é¢çå®æ´ç»è®¡æ°æ®:

| # | å½å®¶ | å°åºæåå¸ | #å°åºæåå¸ | #å½å®¶ | % |
| --- | --- | --- | --- | --- | --- |
| one | ç¾å©ååä¼å½ |  |  | One thousand eight hundred and fifty | 16.32% |
|  |  | åæ¬çº½çº¦ | Two hundred and five |  |  |
|  |  | åå« CA | One hundred and ninety-four |  |  |
|  |  | Incl NY | One hundred and forty-seven |  |  |
|  |  | åæ¬æ§éå±± | One hundred and thirty-seven |  |  |
|  |  | åæ¬åé | One hundred and nineteen |  |  |
|  |  | åæ¬è¥¿éå¾ | One hundred and seventeen |  |  |
|  |  | åæ¬æ´æç¶ | One hundred and seven |  |  |
|  |  | åæ¬èå å¥ | One hundred and one |  |  |
|  |  | åæ¬åçé¡¿ | Eighty-one |  |  |
|  |  | åå« FL | seventy-eight |  |  |
|  |  | åæ¬ PA | sixty-eight |  |  |
|  |  | åå« MA | Sixty-seven |  |  |
|  |  | åå« AZ | Thirty-eight |  |  |
|  |  | åå« MI | Thirty-four |  |  |
|  |  | åå«å¦ | Thirty |  |  |
| Two | å°åº¦ |  |  | Six hundred and sixty | 5.82% |
|  |  | åæ¬ç­å ç½å° | One hundred and seven |  |  |
|  |  | åæ¬å¾·é | One hundred and three |  |  |
| three | è±å½ |  |  | Five hundred and fifty-one | 4.86% |
|  |  | åæ¬ä¼¦æ¦ | Two hundred and fifty-eight |  |  |
|  |  | åæ¬è±å½ | Thirty-five |  |  |
|  |  | åæ¬èæ ¼å° | Thirty-three |  |  |
|  |  | åæ¬å¨å°å£« | seven |  |  |
| four | æ¥æ¬ |  |  | Four hundred and fifty-one | 3.98% |
|  |  | åæ¬ä¸äº¬ | Two hundred and forty-two |  |  |
| five | å¾·å½ |  |  | Four hundred and twenty-eight | 3.77% |
|  |  | åæ¬ææ | One hundred and twenty-eight |  |  |
| six | å·´è¥¿ |  |  | Three hundred and one | 2.65% |
| seven | æ³å½ |  |  | Two hundred and ninety-four | 2.59% |
|  |  | åæ¬å·´é» | Ninety-eight |  |  |
| eight | å æ¿å¤§ |  |  | Two hundred and fifty-seven | 2.27% |
|  |  | åæ¬å¤ä¼¦å¤ | One hundred and seven |  |  |
| nine | å°¼æ¥å©äº |  |  | One hundred and eighty-seven | 1.65% |
|  |  | åæ¬æåæ¯ | Ninety-seven |  |  |
| Ten | è·å° |  |  | One hundred and fifty-four | 1.36% |
|  |  | åæ¬é¿å§æ¯ç¹ä¸¹ | Sixty-two |  |  |
| Eleven | è¥¿ç­ç |  |  | One hundred and ten | 0.97% |
| Twelve | é¿æ ¹å»· |  |  | One hundred and nine | 0.96% |
| Thirteen | æå¤§å© |  |  | One hundred and two | 0.90% |
| Fourteen | å°åº¦å°¼è¥¿äº |  |  | Ninety-two | 0.81% |
| Fifteen | ä¿ç½æ¯ |  |  | eighty-nine | 0.78% |
| Sixteen | å¢¨è¥¿å¥ |  |  | Eighty-six | 0.76% |
| Seventeen | è²å¾å®¾ |  |  | eighty-five | 0.75% |
| Eighteen | æ³¢å° |  |  | Eighty-one | 0.71% |
| Nineteen | ä¹åå° |  |  | Seventy-five | 0.66% |
| Twenty | è¡èç |  |  | Sixty-three | 0.56% |
| Twenty-one | åé |  |  | Fifty-seven | 0.50% |
| Twenty-two | å·´åºæ¯å¦ |  |  | fifty-six | 0.49% |
| Twenty-three | ç«é¸¡ |  |  | Fifty-five | 0.49% |
| Twenty-four | æ¯å©æ¶ |  |  | Fifty-three | 0.47% |
| Twenty-five | åå |  |  | fifty-two | 0.46% |
| Twenty-six | å­å æå½ |  |  | Fifty | 0.44% |
| Twenty-seven | å¥ä¼¦æ¯äº |  |  | Forty-six | 0.41% |
| Twenty-eight | ç½é©¬å°¼äº |  |  | Forty-five | 0.40% |
| Twenty-nine | è¯å°¼äº |  |  | Forty-five | 0.40% |
| Thirty | çå£« |  |  | Forty-five | 0.40% |
| Thirty-one | å¥¥å°å© |  |  | Forty-three | 0.38% |
| Thirty-two | ä¸­å½ |  |  | Forty | 0.35% |
| Thirty-three | æªå¨ |  |  | Thirty-eight | 0.34% |
| Thirty-four | ç±å°å° |  |  | Thirty-three | 0.29% |
|  | **ä¸»è¦å°ç¹æ»æ°**(å¦ä¸æå) |  |  | **6683** | **58.94%** |
|  | **å¶ä»å°ç¹**(æªååº) |  |  | **4656** | **41.06%** |
|  | **æ»ä½ç½®** |  |  | **11339** | **100.00%** |
|  | *T2`<null>`* |  |  | *90260* | *80.80%* |
|  | *T2`<empty>`* |  |  | *10108* | *9.05%* |
|  | **æ»è®¡** |  |  | **111707** | **100%** |

> æ³¨:è¿äºæ°å­æ¯ä¸ä¸ªæåçï¼ä½å®ä»¬å¾å¥½å°æ¦è¿°äºè¶å¿ã

æè°¢éè¯»ï¼
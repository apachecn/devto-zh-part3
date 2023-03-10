# ðå°å¿ Python dict.get()

> åæï¼<https://dev.to/daolf/beware-of-python-dictget-194k>

å¦æä½ è®¤ä¸º`value = my_dict.get('my_key', 'default_value')`ç­åäº
å`value = my_dict.get('my_key') or 'default_value'`,ä½ å¯è½åºè¯¥è¯»ä¸ä¸è¿ä¸ªðãå¦æä½ ç¥éä¸ºä»ä¹ä¸ä¸æ ·ï¼é£ä¹ä½ å¾å¯è½å¨è¿éå­¦ä¸å°ä¸è¥¿ã

## å¥½äº:

ä»»ä½ä½¿ç¨ Python 3 çäººé½åºè¯¥ç¥éï¼dict API éå¸¸æ¸æ°ç®åãæå¯ä»¥è¿æ ·å£°æä¸ä¸ªæ ¼è¨:

```
my_car = {'wheels': 4, 'brand': 'Tesla'} 
```

Enter fullscreen mode Exit fullscreen mode

è¿æ¯ç®åãå¿«æ·åå®¹æçãæ£ç´¢å¼å¾ç®å:

```
my_car.get('brand')
>'Tesla'
my_car['brand']
>'Tesla' 
```

Enter fullscreen mode Exit fullscreen mode

ä½æ¯ä¸ºäºæ£ç´¢å¼ï¼ææ´åæ¬¢ãget()æä¸¤ä¸ªåå ãé¦åï¼å¦ææ¨æ³è¦è®¿é®çé®ä¸å¨è¿éï¼å°ä¸ä¼åºç°å¼å¸¸(å®å°è¿å`None`)ãç¬¬äºï¼æ¨å¯ä»¥åæ¹æ³ä¼ éä¸ä¸ªé»è®¤å¼ï¼å¦æå³é®å­ä¸å¨å­å¸ä¸­ï¼è¯¥å¼å°è¢«è¿å:

```
my_car['color']
>KeyError: 'color'

my_car.get('color')
>

my_car.get('color', 'black')
>'black' 
```

Enter fullscreen mode Exit fullscreen mode

### è¿æä¸ä¸ªæ£æçé®é¢:

ç°å¨ï¼æå°åæ¨å±ç¤ºå¨ç°å®ä¸çä¸­åçäºä»ä¹ï¼åæ¶å¨æç¼åçä¸ä¸ªæ¹æ³ä¸­ä¿®å¤äº [ShopToList](https://www.shoptolist.com) çä¸ä¸ªéè¯¯ï¼è¯¥æ¹æ³ä½¿ç¨äºä¸ä¸ª[åº](https://github.com/scrapinghub/extruct)ä» HTML é¡µé¢(å¨æ¬ä¾ä¸­æ¯ä¸ä¸ªçµå­åå¡é¡µé¢)ä¸­æååæ°æ®ã

ç®èè¨ä¹ï¼æææçæ°æ®åºè¯¥æ¯è¿æ ·ç(ç®åçä¾å­):

```
data_from_extruct = {    
    'title': 't-shirt',    
    'brand': 'french-rocket',    
    'color': 'green',    
    'offer': {        
          'amount': 20,        
          'currency': 'â¬'
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

ä»è¿äºæ°æ®ä¸­å¾å°ä»·æ ¼çæç®åçæ¹æ³æ¯:

```
 price_from_extruct = data_from_extruct['offer']['amount']
    > 20 
```

Enter fullscreen mode Exit fullscreen mode

ä½æ¯æ­£å¦æä¹åæè¯´ï¼è¿ä¸ªè§£å³æ¹æ¡ä¸ç¹ä¹ä¸å¥å£®ãè¿æ¯çå®çä¸çï¼å¨çå®çä¸çä¸­ï¼æ¥èª extruct çæ°æ®å¹¶ä¸æ»æ¯å¸¦ææ¥ä»·åæ¥ä»·ä¸­çä»·æ ¼ãæ´å¥½çæ¹æ³æ¯ä½¿ç¨ dict.get:

```
price_from_extruct = data_from_extruct.get('offer').get('amount') 
```

Enter fullscreen mode Exit fullscreen mode

è¿ä»ç¶ä¸å¤å¥½ï¼å ä¸ºå¦ææ°æ®ä¸­æ²¡ææ¥ä»·ï¼æ¨å°å°è¯æ§è¡ç¬¬äºä¸ªãå¯¹`None`æ§è¡ get('amount ')æä½ï¼è¿å°å¼åä¸ä¸ªéè¯¯ãé¿åè¿ç§æåµçæ¹æ³æ¯:

```
price_from_extruct = data_from_extruct.get('offer',{}).get('amount') 
```

Enter fullscreen mode Exit fullscreen mode

è¿éï¼å¦ææä»¬å¨æ°æ®ä¸­æ²¡æ offerï¼ç¬¬ä¸ä¸ª get å°è¿å`{}`(ç©ºå­å¸)èä¸æ¯`None`ï¼ç¶åç¬¬äºä¸ª get å°æ§è¡ä¸ä¸ªç©ºå­å¸å¹¶è¿å`None`ãä¸åé½å¾å¥½ï¼çèµ·æ¥æä»¬æä¸ä¸ªå¥å£®çæ¹æ³æ¥ä»æ ¼å¼ä¸ä¸è´çæ°æ®ä¸­æåä»·æ ¼ãå½ç¶ï¼ææ¶è¿ä¸ªå¼ä¼æ¯ noneï¼ä½è³å°è¿æ®µä»£ç ä¸ä¼ä¸­æ­ã

å¥½å§ï¼æä»¬éäºãæææ¥èªé»è®¤åæ°çè¡ä¸ºãè®°ä½ï¼å½ä¸ä»å½å³é®å­**ä¸å¨å­å¸ä¸­**æ¶ï¼æä¼è¿åé»è®¤å¼ã

å®çæææ¯ï¼å¦æä½ æ¶å°çæ°æ®æ¯è¿æ ·ç:

```
data_from_extruct = {    
    'title': 't-shirt',    
    'brand': 'french-rocket',    
    'color': 'green',    
    'offer': None
} 
```

Enter fullscreen mode Exit fullscreen mode

é£ä¹åé¢çä»£ç çæ®µå°ä¼ä¸­æ­:

```
price_from_extruct = data_from_extruct.get('offer',{}).get('amount')
> AttributeError: 'NoneType' object has no attribute 'get' 
```

Enter fullscreen mode Exit fullscreen mode

è¿éæ²¡æè¿å get çé»è®¤å¼(' offer 'ï¼{})ï¼å ä¸ºå³é®ç offer å¨ dict ä¸­ãå®ååè¢«è®¾ç½®ä¸ºæ ã

å½ç¶ Python å¾æ£ï¼æä»¥æå¾å¤ç®åçæ¹æ³å¯ä»¥è§£å³è¿ä¸ªé®é¢ãä»¥ä¸çæ®µåªæ¯å¶ä¸­ä¹ä¸:

```
offers_from_extruct = data_from_extruct.get('offer') or {}
price_from_extruct = offers_from_extruct.get('amount') 
```

Enter fullscreen mode Exit fullscreen mode

å½ç¶ï¼ä¾å¦ï¼å¦æ offer çåå®¹æ¯ä¸ä¸ªåè¡¨ï¼è¿ä¹å¯ä»¥ä¸­æ­ãä½æ¯ä¸ºäºä¸¾ä¾ï¼æä»¬å°±è®²å°è¿éã

## æè°¢éè¯»

å¸æè¿ç¯ç­æè½å¸®ä½ ä»¥åèçæ¶é´ãæå¸æå¨æ¬å¨è±è´¹å¤§éæ¶é´è¯å¾ä¿®å¤æä¸ª bug ä¹åç¥éè¿ä¸ç¹ã

å¦æä½ åæ¬¢è¿ç¯æç« ï¼è¯·å¨è¯è®ºä¸­åè¯æï¼ä¸è¦å¿è®°è®¢éæçæ¶äºéè®¯ï¼è¿ææ´å¤(ä½ è¿å¯ä»¥åè´¹è·å¾æä¸ä¸æ¬çµå­ä¹¦çç¬¬ä¸ç« )ð).

å¦æä½ åæ¬¢ JSï¼æåååè¡¨äº[ä¸äºä½ å¯è½ä¼åæ¬¢çä¸è¥¿](https://dev.to/daolf/things-you-should-know-about-js-events-4k2l)ã

å¦æä½ åæ¬¢ gitï¼[æä¼å¸®ä½ æå®](https://dev.to/daolf/git-series-13-understanding-git-for-real-by-exploring-the-git-director--5bd0)ã
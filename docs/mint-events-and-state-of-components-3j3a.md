# é¸é ð:ç»ä»¶çäºä»¶åç¶æ

> åæï¼<https://dev.to/gdotdesign/mint-events-and-state-of-components-3j3a>

*è¿æ¯å±ç¤º Mint ç¹æ§çç³»åæç« çç¬¬ä¸ç¯ï¼ä½ å¯ä»¥å¨è¿éæ¾å°ä¹åçæç« :*

*   *[èè·ð:å¥é¨](https://dev.to/gdotdesign/getting-started-with-mint-3k2o)T3ã*
*   *[èè·ð:ç»ä»¶](https://dev.to/gdotdesign/components-in-mint-4a4n)T3ã*

å¨è¿ç¯æç« ä¸­ï¼æå°åä½ å±ç¤ºå¦ä½ä½¿ç¨ç»ä»¶ç**äºä»¶**å**åé¨ç¶æãT3:)**

## äºä»¶

æ¯ä¸ª web åºç¨ç¨åºé½å©ç¨äºä»¶æ¥å¤çå¶ç¶æçååï¼è¿æ ·çäºä»¶å¯è½æ¥èªå ä¸ªä¸åçæ¥æº:

*   æ¥èª DOM èç¹çç¨æ·äº¤äº
*   æ¥èªæµè§å¨æ¬èº«çäº¤äº
*   æ¥èªæå¡å¨çäº¤äº(ä¾å¦æ¥èª WebSocket è¿æ¥)
*   åå¯è½çå¶ä»æ¥æº

æå°åæ¨å±ç¤ºå¦ä½å¤çæ¥èª DOM èç¹çäºä»¶ã

### äºä»¶å±æ§åå¤çç¨åº

ä¸ React ä¸­ä¸æ ·ï¼DOM èç¹å¯ä»¥éå äºä»¶å±æ§:æ¯ä¸ªä»¥ä¸ç**å¼å§çå±æ§é½æ¯ä¸ä¸ª**äºä»¶å±æ§** ( `onClick`ï¼`onMouseOver`ï¼ç­ç­...)**

*ç±äº Mint ä½¿ç¨ React ä½ä¸ºå¹³å°ï¼ä½ å¯ä»¥åèå®å¨[æ¯æäºä»¶](https://reactjs.org/docs/events.html)åè¡¨ä¸çææ¡£ã*

äºä»¶å±æ§å¼æ¯å¿é¡»å¹éä»¥ä¸ä¸¤ç§ç±»åä¹ä¸ç**å½æ°**:

*   `Function(a)`è¿å`a`
*   `Function(Html.Event,a)`è·åä¸ä¸ª`Html.Event`å¹¶è¿å`a`ã

*`a`æ¯ä¸ä¸ªç±»ååéï¼è¿æå³çå®å¯ä»¥æ¯ä»»ä½å¶ä»ç±»åã*

ä¾å¦ï¼å¨æ¬ä¾ä¸­ï¼ä¸¤ä¸ªå¤çç¨åºé½ææ:

```
component Main {
  fun increment (event : Html.Event) : String {
    Debug.log("Increment")
  }

  fun decrement : String {
    Debug.log("Decrement")
  }

  fun render : Html {
    <div>
      <button onClick={decrement}>
        "Decrement"
      </button>

      <button onClick={increment}>
        "Increment"
      </button>
    </div>
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

å½ç¹å»ä¸ä¸ªæé®æ¶ï¼ä½ ä¼å¨æ§å¶å°ä¸çå°`increment`æ`decrement`ï¼è¿åå³äºä½ ç¹å»äºåªä¸ªæé®ã

å¦ä½ æè§ï¼ä½ å¯ä»¥å¼ç¨å½æ°æ¬èº«ï¼èä¸ç¨è°ç¨å®ï¼åªééè¿å®çåå­ã

### Htmlãäºä»¶

`Html.Event`ç±»åæ¯ [DOM äºä»¶](https://developer.mozilla.org/en-US/docs/Web/API/Event)æ¥å£çè§èåçæ¬ï¼ä½ å¯ä»¥å¨è¿éçå°å®éçç±»åå®ä¹[ã](https://github.com/mint-lang/mint/blob/master/core/source/Html.Event.mint#L2)

## åé¨ç¶æ

ç»ä»¶å¯ä»¥æèªå·±çç¶ææ¥å®ç°æäºä¸éè¦å¨å±ç¶æçç¹å®åè½ã

ä¸ä¸ª**ç¶æ**å¯ä»¥ä½¿ç¨`state`å³é®å­å®ä¹ï¼ç±»ä¼¼äº`property`å³é®å­:

```
component Main {
  state count : Number = 0

  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

è¿ä¸ª**ç¶æ**éè¿å¼ç¨å°±å¯ä»¥è·¨ç»ä»¶ä½¿ç¨:

```
 ...
      <button onClick={decrement}>
        "Decrement"
      </button>

      <{ Number.toString(count) }>

      <button onClick={increment}>
        "Increment"
      </button>
  .... 
```

Enter fullscreen mode Exit fullscreen mode

### ä¿®æ¹ç¶æ

å¯ä»¥ä½¿ç¨`next`å³é®å­è®¾ç½®ä¸ä¸ªç¶æ(æå¤ä¸ªç¶æ):å®åè¯ç»ä»¶ç¨æ°å¼æ¿æ¢ç»å®çç¶æã

ç¶æä¸ä¼åå¼ï¼ä½ä¼è¢«æ¿æ¢ï¼å ä¸º Mint ä¸­çæ°æ®ç»ææ¯ä¸å¯åçã

ä¾å¦ï¼æä»¬ä¿®æ¹å½æ°æ¥æ´æ°è®¡æ°:

```
...
  fun increment : Promise(Never, Void) {
    next { count = count + 1 }
  }

  fun decrement : Promise(Never, Void) {
    next { count = count - 1 }
  }
... 
```

Enter fullscreen mode Exit fullscreen mode

è¯·æ³¨æï¼å½æ°çè¿åç±»åå·²ç»æ´æ¹ä¸º`Promise(Never, Void)`ã

æ¿è¯ºç¨äºå¼æ­¥è®¡ç®(æä»¬å°å¨ä¸ä¸ç¯æç« ä¸­è®¨è®º)ï¼ä¿®æ¹ç¶æä¼è¿åä¸ä¸ªæ¿è¯ºï¼å ä¸ºè¿è¢«è®¤ä¸ºæ¯å¯ä½ç¨ã

ä¸ä¸ªæ¿è¯ºæä¸¤ä¸ªåæ°ï¼ç¬¬ä¸ä¸ªæ¯éè¯¯ç±»åï¼å¨è¿ç§æåµä¸å®æ¯`Never`è¡¨ç¤ºå®ä¸è½å¤±è´¥ï¼ç¬¬äºä¸ªæ¯å½å®è§£ææ¶è¿åå¼çç±»åï¼å¨è¿ç§æåµä¸å®æ¯`Void`è¡¨ç¤ºå®ä¸ç¸å³(åºæ¬ä¸ä¸è½ç¨äºä»»ä½äºæ)ã

* * *

è¿éæ¯å®æ´çæºä»£ç ï¼æè°¢æ¨çéè¯»ð:

```
component Main {
  state count : Number = 0

  fun increment : Promise(Never, Void) {
    next { count = count + 1 }
  }

  fun decrement : Promise(Never, Void) {
    next { count = count - 1 }
  }

  fun render : Html {
    <div>
      <button onClick={decrement}>
        "Decrement"
      </button>

      <{ Number.toString(count) }>

      <button onClick={increment}>
        "Increment"
      </button>
    </div>
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

å¦æä½ æ³äºè§£æ´å¤å³äº Mint çç¥è¯ï¼è¯·æ¥ç[æå](https://guide.mint-lang.com)ð

å¨ä¸ä¸é¨åä¸­ï¼æå°å[å±ç¤ºå¦ä½ä» JSON API](https://dev.to/gdotdesign/mint-handling-http-requests-2ep3) å è½½åæ¾ç¤ºæ°æ®ðå¨é£éè§ð
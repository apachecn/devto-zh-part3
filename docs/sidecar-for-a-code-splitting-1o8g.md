# ðç¨äºä»£ç åå²çè¾¹è½¦

> åæï¼<https://dev.to/thekashey/sidecar-for-a-code-splitting-1o8g>

ä»£ç åå²ãä»£ç æåæ å¤ä¸å¨ãç¶èï¼ä¸ºä»ä¹å¢ï¼ä»ä»å ä¸ºç°å¨æå¤ªå¤ç javascriptï¼èä¸ä¸æ¯ææçé½å¨åä¸æ¶é´ç¹ä½¿ç¨ã

JS æ¯ä¸ä¸ªå¾*é*çä¸è¥¿ãä¸æ¯ä¸ºä½ ç iPhone Xs æå¨æ°ç i9 ç¬è®°æ¬çµèï¼èæ¯ä¸ºæ°ç¾ä¸(å¯è½æ¯æ°åäº¿)è¾æ¢ç*è®¾å¤æ¥æèãæèè³å°æ¯ä½ çæè¡¨ã*

æä»¥- JS æ¯ä¸å¥½çï¼ä½æ¯å¦ææä»¬**ç¦ç¨å®**ä¼åçä»ä¹å¢-é®é¢ä¼æ¶å¤±...å¯¹äºä¸äºç½ç«æ¥è¯´ï¼ååºäº React çç½ç«ä¸èµ·æ¶å¤±ãä½æ¯ä¸ç®¡ææ ·ï¼æäºç½ç«ä¸éè¦ JS ä¹è½å·¥ä½...æä»¬åºè¯¥åä»ä»¬å­¦ä¹ ä¸äºä¸è¥¿...

# ä»£ç æå

ä»å¤©ï¼æä»¬æä¸¤æ¡è·¯å¯èµ°ï¼ä¸¤æ¡è®©å®åå¾æ´å¥½æä¸åå¾æ´ç³çè·¯:

## 1ãå°åä»£ç 

é£æ¯ä½ è½åçæå¥½çäºæãè½ç¶`React Hooks`å¯ä»¥è®©ä½ åå¸æ´å°çä»£ç ï¼å`Svelte`è¿æ ·çè§£å³æ¹æ¡å¯ä»¥è®©ä½ çææ¯*éå¸¸ç*æ´å°çä»£ç ï¼ä½è¿å¹¶ä¸å®¹æåå°ã

è¿ä¸ä»ä¸ä»£ç æå³ï¼è¿ä¸*åè½*æå³ââä¸ºäºä¿æä»£ç âç´§åâï¼ä½ å¿é¡»ä¿æå®âç´§åâãå¦æ application bundle è¦åè¿ä¹å¤äºæ(å¹¶ä¸æ 20 ç§è¯­è¨çæ¬)ï¼å°±æ²¡æåæ³ä¿æå®çå°ã

æåæ³ååº*ç®ç­èå¥å¨ç*ä»£ç ï¼ä¹æåæ³ååºç¸åçå®ç°ââ*è¡æä¼ä¸*ãä½ ç¥éï¼ä¸¤èé½æ¯åæ³çã

[![](img/03fa269c6ae0c9e723cca1cac356247b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lG6PDbQr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/owpecb2mbhg4bg2bck7s.png)

ä½ä¸»è¦é®é¢æ¯ä»£ç æ¬èº«ãä¸ä¸ªç®åç react åºç¨ç¨åºå¯ä»¥è½»æ¾ç»è¿âæ¨èâç 250kbãä½ å¯è½ä¼è±ä¸ä¸ªæçæ¶é´ä¼åå®ï¼è®©å®åå¾æ´å°ãâå°âä¼åè¢«å¾å¥½å°è®°å½ä¸æ¥å¹¶ä¸éå¸¸æç¨ââåªéç¨`size-limit`å¾å°`bundle-analyzer`,ç¶åæ¢å¤åç¶ã
æè®¸å¤åºï¼å®ä»¬äºå¤ºæ¯ä¸ä¸ªå­èï¼è¯å¾è®©ä½ ä¿æå¨ä½ çæéåââ[preact](https://preactjs.com/)å [storeon](https://github.com/storeon/storeon) ï¼ä»ä¸¾å ä¾ã

ä½æ¯æä»¬çåºç¨ç¨åºè¶è¿äº 200kbãæ´æ¥è¿ **100Mb** ãå é¤åå­èæ²¡ææä¹ãå³ä½¿å é¤åå­èä¹æ²¡ææä¹ã

> è¿ä¸ä¼å¿ï¼ä½ å°±ä¸å¯è½è®©ä½ çåºç¨ç¨åºä¿æå°äºãå®ä¼æ¢æ¢åå¤§ã

## 2ãæ è¹ä»£ç 

æèï¼`code split`ãæ¢å¥è¯è¯´ââ**æé**ãæ¿ä½ ç 100mb åï¼å 20 ä¸ª 5mb åãèå®è¯´ââå¦æä½ çåºç¨ç¨åºåå¤§äºï¼è¿æ¯å¯ä¸å¯è½çå¤çæ¹æ³ââç¨å®åå»ºä¸ä¸ªå°åºç¨ç¨åºåã

> æ¢ç¶æä»¬æ­£å¨è®¨è®ºå®ï¼ä½ å¯è½å¸æç¡®ä¿å¨ 2019 å¹´åºå¯¹ä»£ç æåæ¶ï¼ä½ æ¯ææ°åææ£çãæèåªæ¯éè¯»ä¸äºå®ç°ç»èã

[![thekashey image](img/7af672ebf5c7bceac7469e8a0f64ea0d.png)](/thekashey) [## ð¡2019 å¹´ååºä»£ç æå

### Anton Korzunov 3 æ 19 æ¥ 7 åéè¯»å

#react #javascript #ssr #codesplitting](/thekashey/react-code-splitting-in2019-3cmg)

ä½æ¯ç°å¨æä¸ä»¶äºä½ åºè¯¥ç¥é:æ è®ºä½ éæ©åªä¸ªéé¡¹ï¼å®é½æ¯ä¸ä¸ªå®ç°ç»èï¼èæä»¬æ­£å¨å¯»æ¾æ´å¯é çä¸è¥¿ã

# å³äºä»£ç æåççç¸

å³äºä»£ç æåççç¸æ¯ï¼å®çæ¬è´¨æ¯**æ¶é´åé**ãä½ ä¸ä»ä»æ¯*åå²*ä½ çä»£ç ï¼ä½ è¿ä»¥ä¸ç§æ¹å¼åå²å®ï¼è®©**å¨ä¸ä¸ªæ¶é´ç¹å°½å¯è½å°å°ä½¿ç¨**ã

åªæ¯ä¸è¦åå¸ä½ ç°å¨ä¸éè¦çä»£ç ãæè±å®ã

[![seee yaa](img/010fc1cfe217d14a93dd35fd623bbb6f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Zpy8sx-3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5hh0jid2m3la0t8o5b2x.png)

è¯´èµ·æ¥å®¹æï¼åèµ·æ¥é¾ãææå ä¸ªæ²éçï¼ä½æ²¡æåååè£çåºç¨ç¨åºï¼å¶ä¸­ä»»ä½ä¸ä¸ªé¡µé¢å è½½äºå¤§çº¦ 50%çä¸è¥¿ãææ¶`code splitting`åæäº`code separation`ï¼æçæææ¯ââä½ å¯ä»¥å°ä»£ç ç§»å¨å°ä¸åçåä¸­ï¼ä½æ¯ä»ç¶è¦å¨é¨ä½¿ç¨ãåæ³ä¸ä¸*âä¸è¦åå¸ä½ ç°å¨ä¸éè¦çä»£ç â*ï¼âæ*éè¦* 50%çä»£ç ï¼è¿ææ¯çæ­£çé®é¢ã

> ææ¶ä»ä»å¨è¿éåé£éå ä¸`import`æ¯ä¸å¤çãç´å°ä¸æ¯**æ¶é´**åç¦»ï¼èåªæ¯**ç©ºé´**åç¦»ââè¿ä¸ç¹é½ä¸éè¦ã

æ 3 ç§å¸¸è§çä»£ç æåæ¹å¼:

1.  åªæ¯å¨æ`import`ãè¿å å¤©å¾å°åç¬ä½¿ç¨ãå®æ´å¤çæ¯å³äºè·è¸ªä¸ä¸ª*ç¶æ*çé®é¢ã
2.  å½æ¨å¯è½æ¨è¿ React ç»ä»¶çåç°åå è½½æ¶ãå¦ä»å¤§æ¦ 90%çâååºä»£ç åå²âã
3.  *æ* `Library`ï¼å¶å®å°±æ¯`.1`ï¼ä½æ¯ä¼éè¿ React æ¸²æéå·ç»ä½ ä¸ä¸ªåºç ãå¨ [react-imported-component](https://github.com/theKashey/react-imported-component#library-level-code-splitting) å [loadable-components](https://www.smooth-code.com/open-source/loadable-components/docs/library-splitting/) ä¸­å®ç°ãç¸å½æç¨ï¼ä½ä¸å¤ªä¸ºäººæç¥ã

## ç»ä»¶çº§ä»£ç æå

è¿ä¸ªæåæ¬¢è¿ãä½ä¸ºæ¯è·¯ç±ä»£ç åå²ææ¯ç»ä»¶ä»£ç åå²ãè¦åå°è¿ä¸ç¹å¹¶æç»ä¿æè¯å¥½ç*æç¥ææ*å¹¶ä¸å®¹æãä»`Flash of Loading Content`å¼å§å°±æ¯æ­»äº¡ã

å¥½çæå·§æ¯:

*   å¹¶è¡å è½½ä¸æ¡è·¯çº¿ç`js chunk`å`data`ã
*   ä½¿ç¨`skeleton`å¨é¡µé¢å è½½åæ¾ç¤ºç±»ä¼¼é¡µé¢çåå®¹(å¦è¸ä¹¦)ã
*   ä¸ºäºæ´å¥½çé¢æµï¼ä½ çè³å¯ä»¥ä½¿ç¨ [guess-js](https://github.com/guess-js/guess) ã
*   ä½¿ç¨ä¸äºå»¶è¿ãå è½½æç¤ºå¨ã`animations`å`Suspense`(å°æ¥)æ¥è½¯åè¿æ¸¡ã

èä¸ï¼ä½ ç¥éï¼é£é½æ¯å³äº*æç¥*è¡¨ç°çã

[![](img/c3a6449079b02b06d0edb95031c87bbb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NP7f8YXF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4tl08n169vx1ziat7nhc.png)

> å¾çæ¥èª[ç¨å¹½çµåç´ æ¹è¿ç UX](https://blog.angularindepth.com/https-medium-com-thomasburleson-animated-ghosts-bfc045a51fba)

# å¬èµ·æ¥ä¸å¤ªå¥½

ä½ ç¥éï¼æå¯ä»¥ç§°èªå·±ä¸ºä»£ç åè§£ä¸å®¶ï¼ä½æä¹æèªå·±çå¤±è´¥ã

ææ¶æå¯è½æ æ³åå°åçå¤§å°ãææ¶æå¯è½æ æ³æé«ç»ææ§è½ï¼åªè¦`the _more_ code-splitting you are introducing - the more you spatially split your page - the more time you need to _reassemble_ your page back` *ãè¿å«å**å è½½æ³¢**ã

> *   No SSR or pre-rendering. The appropriate SSR is the current game changer.

[![loading waves](img/725e79ba3809310c67e9027334968234.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gZpfLwd---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/evzfftae04h5t868xhyw.jpeg)

ä¸å¨æå¤±è´¥äºä¸¤æ¬¡:

*   æå¨[ä¸æ¬¡å¾ä¹¦é¦æ¯è¾](https://github.com/mui-org/material-ui/issues/15450)ä¸­è¾äºï¼åªè¦æçå¾ä¹¦é¦æ´å¥½ðï¼ä½æ¯å¦ä¸ä¸ªå¤§å¾å¤ãææ²¡è½**1ã**å°åä»£ç ã
*   ä¼åä¸ä¸ªå°ç½ç«ï¼ç±æçå¦»å­å¨ Reactãå®ä½¿ç¨åºäºè·¯ç±çç»ä»¶åå²ï¼ä½æ¯`header`å`footer`è¢«ä¿çå¨ä¸»åä¸­ï¼ä»¥ä½¿è½¬æ¢æ´âå¯æ¥åâãä»ä»å ä»¶äºï¼**äºç¸ç´§è¦å**æç»ç«¯æ´æ¶¨å° 320 kb(gzip ä¹å)ãæ²¡æä»ä¹éè¦çä¸è¥¿ï¼ä¹æ²¡æä»ä¹æççå¯ä»¥æ¿èµ°çä¸è¥¿ã**ååä¸å**ãæç**åºè´§å¤±è´¥ä»£ç **ã

> `React-Dom`ä¸º 20%ï¼`core-js`ä¸º 10%ï¼`react-router`ï¼`jsLingui`ï¼`react-powerplug`...20%çèªæä»£ç ...æä»¬å·²ç»åå®äºã

[![](img/32d74d5fbbd7fa8286c3b2ecda469976.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oDpV2F7e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vxnjfmzn6z63o9qhdlg6.png)

## è§£

æå·²ç»å¼å§æèå¦ä½è§£å³æçé®é¢ï¼ä»¥åä¸ºä»ä¹*å¸¸è§çè§£å³æ¹æ¡*å¯¹æçç¨ä¾ä¸éç¨ã

> æåäºä»ä¹ï¼æå·²ç»ååºäºææéè¦çä½ç½®ï¼æ²¡æå®ä»¬åºç¨ç¨åºæ ¹æ¬æ æ³å·¥ä½ï¼å¹¶è¯å¾çè§£ä¸ºä»ä¹æè¿æå¶ä»çä½ç½®ã

è¿è®©æå¤§åä¸æââé®é¢åºå¨ CSS ä¸ãå¨æ®é CSS è½¬æ¢ä¸­ï¼æä½¿ç¨äºæ´å¹³æ»çç¨æ·çé¢ï¼ä»¥åæå®ç°å®çæ¹å¼ãé¿è¯ç­è¯´ââå¨è½¬æ¢å¨ç»ä¹åï¼åºå± DOM èç¹å¿é¡»å­å¨ã

è¿æ¯ä»£ç 

*   ä¸ä¸ª*æ§å¶*åé- `componentControl`ï¼æç»ä¼è¢«è®¾ç½®ä¸º`DisplayData`åºè¯¥æ¾ç¤ºçä¸è¥¿ã
*   ä¸æ¦è®¾å®å¼- `DisplayData`åå¾å¯è§ï¼æ¹å`className`ï¼ä»èè§¦åè±å¼è¿æ¸¡ãåæ¶`FocusLock`åå¾æ´»è·ï¼ä½¿`DisplayData`æä¸º**æ¨¡æ**ã

```
<FocusLock
 enabled={componentControl.value} 
 // ^ initially it's "disabled". And when it's disabled - it's dead.
>
  {componentControl.value && <PageTitle title={componentControl.value.title}/>}
  // ^ it's does not exists. Dead-dead
  <DisplayData
    data={componentControl.value}
    visible={componentControl.value !== null}
    // ^ would change a className basing on visible state
  />
  // ^ that is just not visible, but EXISTS
</FocusLock> 
```

ææ³å°è¿æ®µä»£ç ä½ä¸ºä¸ä¸ªæ´ä½è¿è¡æåï¼ä½è¿æ¯ææ æ³åå°çï¼åå æä¸¤ä¸ª:

1.  ä¸æ¦éè¦ï¼ä¿¡æ¯åºè¯¥ç«å³å¯è§ï¼æ²¡æä»»ä½å»¶è¿ãä¸å¡éæ±ãæä»¥æå¥½ä¸è¦ç å**ä¿¡æ¯**ã
2.  ä¿¡æ¯âéª¨æ¶âåºè¯¥åå­å¨ï¼æè½æ­£ç¡®å¤ç CSS è½¬æ¢ã

è¿ä¸ªé®é¢å¯ä»¥éè¿ä½¿ç¨ [CSSTransitionGroup](https://github.com/reactjs/react-transition-group) æ [recondition](https://github.com/theKashey/recondition) æ¥é¨åè§£å³ââé¦ååå»º hiddenï¼ç¶ååºç¨ä¸ä¸ª*visible*class nameââä½æ¯ï¼ä½ ç¥éï¼ä¿®å¤*ä¸ä¸ªä»£ç *æ·»å *å¦ä¸ä¸ªä»£ç *å¬èµ·æ¥å¾å¥æªï¼å³ä½¿å®éä¸*è¶³å¤äº*ãæçæææ¯æ·»å æ´å¤çä»£ç å¯ä»¥å¸®å©å é¤æ´å¤çä»£ç ãä½æ¯...ä½æ¯...

> åºè¯¥ææ´å¥½çåæ³ï¼

TLï¼åå£«ââè¿éæä¸¤ä¸ªå³é®ç¹:

*   `DisplayData`å¿é¡»è¢«**æè½½**ï¼å¹¶ä¸ä¹åå­å¨äº DOM ä¸­ã
*   `FocusLock`ä¹åºè¯¥å­å¨å¨åï¼ä»¥çµå¶`DisplayData`ï¼ä½å®ç**å¤§èå¨ä¸å¼å§å¹¶ä¸éè¦**ã

* * *

æä»¥è®©æä»¬æ¹åæä»¬çæç»´æ¨¡å¼

# èè ä¾ åç½å®¾

åè®¾æä»¬çä»£ç æ¯èè ä¾ åç½å®¾ãèè ä¾ å¯ä»¥å¯¹ä»å¤§å¤æ°çåäººï¼ä½æ¯å½ä»ä¸è½çæ¶åï¼ä»çä¼ä¼´ç½å®¾ä¼æ¥æ¯æä»..

> èè ä¾ å°åæ¬¡æå¥ææï¼ç½å®¾å°ç¨åå°è¾¾ã

è¿æ¯èè ä¾ :

```
+<FocusLock - enabled={componentControl.value} +> -  {componentControl.value && <PageTitle title={componentControl.value.title}/>} +  <DisplayData
+    data={componentControl.value}
+    visible={componentControl.value !== null}
+  />
+</FocusLock> 
```

è¿æ¯ä»çä¼ä¼´ï¼ç½å®¾

```
-<FocusLock + enabled={componentControl.value} -> +  {componentControl.value && <PageTitle title={componentControl.value.title}/>} -  <DisplayData
-    data={componentControl.value}
-    visible={componentControl.value !== null}
-  />
-</FocusLock> 
```

èè ä¾ åç½å®¾å¯ä»¥ç»æä¸ä¸ªå¢éï¼ä½ä»ä»¬å®éä¸æ¯ä¸¤ä¸ªä¸åçäººã

å«å¿äºââæä»¬è¿å¨è®¨è®º**ä»£ç æå**ãå¹¶ä¸ï¼å¨ä»£ç åå²æ¹é¢ï¼å©æå¨åªéï¼ç½å®¾å¨åªéï¼

[![Robin is in sidecar](img/27f9372e7a569ca97542d720e736bd96.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EylBIQb0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/647ij80gvo56g2apw3tv.png)

> å¨è¾¹è½¦éãç½å®¾æ­£å¨ä¸è¾**è¾¹è½¦éç­å¾**ã

# è¾¹è½¦

*   è¿äºé½æ¯ä½ çé¡¾å®¢å¿é¡»å°½å¿«çå°çè§è§ææãæå¥½æ¯ç«å»ã
*   è¿éæ¯ææçé»è¾åå¥ç¹çäº¤äºåè½ï¼å®ä»¬å¯è½å¨ä¸ç§éåå¯ç¨ï¼ä½ä¸æ¯å¨ä¸å¼å§ã

æå¥½ç§°ä¹ä¸º**åç´ä»£ç åå²**ï¼å¶ä¸­ä»£ç åæ¯å¹³è¡å­å¨ï¼ä¸å¸¸è§ç**æ°´å¹³ä»£ç åå²**ç¸åï¼å¶ä¸­ä»£ç åæ¯è¢«*åå²*ã

*   å¨[ä¸äºå½å®¶](https://github.com/respond-framework/rudy)ï¼è¿ä¸äººç»è¢«ç§°ä¸º`replace reducer`æå¶ä»æ¹å¼æ¥å»¶è¿å è½½ redux é»è¾åå¯ä½ç¨ã
*   å¨[å¶ä»ä¸äºå½å®¶](https://developers.facebook.com/videos/2019/building-the-new-facebookcom-with-react-graphql-and-relay/)ï¼å®è¢«ç§°ä¸º`"3 Phased" code splitting`ã

> è¿åªæ¯å³æ³¨ç¹çå¦ä¸ç§åç¦»ï¼åªéç¨äºè¿æ ·çæåµï¼æ¨å¯ä»¥å»¶è¿å è½½ç»ä»¶çæä¸ªé¨åï¼ä½ä¸è½å»¶è¿å è½½å¦ä¸ä¸ªé¨åã

[![phase 3 code spliting](img/586882d845741f9f307f71a88b5151eb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nCax3xxB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/96yww94ggedak03dphz4.jpeg)

> å¾çæ¥èª[ç¨ ReactãGraphQL å Relay](https://developers.facebook.com/videos/2019/building-the-new-facebookcom-with-react-graphql-and-relay/) å»ºé æ° facebook.comï¼å¶ä¸­`importForInteractions`æ`importAfter`T5 æ¯`sidecar`T6ã

è¿æä¸ä¸ª**æè¶£ç**è§å¯ââè½ç¶`Batman`å¯¹é¡¾å®¢æ¥è¯´æ´æä»·å¼ï¼åªè¦æ¯é¡¾å®¢å¯è½*çå°ç*ï¼ä»æ»æ¯å¾å¥åº·(å¹¶ä¸æä¸ä¸ªç§å¯çè¹è)...è`Robin`ï¼ä½ ç¥éï¼ä»å¯è½æç¹*è¶é*ï¼éè¦æ´å¤çå­èæ¥çå­ã

å æ­¤ï¼å¯¹äºé¡¾å®¢æ¥è¯´ï¼èè ä¾ æ¯å¯ä»¥å¿åçï¼ä»ä»¥æ´ä½çææ¬æä¾äºæ´å¤çä»·å¼ãä½ æ¯æçè±éèè ï¼

## ä»ä¹ä¸è¥¿å¯ä»¥æ¬å°è¾¹è½¦ä¸:

*   `useEffect`ã`componentDidMount`åæåå±å¤ã
*   åææç*æ¨¡æ*ææä¸æ ·ãå³`focus`å`scroll`éãä½ å¯ä»¥åå±ç¤ºä¸ä¸ªæ¨¡æï¼ç¶å**å**è®©æ¨¡æ*æ¨¡æ*ï¼å³âéå®âå®¢æ·çæ³¨æåã
*   èªå®ä¹`Selects`ââä»ä»¬èªç¶åè£æèè ä¾ (è¾å¥)åç½å®¾(Dropdrown)ãèªå®ä¹`Calendars`æä»»ä½å¶ä» UI ç»ä»¶ä¸æ¾ç¤ºå¦ä¸ä¸ª(æå¤§åæå¤æç)é¨åæç¹å»/æ¬åæ¯ä¸æ ·çã
*   è¡¨æ ¼ãå°ææé»è¾åéªè¯è½¬ç§»å°è¾¹è½¦ä¸ï¼å¹¶é»æ­¢è¡¨åæäº¤ï¼ç´å°é»è¾å è½½å®æ¯ãå®¢æ·å¯è½å¼å§å¡«åè¡¨æ ¼ï¼èä¸ç¥éå®åªæ¯`Batman`ã
*   ä¸äºå¨ç»ãå¯¹ææ¥è¯´æ¯ä¸æ´åã
*   ä¸äºè§è§ä¸çä¸è¥¿ãæ¯å¦[èªå®ä¹æ»å¨æ¡](https://github.com/theKashey/React-stroller)ï¼å®å¯è½ä¼å¨ä¸ç§éåæ¾ç¤ºç²¾ç¾çæ»å¨æ¡ãð¤·ââï¸è®¾è®¡å¸ð¤·ââï¸

æ­¤å¤ï¼ä¸è¦å¿è®°ââå¸è½½å° sidecar çæ¯ä¸æ®µä»£ç ï¼ä¹å¸è½½è¢«å é¤çä»£ç ä½¿ç¨ç core-js poly-å ponyfills ä¹ç±»çä¸è¥¿ã

[![batman and robin](img/4d49b413b16c7c68ed98e2c1ac96b763.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Rc1jlNXq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kknd3t392m1lbxiy7hro.jpg)

ä»£ç åå²å¯ä»¥æ¯æä»¬ä»å¤©çåºç¨ç¨åºæ´å æºè½ãæä»¬å¿é¡»æè¯å°æä¸¤ç§*ä»£ç *å¯ä»¥æå:1)è§è§æ¹é¢ 2)äº¤äºæ¹é¢ãåèå¯ä»¥ç¨ååºç°ã`Sidecar`æ ç¼åå²ä¸¤ä¸ªä»»å¡ï¼è®©*æè§ææä¸è¥¿é½å è½½å¾æ´å¿«*ãå®ä¼çã

## æå¤èçä»£ç æåæ¹å¼

è½ç¶å¯è½è¿ä¸å¤ªæ¸æ¥ä»ä¹æ¶åä»ä¹æ¯`sidecar`,ä½æä¼ç»åºä¸ä¸ªç®åçè§£é:

> `Sidecar`æ¯**ä½ ææçå§æ¬**ãSidecar æ¯æä»¬ä»å¤©å¾å°ææåç«¯ä¸è¥¿ä¹å*å¯¹*è¿è¡ä»£ç åå²çæ¹å¼ã

æè¯´çæ¯æå¡å¨ç«¯æ¸²æ( **SSR** ï¼æèåªæ¯æ®éç **HTML** ï¼æä»¬æ¨å¤©é½å·²ç»ä¹ æ¯äºãå½é¡µé¢åå«ç HTML åé»è¾åå«ä½äºå¯åµå¥çå¤é¨èæ¬ä¸­æ¶(å³æ³¨ç¹åç¦»)ï¼äºæä¼åå¾åä»¥åä¸æ ·ç®åã

æä»¬æ HTMLï¼**å ä¸** CSSï¼**å ä¸**ä¸äºååµçèæ¬ï¼**å ä¸**å©ä¸çèæ¬æåå°ä¸ä¸ª`.js`æä»¶ä¸­ã

`HTML` + `CSS` + `inlined-js`æ¯`Batman`ï¼èå¤é¨èæ¬æ¯`Robin`ï¼è¯¥ç½ç«è½å¤å¨æ²¡æç½å®¾çæåµä¸è¿è¡ï¼èå®è¯´ï¼é¨åæ²¡æèè ä¾ (ä»å°å¨åè¿(åèèæ¬)éª¨æçæåµä¸ç»§ç»­ææ)ãè¿åªæ¯æ¨å¤©çäºï¼è®¸å¤âéç°ä»£åé·âçç½ç«ä»å¤©ä¹æ¯å¦æ­¤ã

* * *

å¦æä½ çåºç¨æ¯æ SSRââè¯ç**ç¦ç¨ js** ,è®©å®å¨æ²¡æå®çæåµä¸ä¹è½å·¥ä½ãè¿æ ·å°±è½æ¸æ¥å°ç¥éä»ä¹ä¸è¥¿å¯ä»¥æ¬å°è¾¹è½¦éã
å¦æä½ çåºç¨ç¨åºåªæ¯ä¸ä¸ªå®¢æ·ç«¯ç SPAââè¯çæ³è±¡ä¸ä¸ï¼å¦æ SSR å­å¨ï¼å®å°å¦ä½å·¥ä½ã

> æ¯å¦ç¨ React åç-[theurge.com](https://theurge.com/en-au/)ï¼å¨åè½**æ²¡æä»»ä½ js ä½¿è½**ã

æå¾å¤ä¸è¥¿ä½ å¯ä»¥å¸å°ä¸ä¸ªè¾¹è½¦ãä¾å¦:

*   è¯è®ºãæ¨å¯ä»¥å°ä»£ç åå¸å°`display`æ³¨éï¼èä¸æ¯`answer`ï¼åªè¦å®å¯è½éè¦æ´å¤çä»£ç (åæ¬ WYSIWYG ç¼è¾å¨)ï¼è¿å¨æåæ¯ä¸éè¦çãæå¥½å»¶è¿ä¸ä¸ª*è¯è®ºæ¡*ï¼æèçè³åªæ¯å°ä»£ç å è½½éèå¨å¨ç»åé¢ï¼èä¸æ¯å»¶è¿æ´ä¸ªé¡µé¢ã
*   è§é¢æ­æ¾å¨ãè¿éæ²¡æâæ§å¶âçâè§é¢âãä¸ç§éåå è½½å®ä»¬ï¼å®¢æ·å¯è½ä¼å°è¯ä¸ä¹äº¤äºã
*   å¾çåºï¼å`slick`ãç»**ç»**æ²¡ä»ä¹å¤§ä¸äºçï¼ä½æ¯å¶ä½å¨ç»åç®¡çèµ·æ¥å°±å°é¾å¤äºãå¾æ¸æ¥ä»ä¹å¯ä»¥è¢«ç§»å°è¾¹è½¦ã

> æ³æ³ä»ä¹å¯¹ä½ çåºç¨ç¨åºæ¯å¿è¦çï¼ä»ä¹ä¸æ¯...

# å®ç°ç»è

## (DI)æä»¶ä»£ç æå

æç®åçå½¢å¼`sidecar`å¾å®¹æå®ç°ââåªè¦æææä¸è¥¿é½ç§»å°ä¸ä¸ªå­ç»ä»¶ä¸­ï¼ä½ å°±å¯ä»¥ç¨ä¸ç§âæ§âçæ¹å¼è¿è¡ä»£ç æåãè¿å ä¹æ¯æºè½ç»ä»¶åéæºè½ç»ä»¶ä¹é´çä¸ç§åç¦»ï¼ä½è¿ä¸æ¬¡æºè½ç»ä»¶å¹¶ä¸æ¯åå«éæºè½ç»ä»¶ç*-èæ¯ç¸åçã* 

```
const SmartComponent = React.lazy( () => import('./SmartComponent'));

class DumbComponent extends React.Component {
  render() {
    return (
      <React.Fragment>
       <SmartComponent ref={this} /> // <-- move smart one inside
       <TheActualMarkup />           // <-- the "real" stuff is here
      </React.Fragment>
  } 
} 
```

è¿ä¹éè¦å°*åå§å*ä»£ç è½¬ç§»å°ä¸ä¸ªåä»£ç ä¸­ï¼ä½æ¯ä½ ä»ç¶å¯ä»¥å¯¹ä»£ç ä¸­*æéç*é¨åè¿è¡ä»£ç æåã

> ä½ ç°å¨è½çå°ä¸ä¸ª`parallel`æ`vertical`ä»£ç åè£æ¨¡å¼åï¼

## äºæè½¦

ç¨ ReactãGraphQL å Relay å»ºé æ° facebook.com[ï¼æå·²ç»å¨è¿éæå°è¿ï¼æä¸ä¸ª`loadAfter`æ`importForInteractivity`çæ¦å¿µï¼è¿å¾å sidecar æ¦å¿µã](https://developers.facebook.com/videos/2019/building-the-new-facebookcom-with-react-graphql-and-relay/)

åæ¶ï¼æä¸å»ºè®®åå»ºç±»ä¼¼äº`useSidecar`çä¸è¥¿ï¼åªè¦ä½ å¯è½ææå°è¯å¨éé¢ä½¿ç¨`hooks`ï¼ä½æ¯è¿ç§å½¢å¼çä»£ç åå²ä¼æç ´æé©ç*è§åã*

è¯·éæ©æ´å·å£°ææ§çç»ä»¶æ¹å¼ãæ¨å¯è½ä¼å¨`SideCar`ç»ä»¶ä¸­ä½¿ç¨`hooks`ã

```
const Controller = React.lazy( () => import('./Controller'));
const DumbComponent = () => {
 const ref = useRef();
 const state = useState();

 return (
  <>
   <Controller componentRef={ref} state={state} />
   <TheRealStuff ref={ref} state={state[0]} />
  </>
 )
} 
```

## é¢å

å«å¿äºââä½ å¯è½ä¼ä½¿ç¨[è£è½½ä¼åçº§æç¤º](https://medium.com/webpack/link-rel-prefetch-preload-in-webpack-51a52358f84c)æ¥é¢è½½æé¢å`sidecar`å¹¶ä½¿å¶æ´å éæåä¸å¯è§ã

éè¦çä¸è¥¿ââé¢åèæ¬å°éè¿**ç½ç»**å è½½å®ï¼ä½æ¯é¤éå®ç¡®å®éè¦ï¼å¦åä¸ä¼æ§è¡(å¹¶æ¶è CPU)ã

## SSR

ä¸*æ­£å¸¸ç*ä»£ç åè§£ä¸åï¼SSR ä¸éè¦ç¹æ®çå¨ä½ã`Sidecar`å¯è½ä¸æ¯ SSR æµç¨çä¸é¨åï¼å¨`hydration`æ­¥éª¤ä¹åä¸éè¦ãå®å¯ä»¥è¢«âææâæ¨è¿ã

å æ­¤ââéæä½¿ç¨`React.lazy`(çæ³çæ¯æ²¡æ `Suspense`ç**ï¼è¿éä½ ä¸éè¦ä»»ä½åå(å è½½)æç¤ºå¨)ï¼æèä»»ä½å¶ä»æ SSR æ¯æçåºï¼ä½æ¯æå¥½æ²¡æ SSR æ¯æï¼ä»¥ä¾¿å¨ SSR è¿ç¨ä¸­*è·³è¿*è¾¹è½¦åã**

# åé¶ä»¶

ä½æ¯è¿ä¸ªæ³æ³ä¹æä¸äºä¸å¥½çå°æ¹

## èè ä¾ ä¸æ¯ä¸ä¸ªå¶ä½åç§°

è½ç¶`Batman` / `Robin`å¯è½æ¯ä¸ä¸ªå¾å¥½çæç»´æ¦å¿µï¼è`sidecar`æ¯ææ¯æ¬èº«çå®ç¾å¹éââå¯¹äº`maincar`æ²¡æâå¥½âçåå­ãæ²¡ææè°ç`maincar`ï¼æ¾ç¶`Batman`ã`Lonely Wolf`ã`Solitude`ã`Driver`ã`Solo`ä¸è½ç¨æ¥å½åéè¾¹è½¦é¶ä»¶ã

è¸ä¹¦ä½¿ç¨äº`display`å`interactivity`ï¼è¿å¯è½æ¯æä»¬ææäººçæä½³éæ©ã

> å¦æä½ æä¸ä¸ªå¥½åå­ç»æï¼è¯·å¨è¯è®ºä¸­çä¸

## æ ææ

è¿æ´å¤çæ¯ä» T4 çè§åº¦æ¥åç¦»å³æ³¨ç¹ãåè®¾ä½ æ`Batman`å`Robin`ãè¿æ`stuff.js`

```
export * from `./batman.js`
export * from `./robin.js` 
```

ç¶åä½ å¯ä»¥å°è¯åºäºç»ä»¶çä»£ç åå²æ¥å®ç°ä¸ä¸ª sidecar

```
//main.js
import {batman} from './stuff.js'

const Robin = React.lazy( () => import('./sidecar.js'));

export const Component = () => (
  <>
   <Robin />  // sidecar
   <Batman /> // main content
  </> )

// and sidecar.js... that's another chunk as long as we `import` it
import {robin} from './stuff.js'
..... 
```

ç®èè¨ä¹ï¼ä¸é¢çä»£ç å¯ä»¥å·¥ä½ï¼ä½æ¯ä¸è½âå·¥ä½âã

*   å¦æä½ åªä½¿ç¨æ¥èª`stuff.js`ç`batman`-æ æå¨å°åªä¿çå®ã
*   å¦æä½ åªä½¿ç¨æ¥èª`stuff.js`ç`robin`-æ æå¨å°åªä¿çå®ã
*   **ä½æ¯**å¦æä½ åæ¶ä½¿ç¨ä¸¤èï¼å³ä½¿æ¯å¨ä¸åçåä¸­ââä¸¤èé½å°è¢«æç»å¨`stuff.js`ç**ç¬¬ä¸ä¸ª**äºä»¶ä¸­ï¼å³**ä¸»æç»å**ã

> æ æå¨å¯¹ä»£ç åè§£ä¸åå¥½ãæ¨å¿é¡»éè¿æä»¶æ¥åç¦»å³æ³¨ç¹ã

## åæ¶å¯¼å¥

å¦ä¸ä»¶è¢«å¤§å®¶éå¿çäºææ¯ javascript çææ¬ãå¨ jQuery æ¶ä»£,`jsonp` payload å è½½èæ¬(ç¨`json` payload ),è·åææè½½è·ï¼ç¶å**ç§»é¤**èæ¬çæ¶ä»£ï¼è¿æ¯å¾å¸¸è§çã

> å¦ä»æä»¬é½å¨ç¼åèæ¬ï¼å³ä½¿ä¸åéè¦ï¼å®ä¹å°æ°¸è¿è¢«è¾å¥ã

å°±åæä¹åè¯´çââJS å¤ªå¤äºï¼è¿æ©æä¸å¤©ï¼éç*è¿ç»­å¯¼èª*ä½ ä¼å è½½ææç JSãæä»¬åºè¯¥æ¾å°ä¸ç§æ¹æ³æ¥åæ¶å¯¼å¥ä¸åéè¦çåï¼æ¸é¤ææåé¨ç¼å­å¹¶éæ¾åå­ï¼ä»¥ä½¿ web æ´å å¯é ï¼èä¸æ¯å ä¸ºåå­ä¸è¶³å¼å¸¸èå´©æºåºç¨ç¨åºã

å¤§æ¦è½å¤`un-import` (webpack [å¯ä»¥åå°](https://github.com/theKashey/wipeWebpackCache))æ¯æä»¬åºè¯¥åæä½¿ç¨*åºäºç»ä»¶ç* API çåå ä¹ä¸ï¼åªè¦å®è½ç»æä»¬å¤ç`unmount`çè½åã

å°ç®åä¸ºæ­¢ââESM æ¨¡åæ åæ²¡æå³äºè¿ç±»ä¸è¥¿çåå®¹ââä¹æ²¡æå³äºç¼å­æ§å¶çåå®¹ï¼ä¹æ²¡æå³äºæ¤éå¯¼å¥æä½çåå®¹ã

## åå»ºæ¯æä¾§è¾¹æçåº

å°ç®åä¸ºæ­¢ï¼åªæä¸ç§æ¹æ³å¯ä»¥åå»ºæ¯æ`sidecar`çåº:

*   å°æ¨çç»ä»¶æåæå ä¸ªé¨å
*   éè¿`index`æ´é²ä¸ä¸ª`main`é¨åå`connected`é¨å(ä¸ç ´å API)
*   éè¿åç¬çå¥å£ç¹å¬å¼ä¸ä¸ª`sidecar`ã
*   å¨ç®æ ä»£ç ä¸­-å¯¼å¥`main`é¨åå`sidecar` -æ æå¨åºè¯¥åå²ä¸ä¸ª`connected`é¨åã

è¿ä¸æ¬¡æ æå¨åºè¯¥æ­£å¸¸å·¥ä½ï¼å¯ä¸çé®é¢æ¯å¦ä½å½å`main`é¨åã

```
//main.js
export const Main = ({sidecar, ...props}) => (
  <div>
    {sidecar} 
    ....
  </div> );

// connected.js
import Main from './Component';
import Sidecar from './Sidecar';

export const Connected = props => (
  <Main
    sidecar={<Sidecar />}
    {...props}
  /> );

//index.js
export * from './Main';
export * from './Connected';

//sidecar.js
import * from './Sidecar';

// -------------------------

//your app BEFORE
import {Connected} from 'library'; //

// -------------------------

//your app AFTER, compare to `connected.js`
import {Main} from 'library';
const Sidecar = React.lazy(import( () => import('library/sidecar')));
// ^ all the difference ^

export SideConnected = props => (
  <Main
    sidecar={<Sidecar />}
    {...props}
  /> );

// ^ you will load only Main, Sidecar will arrive later. 
```

çè®ºä¸æ¥è¯´,`dynamic import`å¯ä»¥ç¨å¨ node_modules åé¨ï¼ä½¿å¾*ç»è£è¿ç¨*æ´å éæã

> åæ­£ââæ éå°±æ¯`children` / `slot`çæ¨¡å¼ï¼å¨ React éé£ä¹å¸¸è§ã

## æç»å½¢æ

ä¸é¢ååºäºææçååï¼æç»ç`sidecar`å½¢å¼æ¯:

```
import {Main} from 'library';
const Sidecar = React.lazy(import(/* webpackPrefetch: true */ () => import('library/sidecar')));

export SideConnected = ({enabled, props}) => (
  <Main
    sidecar={enabled && <Sidecar />}
    {...props}
  /> ); 
```

å®**é¢å** sidecar åï¼å¹¶ä¸å¨ç»ä»¶ååâä½¿ç¨âæ¶ä½¿ç¨ï¼èæ¯å¨å®ä»¥âæ´»å¨âå½¢å¼ä½¿ç¨æ¶ä½¿ç¨(å¦æè¯¥å½¢å¼å­å¨)ã

å¦æä¸æåâæ´»å¨å½¢å¼â, sidecar å°æ¹åæ¸²ææ¶é´ï¼å°å¶ä¸äº¤äºæ¶é´åå¼ï¼åªè¦âäº¤äºâç±`the main bundle`æ¬èº«å è½½ï¼ç¬¬äºä¸ªæ¶é´å°±ä¼ç¨æå»¶è¿ã

> è¿âä¸ç¹âå¯è½æ¯ç¬¬ä¸æ¬¡å è½½ä¸»ç¨åºåååç°åºç¨ç¨åºæéçå¨é¨æ¶é´ã

è®°ä½ââæåå°çâæ±½è½¦â,å¨æåçæ¸²æä¹åè¿è¡å¯è½ä¸æ¯æå¥½çä¸»æãå¨æçä¾å­ä¸­ï¼æè½å¤âæåâå ä¹ 70%çä»£ç ï¼å¤§å¤§ç¼©ç­äºæ¸²ææ¶é´ã

# æªæ¥

`Facebook`è¯æäºè¿ä¸ªæ³æ³æ¯æ­£ç¡®çãå¦æä½ è¿æ²¡æçè¿é£ä¸ªè§é¢ââç°å¨å°±çãæååä»ä¸ä¸ªç¨å¾®ä¸åçè§åº¦è§£éäºåæ ·çæ³æ³(å¹¶å¨ F8 ä¼è®®åä¸å¨å¼å§åè¿ç¯æç« )ã

ç°å¨å®éè¦å¯¹ä½ çä»£ç åºè¿è¡ä¸äºä¿®æ¹ãå®éè¦æ´æç¡®çå³æ³¨ç¹åç¦»æ¥å®éåç¦»å®ä»¬ï¼å¹¶è®©ä»£ç ä¸æ¯æ°´å¹³å°èæ¯åç´å°åç¦»ï¼ä¸ºæ´å¤§çç¨æ·ä½éªè¿éæ´å°çä»£ç ã

é¤äºä¼ ç»ç SSRï¼è¿å¯è½æ¯å¤çå¤§åä»£ç åºçå¯ä¸æ¹æ³ãå½æ¨æ¥æå¤§éä»£ç æ¶ï¼è¿æ¯äº¤ä»æå°éä»£ç çæåæºä¼ã

> å®å¯ä»¥ä½¿ä¸ä¸ªå¤§çåºç¨ç¨åºåå¾æ´å°ï¼ä¸ä¸ªå°çåºç¨ç¨åºçè³æ´å°ã

10 å¹´åï¼åªä½ç½ç«å¨ 300 æ¯«ç§åâåå¤å°±ç»ªâï¼å æ¯«ç§å*çç*åå¤å°±ç»ªäºåï¼ä»å¤©ï¼ç§çè³è¶è¿ 10 ç§æ¯å¸¸è§çæ°å­ãçå¯æã

è®©æä»¬åä¸æ¥ï¼æ³ä¸æ³ââæä»¬ææ ·æè½è§£å³è¿ä¸ªé®é¢ï¼è®© UX åæ¬¡åå¾ä¼å¤§...

[![should vs could](img/fc5dbdbb701b059be2c80d7c063038d0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zleHbamk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j2ux5xt0hrka9wuoz3iq.jpg)

# æ´ä½

`sidecar`æä¾**æ¶é´**å/æ**ç©ºé´**é´éãç¨åä½ å¯ä»¥`import`ä½ éè¦çææèæ¬...ç¨åä½¿ç¨å¨æå¯¼å¥ï¼æèä½ å¯ä»¥`require`å®ä»¬ï¼å½ä½ éè¦å®ä»¬çæ¶åãå¨ç¬¬äºæ¬¡ä¸­ï¼æ¨å°ä½¿äºæåå¾æ´ç®åï¼æ´å¤å°ä½¿ç¨ synchorniosï¼ä½ä»ç¶è½å¤èçä¸äºåå§åå¯å¨æ¶é´ï¼æ¨è¿æ¨¡åè¯ä¼°ç¤ºä¾ã

```
// time and space separation
const ImportSidecar = sidecar( () => import("./sidecar"));

export function ComponentCombination(props) {
  return (
    <ComponentUI
      {...props}
      sideCar={RequireSideCar}
    />
  );
}

// only time separation
const RequireSideCar = (props: any) => {
  const SideCar = require('./sidecar').default;
  return <SideCar {...props} />; };

export function ComponentCombination(props) {
  return (
    <ComponentUI
      {...props}
      sideCar={RequireSideCar}
    />
  );
} 
```

*   1.ç»ä»¶ä»£ç åå²æ¯ä¸ä¸ªæå¼ºå¤§çå·¥å·ï¼å®è®©ä½ è½å¤å®å¨åå²æäºä¸è¥¿ï¼ä½æ¯è¿æ¯æä»£ä»·çââä½ å¯è½å¨ä¸æ®µæ¶é´åé¤äºä¸ä¸ªç©ºç½é¡µæèä¸ä¸ª T2 æ¡æ¶ä¹å¤ä»ä¹é½ä¸ä¼æ¾ç¤ºãé£æ¯æ°´å¹³åç¦»ã
*   2.å½ç»ä»¶æåä¸èµ·ä½ç¨æ¶ï¼åºä»£ç æåä¼ææå¸®å©ãé£æ¯æ°´å¹³åç¦»ã
*   3.ä»£ç ï¼å¸è½½å° sidecar å°å®æå¾çï¼å¹¶å¯è½è®©æ¨æä¾æ´å¥½çç¨æ·ä½éªãä½æ¯ä¹éè¦ä¸äºå·¥ç¨ä¸çåªåãé£æ¯åç´åç¦»ã

**æä»¬æ¥èèè¿ä¸ª**ã

## åï¼é£ä¹ä½ è¯å¾è§£å³çé®é¢å¢ï¼

> [ååº-èç¦-éå®](https://github.com/theKashey/react-focus-lock)ã[ååº-èç¦](https://github.com/theKashey/react-focus-on)å[ååº-ç§»é¤-æ»å¨](https://github.com/theKashey/react-remove-scroll)é½å®ç°äºè¿ç§æ¨¡å¼ã

è¿åªæ¯ç¬¬ä¸é¨åã**æä»¬ç°å¨å¤äºæåé¶æ®µ**ï¼è¿éè¦å å¨æ¶é´æ¥åä¸è¿ä»½ææ¡çç¬¬äºé¨åãåæ¶...

[![](img/068ef7e2ca5d0b7b767d2d71a2ce0af1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--b_tsuoKW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kpjsy69niqycdo17kp4u.png)
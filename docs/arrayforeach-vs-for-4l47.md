# Array.foreach vs for()ð°

> åæï¼<https://dev.to/amatosg/arrayforeach-vs-for-4l47>

äºå®ä¸ï¼æä¸ç´ä»¥ä¸ºä»ä»¬æ¯åä¸æ§çï¼ææ²¡æ³å°ä¸ä¸ªè§è²åå¦ä¸ä¸ªè§è²å¯è½æè¿ä¹å¤§çåºå«ã

äºå®è¯æï¼æ§è¡âT0âæå³çæ§è¡åªéä¸è¶è¿ 96%çæ¶é´(ï¼ï¼ï¼)æ¯ä¸ä¸ª`for`åä¾å¦æé®:

```
Benchmark.prototype.setup = function() {
    // Populate the base array
    var arr = [];

    for (var i = 0; i < 1000; i++) {
        arr[i] = i; 
    }

    function someFn(i) {
        return i * 3 * 8;
    }        
}; 
```

Enter fullscreen mode Exit fullscreen mode

æ§è¡æ­¤æä½æ¶ï¼æä»¬æä»¥ä¸åå®¹(æ¨å¯ä»¥å¨æ­¤å¤è¯ç¨):

[![alt text](img/5adda0c2c0f2a88e740227d9856e86d9.png "Logo Title Text 1")](https://res.cloudinary.com/practicaldev/image/fetch/s--0XNuVj0o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://amatos.dev/conteimg/2019/03/image.png)

å¾ææ¾ï¼å½æä»¬å¤çæåä¸ä¸æ¡è®°å½æ¶å¯ä»¥çåºè¿ä¸ç¹ï¼ä½æå¥½ä»ä¸å¼å§å°±å®æ½è¯å¥½åæ³

æäºè¿ç§è¯æ®ï¼ç»å¯¹ç¦æ­¢å¨ä»»ä½æå©äºåçº¯å¯ç±ç`for`çåå±ä¸­ä½¿ç¨`foreach`

é¡ºä¾¿è¯´ä¸ä¸ï¼è¿å¹¶ä¸éç¨äº TypeScriptï¼å ä¸ºât0âæ¯ç¼è¯æât1âæä»¬åæ¬¢ç
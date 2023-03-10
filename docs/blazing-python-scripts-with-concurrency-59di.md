# â¡ ï¸Blazing èèðå·æå¹¶åâ¡ï¸ï¸çèæ¬

> åæï¼<https://dev.to/theghostyced/blazing-python-scripts-with-concurrency-59di>

ä¸ºäºç¡®ä¿åºç¨ç¨åºçé«æ§è½ï¼ç¨åºåéè¦ç¼åé«æçä»£ç ãä»£ç æçä¸è½¯ä»¶çç®æ³æçåè¿è¡æ¶æ§è¡éåº¦ç´æ¥ç¸å³ã

è¿ð [Python è¯­è¨æ¯ä¸ç§ðç¼æ¢çè¯­è¨ââä¸ C æ FORTRAN](https://www.huffpost.com/entry/computer-programming-languages-why-c-runs-so-much_b_59af8178e4b0c50640cd632e?guccounter=1&guce_referrer=aHR0cHM6Ly93d3cuZ29vZ2xlLmNvbS8&guce_referrer_sig=AQAAAIPUUFGtMUrtIlSm2M6P6eg2CjZDE-OL4Gpn1rQXa6X6jwj7N9U5bY0uLxMoOKMlddh_fZUiLy1o6T2VnD0luBVHhyDFf8FBX2axU1a7C-IXGYRGjX3CC4U1VThstAf-ztgbBajuWIBuS18MducDdiTdkjwYrChPyIoETRRxUec6) ç¸æ¯ï¼ä¸ºäºè§£å³è¿ä¸ªé®é¢ï¼äººä»¬å¼åäºåç§æ¹æ³æ¥å¸®å©æé«ç¼ç¨è¯­è¨çéåº¦ã

ä¸ç§æ¹æ³æ¯`CONCURRENCY`ã

**å¹¶å**ð¤·ââï¸

å¹¶åæ¯æä¸¤ä¸ªä»»å¡å¯ä»¥å¨éå çæ¶é´æ®µåå¼å§ãè¿è¡åå®æãè¿å¹¶ä¸æå³çå®ä»¬ä¼åæ¶è¿è¡(*åæ [`Parallelism`](https://wiki.haskell.org/Parallelism_vs._Concurrency)* )ãè¿æ¯å¾å°æðï¼è®©æä»¬æç»ä¸ä¸ªåºæ¯:

æä»¬æ­£å¨ä¸ºä¸å¯¹ç¹æ®çå¤«å¦ç­åä¸åºæ¢¦å¹»å©ç¤¼ãç±æä»¬æ¯éçæçä¸½ãèçãé©¬åæ¯åè¥¿èãæ¢¦æ³ä¸­çå©ç¤¼éè¦èç³ãä¹éãè£é¥°ååè¯·æ¬ãæä»¬æçç¤èç³çä»»å¡åéç»èçï¼æéä½£ä¹éçä»»å¡åéç»è¥¿èï¼æå¸ç½®è£é¥°åçä»»å¡åéç»çä¸½ï¼æè¯·å¸åç»é©¬åæ¯ã

è¿åä¸ªæå(*æå¤çå¨*)é½åæ¶æ§è¡ä»ä»¬çä»»å¡(*æè¿ç¨*)ï¼æ²¡æä»»å¡åæ¢æä¸­æ­ï¼ç´å°ä»»å¡å®æãè¿å°±æ¯ââ*å¨å¤è¡äººçæ¥*çæ¯è¯­ç®ç§°**å¹¶å**ã

**PYTHON ä¸­çå¹¶åç±»å**ð

Python ä¸­çåºæ¬å¹¶åç±»ååæ¬:-

*   å¤çº¿ç¨ð§µ
*   å¤éå¤çð§©( *æ¯çï¼æç¥éé£æ¯ä¸ä¸ªæ¼å¾å*ð)
*   é¿è¾è¥¿å¥¥â°(*å¨å¦ä¸ä¸ªæç¨ä¸­ææ´å¤å³äºè¿ä¸ªçåå®¹*)

**å¤çº¿ç¨** ð§µ

ä¸ä¸ª`Thread`æ¯ä¸ä¸ªæä½ç³»ç»ä¸­æå°ç*æ§è¡ååãçº¿ç¨æ¯ç¨åºå°èªå·±åæä¸¤ä¸ªæå¤ä¸ªåæ¶è¿è¡çä»»å¡çä¸ç§æ¹å¼ãçº¿ç¨æ¬èº«ä¸æ¯ç¨åºï¼å®åªå¨ä¸ä¸ªç¹å®çç¨åºæ`process`ä¸­è¿è¡ã*

[![multithreading example](img/643be0f25b0577b5ec04b4bb21793686.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GUD0sp9M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AvZ_RTovr8GW1y7bHBiRQ_A.png)

å¤çº¿ç¨æ¹åäºæ¸¸æè§åï¼ä¸»è¦ç¨äº [I/O ç»å®æä½](https://stackoverflow.com/questions/868568/what-do-the-terms-cpu-bound-and-i-o-bound-mean)ãå®æ¯ä¸ä¸ª[ä¸­å¤®å¤çå¨(CPU)](https://en.wikipedia.org/wiki/Central_processing_unit) (æå¤æ ¸å¤çå¨ä¸­çä¸ä¸ªåæ ¸)å¨æä½ç³»ç»çæ¯æä¸ï¼åæ¶æä¾å¤ä¸ªæ§è¡çº¿ç¨çè½åãæ¯ä¸ªçº¿ç¨å±äº«ç±å®æå¨çè¿ç¨æä¾çç¸åèµæºã

è®©æä»¬ä¸¾ä¾è¯´æä¸ä¸ªå¤çº¿ç¨æä½çä¾å­ãé¦åï¼æä»¬çä¸ä¸åæ­¥è¿ç¨ã

**åæ­¥è¿ç¨**

```
# A simple python script that gets query a list of site import requests
import time

def get_single_site(url):
    with requests.get(url) as response:
        print(f"Read {len(response.content)} from {url}")

def get_all_sites(sites):
    for url in sites:
        get_single_site(url, session)

if __name__ == "__main__":
    start_time = time.time()
    urls = [
        "https://www.google.com",
        "https://www.facebook.com",
        "https://www.twitter.com/theghostyced"
    ] * 30

    get_all_sites(urls)
    end_time = time.time() - start_time

    print(f"Downloaded {len(sites)} in {end_time} seconds") 
```

è¿æ¯ä¸ä¸ªç®åç python ç¨åºï¼å¯ä»¥ä¸è½½ææä¾ç½ç«çåå®¹ãä¸è½½å®ç½ç«åå®¹åï¼å®ä¼æå°åºè®¿é®è¿çç½ç«æ°éåæç¨çæ¶é´ã
è¯¥èæ¬å©ç¨äº [`requests`](https://2.python-requests.org//en/master/) åºååç½®ç python æ åæ¶é´åºã

è¿è¡ä»£ç çè¾åºæ¯:

```
...
Read 107786 from https://www.facebook.com
Read 608312 from https://www.twitter.com/theghostyced
Read 11369 from https://www.google.com
Read 107786 from https://www.facebook.com
Read 608077 from https://www.twitter.com/theghostyced
Read 11369 from https://www.google.com
Read 107787 from https://www.facebook.com
Read 608077 from https://www.twitter.com/theghostyced
Read 11369 from https://www.google.com
Read 107351 from https://www.facebook.com
Read 608311 from https://www.twitter.com/theghostyced
Read 11369 from https://www.google.com
Read 107507 from https://www.facebook.com
Read 608312 from https://www.twitter.com/theghostyced
Read 11369 from https://www.google.com
Read 107918 from https://www.facebook.com
Read 608312 from https://www.twitter.com/theghostyced
Read 11369 from https://www.google.com
Read 107149 from https://www.facebook.com
Read 608312 from https://www.twitter.com/theghostyced
Read 11365 from https://www.google.com
Read 107445 from https://www.facebook.com
Read 608077 from https://www.twitter.com/theghostyced
Read 11369 from https://www.google.com
Read 107351 from https://www.facebook.com
Read 608312 from https://www.twitter.com/theghostyced
Read 11369 from https://www.google.com
Read 107482 from https://www.facebook.com
Read 608312 from https://www.twitter.com/theghostyced
Downloaded 90 in 17.5553081035614 seconds 
```

å¨è¿éï¼èæ¬éè¦ 17.5 ç§æ¥å®æä»»å¡ãç°å¨è®©æä»¬åè¯ä¸æ¬¡ï¼ççæä»¬æ¯å¦å¯ä»¥ä½¿ç¨å¤çº¿ç¨çæ¹æ³æ¥å éå®ã

**å¤çº¿ç¨è¿ç¨**

```
# A simple python script that gets query a list of site import requests
import time
import concurrent.futures

def get_single_site(url):
    with requests.get(url) as response:
        print(f"Read {len(response.content)} from {url}")

def get_all_sites(sites):
    with concurrent.futures.ThreadPoolExecutor(max_workers=5) as executor:
        executor.map(get_single_site, sites)

if __name__ == "__main__":
    start_time = time.time() # our scripts start time
    sites = [
        "https://www.google.com",
        "https://www.facebook.com",
        "https://www.twitter.com/theghostyced"
    ] * 30

    get_all_sites(sites)
    end_time = time.time() - start_time

    print(f"Downloaded {len(sites)} in {end_time} seconds") 
```

å¨ä¸é¢çä»£ç ä¸­ï¼æä»¬ä» Python æ ååºä¸­å¯¼å¥äº [`concurrent.futures`æ¨¡å](https://docs.python.org/3.4/library/concurrent.futures.html?highlight=concurrent.futures#module-concurrent.futures)ãè¯¥æ¨¡åæä¸ä¸ª Executor ç±»ï¼è¿æ¯`ThreadPoolExecutor`å­ç±»çæ¥æºãè®©æä»¬æ¥åè§£ä¸ä¸`ThreadPoolExecutor`ã

ææç`ThreadPoolExecutor`å­ç±»æåçåªæ¯åå»ºä¸ä¸ªçº¿ç¨ç [`Pool`](https://docs.python.org/3.4/library/multiprocessing.html?highlight=pool#module-multiprocessing.pool) ãæ§è¡å¨é¨åæ§å¶çº¿ç¨æ± ä¸­æ¯ä¸ªçº¿ç¨çè¿è¡æ¹å¼åæ¶é´ã

ä¸è¿°èæ¬çè¾åºå¦ä¸æç¤º:-

```
...
Read 608312 from https://www.twitter.com/theghostyced
Read 11354 from https://www.google.com
Read 107810 from https://www.facebook.com
Read 608312 from https://www.twitter.com/theghostyced
Read 11343 from https://www.google.com
Read 107823 from https://www.facebook.com
Read 608312 from https://www.twitter.com/theghostyced
Read 11326 from https://www.google.com
Read 107388 from https://www.facebook.com
Read 11350 from https://www.google.com
Read 608312 from https://www.twitter.com/theghostyced
Read 107787 from https://www.facebook.com
Read 608311 from https://www.twitter.com/theghostyced
Read 608077 from https://www.twitter.com/theghostyced
Read 11299 from https://www.google.com
Read 11367 from https://www.google.com
Read 608312 from https://www.twitter.com/theghostyced
Read 107785 from https://www.facebook.com
Read 11321 from https://www.google.com
Read 107800 from https://www.facebook.com
Read 107350 from https://www.facebook.com
Read 608076 from https://www.twitter.com/theghostyced
Read 608312 from https://www.twitter.com/theghostyced
Read 608312 from https://www.twitter.com/theghostyced
Read 608311 from https://www.twitter.com/theghostyced
Downloaded 90 in 6.443061351776123 seconds 
```

è¿éï¼èæ¬å®æä»»å¡éè¦ 6.4 ç§ãç¸æ¯ä¹ä¸ï¼åæ­¥è¿è¡ä»£ç éè¦ 17.5 ç§ãä½ å¯è½ä¼å¯¹èªå·±è¯´ââåªå·® 12 ç§ï¼æå¯ä»¥æ¥åãåè®¾æä»¬æå¤§éçæ°æ®ï¼æ¯å¦è¯´ 1000 ä¸ªï¼é£ä¹ä½ ä¼ææ¾çå°ä¸¤ç§æ¹æ³çä¸åã

* * *

**å¤éå¤ç** ð§©

ä¸ä¸ª**è¿ç¨**ä»ä»æ¯ä¸ä¸ªæ­£å¨è¿è¡çç¨åºçå®ä¾ãéä¿å°è¯´ï¼å½æä»¬ææä»¬çè®¡ç®æºç¨åº/èæ¬åå¨ä¸ä¸ªææ¬æä»¶ä¸­ï¼æ§è¡è¿ä¸ªç¨åºï¼å°±åæäºä¸ä¸ªæ§è¡ç¨åº/èæ¬ä¸­æå°çææä»»å¡çè¿ç¨ãA `Process`ä¸ä¸å¶ä»è¿ç¨å¦`Threads`å±äº«ä»»ä½åå­ç©ºé´ã

å¤éå¤çåæ¬å¨ä¸ä¸ªè®¡ç®æºç³»ç»ä¸­ä½¿ç¨ä¸¤ä¸ªæå¤ä¸ªåæ ¸ãé»è®¤æåµä¸ï¼Python ä¸æ¯æå¤çº¿ç¨ðç¼ç¨è¯­è¨æºäº [`GIL or Global Interpreter Lock hindrance`](https://dev.to/docoprusta/explain-python-global-interpreter-lock-gil-like-im-five-25k8) ã

## GIL åµå¼ºé©¬å£®

Python æ¯ç±åå¤Â·èÂ·ç½èå§å¨ 20 ä¸çºª 80 å¹´ä»£å¼åçï¼å½æ¶ï¼è®¡ç®æºåªä½¿ç¨åä¸ª CPUï¼ä¸ºäºå¢å åå­ç®¡çï¼Guido å®ç°äº GILï¼åªåè®¸ä¸ä¸ªçº¿ç¨æ§å¶ Python è§£éå¨ãè¿æå³çï¼å©ç¨ä¸ä¸ªä»¥ä¸ç CPU åæ ¸æç¬ç«ç CPU æ¥å¹¶è¡è¿è¡çº¿ç¨æ¯ä¸å¯è½çã

[`multiprocessing module`](https://docs.python.org/3.4/library/multiprocessing.html) çå¼å¥å°±æ¯ä¸ºäºç»è¿è¿ä¸ç¹ã

> **NB**-*GIL ä¸ä¼é»æ­¢å¤çº¿ç¨çåå»ºãGIL æåçå°±æ¯ç¡®ä¿ä¸æ¬¡åªæä¸ä¸ªçº¿ç¨å¨æ§è¡ Python ä»£ç ï¼æ§å¶ä»ç¶å¨çº¿ç¨ä¹é´åæ¢ãå¦æä½ è¿æå°æï¼ [`this article will definitely help you out`](https://dev.to/docoprusta/explain-python-global-interpreter-lock-gil-like-im-five-25k8)* ã

è®©æä»¬æ¥è¯´æå¦ä½ä½¿ç¨ä¸é¢çåæ­¥ä»£ç ç¼åå¤å¤çæä½ã

**åæ­¥è¿ç¨**

```
# A simple python script that gets query a list of site import requests
import time

def get_single_site(url):
    with requests.get(url) as response:
        print(f"Read {len(response.content)} from {url}")

def get_all_sites(sites):
    for url in sites:
        get_single_site(url, session)

if __name__ == "__main__":
    start_time = time.time()
    urls = [
        "https://www.google.com",
        "https://www.facebook.com",
        "https://www.twitter.com/theghostyced"
    ] * 30

    get_all_sites(urls)
    end_time = time.time() - start_time

    print(f"Downloaded {len(sites)} in {end_time} seconds") 
```

**å¤éå¤çæ¹æ³**

```
# A simple python script that gets query a list of site import requests
import time
import multiprocessing

def get_single_site(url):
    with requests.get(url) as response:
        print(f"Read {len(response.content)} from {url}")

def get_all_sites(sites):
    with multiprocessing.Pool(5) as pool:
        pool.map(get_single_site, sites)

if __name__ == "__main__":
    start_time = time.time() # our scripts start time
    sites = [
        "https://www.google.com",
        "https://www.facebook.com",
        "https://www.twitter.com/theghostyced"
    ] * 30

    get_all_sites(sites)
    end_time = time.time() - start_time

    print(f"Downloaded {len(sites)} in {end_time} seconds") 
```

è¿éæä»¬ä» Python çæ ååºä¸­å¯¼å¥å¤å¤çåãå¤å¤çæ¨¡åéå¸¦äºä¸äºå­ç±»è¿ç¨åæ± ã

è¿éæä»¬ä½¿ç¨äº`Pool`å­ç±»ã`Pool`å°å®éè¦äº§ççå·¥äººæè¿ç¨çæ°éä½ä¸ºå®çç¬¬ä¸ä¸ªåæ°ï¼è¿å°±æ¯åçå¨`with multiprocessing.Pool(5) as pool:`è¡ä¸çäºæãå¨`pool.map(get_single_site, sites)`ä¸ï¼æä»¬ä½¿ç¨æä¾ç»æ± ç map æ¹æ³ãè¯¥æ¹æ³å°éè¦è°ç¨çå½æ°ä½ä¸ºç¬¬ä¸ä¸ªåæ°ï¼å°æä»¬ç URL åè¡¨ iterable ä½ä¸ºç¬¬äºä¸ªåæ°ãç¶åï¼å®å° iterable åå²æè®¸å¤åï¼ä½ä¸ºåç¬çä»»å¡æäº¤ç»è¿ç¨æ± ã

ç»å®æä½çè¾åºæ¯:-

```
...
Read 608423 from https://www.twitter.com/theghostyced
Read 108078 from https://www.facebook.com
Read 11386 from https://www.google.com
Read 11387 from https://www.google.com
Read 11304 from https://www.google.com
Read 11353 from https://www.google.com
Read 108021 from https://www.facebook.com
Read 107985 from https://www.facebook.com
Read 108022 from https://www.facebook.com
Read 608423 from https://www.twitter.com/theghostyced
Read 108079 from https://www.facebook.com
Read 608423 from https://www.twitter.com/theghostyced
Read 11340 from https://www.google.com
Read 608423 from https://www.twitter.com/theghostyced
Read 11321 from https://www.google.com
Read 608423 from https://www.twitter.com/theghostyced
Read 107985 from https://www.facebook.com
Read 608423 from https://www.twitter.com/theghostyced
Read 11384 from https://www.google.com
Read 107549 from https://www.facebook.com
Read 608423 from https://www.twitter.com/theghostyced
Read 11294 from https://www.google.com
Read 608423 from https://www.twitter.com/theghostyced
Read 107985 from https://www.facebook.com
Read 11360 from https://www.google.com
Read 609124 from https://www.twitter.com/theghostyced
Downloaded 90 in 6.056399154663086 seconds 
```

è¿éï¼èæ¬è±äº 6 ç§éå®æä»»å¡ï¼æ¯çº¿ç¨è§£å³æ¹æ¡ç¥å¿«ãè¿æ¯åççï¼å ä¸ºæ­£å¨è¿è¡çæä½æ¯ I/O æççãå¤éå¤çå¨å¤ç CPU å¯éåæä½æ¶è¡¨ç°æ´å¥½ï¼æ¯å¦å¤çå¤§éæ°æ®ã

## ç»è®º

æç¥éæ¨ç°å¨å¾æ³äº²èªå°è¯ä¸ä¸ï¼æä»¥å¨æ¨èµ°ä¹åï¼ææ³åè¯æ¨ä¸äºå³äºä½æ¶ä½¿ç¨å¹¶åçæ³¨æäºé¡¹ã

ä½ éè¦åå¼æ¸æ¥ä½ çç¨åºæ¯å CPU éå¶è¿æ¯å I/O éå¶ãè¯·è®°ä½ï¼I/O ç»å®çç¨åºæ¯é£äºè±è´¹å¤§é¨åæ¶é´ç­å¾äºæåç(è¿è¡å¤é¨è°ç¨æè¯·æ±)çç¨åºï¼è CPU ç»å®çç¨åºè±è´¹æ¶é´å°½å¯è½å¿«å°å¤çæ°æ®æè®¡ç®æ°å­ã

å æ­¤ï¼å¯¹äº I/O åéçæä½ï¼å¤çº¿ç¨å°æ¯æå¥½çæ¹æ³ï¼èå¯¹äº CPU åéçæä½ï¼å¤å¤çå°æ¯æ­£ç¡®çæ¹æ³ã

* * *

ð ð ð ðææ¯ [CED](https://twitter.com/theghostyced) ï¼æå¸æä½ åæ¬¢è¿ä¸ªå³äºå¦ä½å é Python è¿è¡æ¶é´çæç¨ðå§æ¬ã
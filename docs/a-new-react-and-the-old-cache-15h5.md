# ð§ æ°çååºåæ§çç¼å­

> åæï¼<https://dev.to/thekashey/a-new-react-and-the-old-cache-15h5>

æ¯ååºðå¿«ï¼æèè¯´ååºæ¯ðæ¢ï¼åç­è¿ä¸ªé®é¢è¶çº§ç®åï¼ä¸è´¹å¹ç°ä¹åã

*   ååºå¾å¿«ãå¥å·ãReact è½å¿«å°±å¿«:ä¸æ¾ç¤ºå¤ªå¤æ°æ®ï¼ä¸ä»¥æ¯ç§ 60 å¸§çéåº¦å¶ä½å¨ç»ãè¡åºéæ»æä¸ä¸ª*æ´å¿«*çå­©å­(èæ¡ï¼)ï¼ä½ React å ä¹å¨ä»»ä½æåµä¸é½è¶³å¤å¿«ã
*   ååºæ¢ãåä¸ä¸ªå¥å·ãè¿å¹¶ä¸æ¯ä»ä¹å¤§ä¸äºçäºæââåè 1000 ä¸ª SVG å¹¶ä½¿ç¨ React æ¥åç°å®ä»¬ï¼æèå¨æå¡å¨ç«¯åæ¶åç° 100 ä¸ªé¡µé¢ã

ç®èè¨ä¹ââååºæ¯ðä½ä¸ºä¸ä¸ªå®¢æ·ç«¯åºï¼ä½å¯¹äºæå¡å¨ç«¯æ¸²æ-å¦ï¼å®ä¼çð¥ç§ä½ ç CPUï¼è¿æ AWS è´¦åãä¸æ¯å ä¸ºå®æ¢ï¼èæ¯å ä¸ºä¸åé½ææéã

è¿æ¯æ¾èæè§çââä½ ä¸è½æ¯è¾ææ CPU é½ä¸ºä¸ä¸ªå®¢æ·å·¥ä½çæåµ(å¨æµè§å¨ä¸­)åä½ å¿é¡»åæ¶ä¸ºåå ä¸ªå®¢æ·æ¸²æç¸ååå®¹çæåµã

å¦ä½ç¼è§£è¿ä¸ªé®é¢ä¹æ¯æ¾èæè§çââè¿æ¯ä¸ä»£äººé½ç¥éçï¼å¯¹äº react æ¬èº«æ¥è¯´ï¼å®çä¸»è¦ææ³æ¯å¤çä¸å¿è¦çæ´æ°ââè®°å¿åãæèï¼ç¨èæ´¾ç SSR æ¥è¯´ââåªæ¯**ç¼å­**ã
å°åç¼å­ï¼å¤§åç¼å­ï¼é¡µé¢ç¼å­ï¼æ°æ®ç¼å­ï¼ä¸­é´ç»æç¼å­ãå°½å¯è½å¤çç¼å­ï¼è®©ä¸ä¸ªå®¢æ·çæ¸²æç»æå éå¦ä¸ä¸ªå®¢æ·çæ¸²æã

> ç¼å­æ¯ä¸ç§ä¼æå¨ç¥çæ¨¡å¼ï¼å¹¶ä¸æ»æ¯èçæ¶é´ï¼å åå¹´äºï¼ä¸ºäºå¤§å®¶ï¼é¤äº Reactã

### ååºè¿æ¥çæ¥å­

é£ä¹ï¼React ä¸­çç¼å­ï¼ä½ è¿è®°å¾å®ç API åï¼ä¸ï¼ä½ ä¸ç¥éãæ²¡æäºã

ä½ ä»ç¶è½å¤ç¼å­`renderToString`ç»æââæ²¡ä»ä¹å¤§ä¸äºçï¼æè[ç¼å­`renderToNodeStream`](https://zeit.co/blog/streaming-server-rendering-at-spectrum) å°±å **mxstbr** å»å¹´ä¸º Spectrum åçé£æ ·ã

ä½æ¯æä»¬éè¦ç¼å­åï¼

æ¯ç- React 15 ç¡®å®å¾æ¢ï¼React 16 å³ä½¿å¿«å¾å¤ï¼ä¹ä»ç¶éè¦ä¸äºæ¶é´æ¥æ¸²æç»æãä»»ä½äºæé½éè¦ä¸äºæ¶é´ãå¡äºé½æè¦æ±åæéã

## ç¼å­å¨ React ä¸­

åªè¦é®é¢å­å¨ï¼å°±æè®¸å¤å¼åäººåï¼ç¨*ç¼å­èæ¯*(æ¥èªä»¥åå¨ä¸åæ¡æ¶æè¯­è¨ä¸­çç»éª)æ¥è§£å³å®ââæ°çæ¨¡å¼ååºåºç°äºã

#### èåè±

ååºç©ºé´çæ¿ä»£ç©ãæä»¬çè±éãèçäºæ°ç¾ä¸ä¸ª CPU å¨æãè½å¤å¨ç»ä»¶çº§å¤çæ°æ®å è½½ï¼å¹¶ç¼å­ç»æã

å®ç¾å°å·¥ä½ã**ç´å°ååºè¿æ¥ 16** ãä¹åå°±ä¸å·¥ä½äºã

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)/[èå](https://github.com/FormidableLabs/rapscallion)

### SSR çå¼æ­¥ React VirtualDOM æ¸²æå¨ã

<article class="markdown-body entry-content container-lg" itemprop="text">

# æµæ°

[![CircleCI](img/9bdb2e0b6b0f19a35ba1ccfb1e32cc81.png) ](https://circleci.com/gh/FormidableLabs/rapscallion) [ ![Join the chat at https://gitter.im/FormidableLabs/rapscallion](img/62b3e1ae275ddfd7aafa7d8634d05c53.png)](https://gitter.im/FormidableLabs/rapscallion?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

## æ¦è§

Rapscallion æ¯æå¡å¨ç React VirtualDOM åç°å¨ãå¶æ¾èç¹ç¹å¦ä¸:

*   æ¸²ææ¯**å¼æ­¥éé»å¡**ã
*   Rapscallion çéåº¦å¤§çº¦æ¯**æ¯`renderToString`å¿« 30%ã**
***   å®æä¾äºä¸ä¸ªæµåªä½æ¥å£ï¼è¿æ ·ä½ å°±å¯ä»¥ç«å³å¼å§åå®¢æ·ç«¯åéåå®¹ã*   å®æä¾äºä¸ä¸ªæ¨¡æ¿ç¹æ§ï¼æä»¥ä½ å¯ä»¥**å¨æ ·æ¿æä»¶**ä¸­åè£ä½ çç»ä»¶ç HTMLï¼èä¸ä¼æ¾å¼æµçå¥½å¤ã*   å®æä¾äºä¸ä¸ª**ç»ä»¶ç¼å­** API æ¥è¿ä¸æ­¥å éä½ çæ¸²æã**

 **## ç®å½

*   [å®è£](https://raw.githubusercontent.com/FormidableLabs/rapscallion/master/#installation)
*   [API](https://raw.githubusercontent.com/FormidableLabs/rapscallion/master/#api)
    *   [T2`render`](https://raw.githubusercontent.com/FormidableLabs/rapscallion/master/#render)
    *   [T2`Renderer#toPromise`](https://raw.githubusercontent.com/FormidableLabs/rapscallion/master/#renderertopromise)
    *   [T2`Renderer#toStream`](https://raw.githubusercontent.com/FormidableLabs/rapscallion/master/#renderertostream)
    *   [T2`Renderer#includeDataReactAttrs`](https://raw.githubusercontent.com/FormidableLabs/rapscallion/master/#rendererincludedatareactattrs)
    *   [T2`Renderer#tuneAsynchronicity`](https://raw.githubusercontent.com/FormidableLabs/rapscallion/master/#renderertuneasynchronicity)
    *   [T2`Renderer#checksum`](https://raw.githubusercontent.com/FormidableLabs/rapscallion/master/#rendererchecksum)
    *   [T2`setCacheStrategy`](https://raw.githubusercontent.com/FormidableLabs/rapscallion/master/#setcachestrategy)
    *   [T2`template`](https://raw.githubusercontent.com/FormidableLabs/rapscallion/master/#template)
        *   [ææè¡¨è¾¾å¼](https://raw.githubusercontent.com/FormidableLabs/rapscallion/master/#valid-expressions)
        *   [è¡ä¸º](https://raw.githubusercontent.com/FormidableLabs/rapscallion/master/#behavior)
        *   [ä¾å­](https://raw.githubusercontent.com/FormidableLabs/rapscallion/master/#example)
*   [ç¼å­](https://raw.githubusercontent.com/FormidableLabs/rapscallion/master/#caching)
*   [å·´å«å¡æä»¶](https://raw.githubusercontent.com/FormidableLabs/rapscallion/master/#babel-plugins)
    *   [T2`babel-plugin-client`](https://raw.githubusercontent.com/FormidableLabs/rapscallion/master/#babel-plugin-client)
    *   [T2`babel-plugin-server`](https://raw.githubusercontent.com/FormidableLabs/rapscallion/master/#babel-plugin-server)
*   [åºå](https://raw.githubusercontent.com/FormidableLabs/rapscallion/master/#benchmarks)
*   [æ§ç§](https://raw.githubusercontent.com/FormidableLabs/rapscallion/master/#license)

## è£ç½®

ä½¿ç¨ npm:

```
$ npm install --save rapscallion
```

å¨ Node.js ä¸­:

```
const {
  render,
  template
} = require("rapscallion");
// ...
```

## åºç¨ç¨åºæ¥å£

### `render`

`render(VirtualDomNode) -> Renderer`

è¿ä¸ªå½æ°è¿åä¸ä¸ªæ¸²æå¨ï¼ä¸ä¸ªæ¸²æä½ ç VirtualDOM åç´ çæ¥å£ãä¸é¢åä¸¾äºä¸äºæ¹æ³ã

* * *

### `Renderer#toPromise`

â¦** </article>

**[View on GitHub](https://github.com/FormidableLabs/rapscallion)** **ç»ä»¶çº§ç¼å­çå¦ä¸ä¸ªå¥½å·¥å·ãå¦ä¸ä¸ªä¾µå¥ React å¹¶è¯å¾æ¹åå¶å·¥ä½æ¹å¼çå·¥å·ãè¿æä¸ä¸ªå·¥å·**ä¸è½ä¸ React 16** ä¸èµ·ä½¿ç¨ã

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [çµæ-io ](https://github.com/electrode-io) / [çµæ-ååº-SSR-ç¼å­](https://github.com/electrode-io/electrode-react-ssr-caching)

### ä½¿ç¨æ¦è¦åæåç»ä»¶ç¼å­ä¼å React SSR

<article class="markdown-body entry-content container-lg" itemprop="text">

# çµæ-ååº-SSR-ç¼å­[![NPM version](img/b7f60a885a2d6cbd3329d1a4dabf437c.png)](https://npmjs.org/package/electrode-react-ssr-caching)[![Build Status](img/1f7a8e2112803c0c1c749c3532579144.png)](https://travis-ci.org/electrode-io/electrode-react-ssr-caching)[![Dependency Status](img/91b9c14770c635664b0ceeaff9011dc4.png)](https://david-dm.org/electrode-io/electrode-react-ssr-caching)

æ¯æ profiling React æå¡å¨ç«¯æ¸²ææ¶é´åç»ä»¶ç¼å­ï¼å¸®å©ä½ å é SSRã

# å®è£

```
npm i electrode-react-ssr-caching 
```

# ä½¿ç¨

æ³¨æï¼ç±äºè¿ä¸ªæ¨¡åä¿®è¡¥äº React çæºä»£ç æ¥æ³¨å¥ç¼å­é»è¾ï¼æä»¥å®å¿é¡»å¨ React æ¨¡åä¹åå è½½ã

ä¾å¦:

```
import SSRCaching from "electrode-react-ssr-caching";
import React from 'react';
import ReactDOM from 'react-dom/server';
```

## åå

ä½ å¯ä»¥ä½¿ç¨è¿ä¸ªæ¨¡åæ¥æ£æ¥æ¯ä¸ªç»ä»¶æ¸²ææè±è´¹çæ¶é´ã

```
import SSRCaching from "electrode-react-ssr-caching";
import { renderToString } from "react-dom/server";
import MyComponent from "mycomponent";
// First you should render your component in a loop to prime the JS engine (i.e: V8 for NodeJS)
for( let i = 0; i < 10; i ++ ) {
    renderToString(<MyComponent />);
}
SSRCaching.clearProfileData();
SSRCaching.enableProfiling();
const html
```

â¦</article>

[View on GitHub](https://github.com/electrode-io/electrode-react-ssr-caching)

#### ååº-SSR-ä¼å

æ¥èªæ²å°çå®éªå®¤çç¨äº*è®°å¿*å*æ¨¡æ¿å*çç¥å¥ç³»ç»ãå¨ React 14-15 è¿ç§»æ¶æ¾è¢«ç ´åï¼**æ²¡æå¾å° React 16** æ¯æã

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[walmartlab](https://github.com/walmartlabs)/[react-SSR-ä¼å](https://github.com/walmartlabs/react-ssr-optimization)

### React.js éè¿ç»ä»¶è®°å¿ååæ¨¡æ¿åè¿è¡æå¡å¨ç«¯åç°ä¼å

<article class="markdown-body entry-content container-lg" itemprop="text">

# [![](img/0b69ad21ae2b28dcee46c32c1bc955cd.png) ](https://raw.githubusercontent.com/walmartlabs/react-ssr-optimization/masteimg/react-ssr-logo.png) React æå¡å¨ç«¯æ¸²æä¼ååº

è¿ä¸ª react æå¡å¨ç«¯ä¼ååºæ¯ä¸ä¸ªå¯éç½®ç ReactJS æ©å±ï¼ç¨äºå¨æå¡å¨ä¸è®°å¿ React ç»ä»¶æ è®°ãå®è¿æ¯æç»ä»¶æ¨¡æ¿åï¼ä»¥ä¾¿ç¨æ´å¨æçæ°æ®è¿ä¸æ­¥ç¼å­åç°çæ è®°ãè¿ä¸ªæå¡å¨ç«¯æ¨¡åéè¿ä½¿ç¨ä¸ä¸ª`require()`é©å­æ¦æª React ç instantiateReactComponent æ¨¡åï¼é¿ååå Reactã

[![Build Status](img/0b4005b445aa54109cada63c258f15f1.png)](https://travis-ci.org/walmartlabs/react-ssr-optimization)[![version](img/ffbc8afac8d32a58181eb03b2f608bd2.png)](https://www.npmjs.org/package/react-ssr-optimization)[![License](img/7ecef471fe245c1d2adc417c5be4065b.png)](https://github.com/walmartlabs/react-ssr-optimization/blob/master/LICENSE)

## æä»¬ä¸ºä»ä¹å»ºé å®

React æ¯ä¸ä¸ªåç±»æä½³ç UI ç»ä»¶æ¡æ¶ï¼åè®¸æä»¬æå»ºå¯ä»¥è·¨é¡µé¢ååºç¨ç¨åºå±äº«åéç¨çé«çº§ç»ä»¶ãReact çèæ DOM æä¾äºåºè²çå¼åä½éªï¼å°æä»¬ä»ç®¡çç»å¾®ç DOM æ´æ¹ä¸­è§£æ¾åºæ¥ãæéè¦çæ¯ï¼React ä¸ºæä»¬æä¾äºä¸ä¸ªä¼ç§çå¼ç®±å³ç¨çåæ/éç¨ JavaScript è§£å³æ¹æ¡ãReact ç`renderToString(..)`å¯ä»¥å¨æå¡å¨ä¸å°é¡µé¢ç HTML æ è®°å®å¨æ¸²ææä¸ä¸ªå­ç¬¦ä¸²ãè¿å¯¹äºåå§é¡µé¢å è½½æ§è½(å°¤å¶æ¯å¯¹äºä½å¸¦å®½çç§»å¨ç¨æ·)åæç´¢å¼æç´¢å¼åæåå°¤ä¸ºéè¦â¦

</article>

[View on GitHub](https://github.com/walmartlabs/react-ssr-optimization)

#### ååº-ç»ä»¶-ç¼å­

TLDR:å®éä¸æ¯ä¸ä¸ªæ­£å¨å·¥ä½çäººï¼

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[rook lab](https://github.com/rookLab)/[react-ç»ä»¶-ç¼å­](https://github.com/rookLab/react-component-caching)

### React 16 ä¸­çç»ä»¶ç¼å­å å¿«äºæå¡å¨ç«¯æ¸²æéåº¦

<article class="markdown-body entry-content container-lg" itemprop="text">

# React ç»ä»¶ç¼å­

## æ¦è§

React ç»ä»¶ç¼å­æ¯ä¸ä¸ªç»ä»¶çº§çç¼å­åºï¼ç¨äºä½¿ç¨ React 16 è¿è¡æ´å¿«çæå¡å¨ç«¯æ¸²æã

*   ä½¿ç¨ React çåç§æå¡å¨ç«¯æ¸²ææ¹æ³ä¸­çä»»æä¸ç§ãæ¸²ææ¯**å¼æ­¥**ã
*   ä½¿ç¨ç®åææ¨¡æ¿ç­ç¥ç¼å­ç»ä»¶ã
*   ä»ä¸ç§ç¼å­å®ç°ä¸­éæ©(LRUãRedis æ Memcached)ã

## è£ç½®

ä½¿ç¨ npm:

```
$ npm install react-component-caching
```

## ä½¿ç¨

### å¨èç¹æ¸²ææå¡å¨ä¸­:

å®ä¾åä¸ä¸ªç¼å­ï¼å¹¶å°å¶ä½ä¸ºç¬¬äºä¸ªåæ°ä¼ éç»ä»»ä½åç°æ¹æ³(`renderToString`ã`renderToStaticMarkup`ã`renderToNodeStream`æ`renderToStaticNodeStream`)ãåªè¦ä½ æ³ç¨`ReactDOM.renderToString`ï¼å°±ç¨`ReactCC.renderToString`ã

**æ³¨æ:ææè¿äºæ¹æ³é½æ¯å¼æ­¥çï¼å¹¶ä¸è¿åä¸ä¸ªæ¿è¯ºãè¦ä½¿ç¨å®ä»¬ï¼`await`æ¸²æåçååº**

```
const ReactCC = require("react-component-caching");
const cache = new ReactCC.ComponentCache();
app.get('/example', async (req,res) => {
    const renderString = await ReactCC.
```

â¦</article>

[View on GitHub](https://github.com/rookLab/react-component-caching)

å¼ç¨[React 16 ä¸­æ´å¿«çæå¡å¨ç«¯æ¸²æåç»ä»¶ç¼å­](https://medium.com/@reactcomponentcaching/speedier-server-side-rendering-in-react-16-with-component-caching-e8aa677929b1)

> Sasha Aickin [å¨ 2016 å¹´å³äºå éæå¡å¨ç«¯æ¸²æçæ¼è®²ä¸­ä»ç»å¹¶æ¼ç¤ºäºç»ä»¶ç¼å­](https://youtu.be/PnpfGy7q96U?t=22m35s)ãä¸ä¹ä¹åï¼æ²å°çå®éªå®¤åå»ºäºä¸ä¸ª[ç»ä»¶ç¼å­åº](https://github.com/electrode-io/electrode-react-ssr-caching),å®å·æå¯¹æ¸²æè¿è¡æ¶é´åæçåè½ãè¿ä¸ªæ³æ³å¨å»å¹´ç [React Amsterdam](https://youtu.be/4TOi9l6x-jo) ä¸åå°å³æ³¨ï¼å¹¶å¨ Github çè®¨è®ºä¸­æµ®åºæ°´é¢ãä¸ä¸ªç»ä»¶ç¼å­ API ä¹è¢«æ´åå°äº React ç React æå¡å¨æ¸²æå¨ä¸­ï¼[rap callion](https://github.com/FormidableLabs/rapscallion)ã
> éçå»å¹´ 9 æ React 16 çåå¸ï¼è®¸å¤è¿äºå·¥ä½é½åºè¯¥éæ°å¯å¨äºãReact æé«äºå¶æå¡å¨ç«¯æ¸²ææ¹æ³çéåº¦ï¼ç°å¨æä¾äºä¸¤ç§æµæ¹æ³ãæä»¬å¾å¥½å¥æ¯å¦æå¯è½å©ç¨è¿äºæ°çåæ¹è¿çæ¹æ³æ¥å©ç¨ç»ä»¶ç¼å­çè½åãæä»¥æä»¬æå»ºäº [React ç»ä»¶ç¼å­](https://github.com/rookLab/react-component-caching)æ¥ç»å¼åèæä¾è¿æ ·çè½åã

* * *

ä½æ¯ï¼ä½ ç¥éï¼è¿ä¸ªåºåé¨éå¸¸å¤æï¼å¨ä¸ä¸ä¸ª React çæ¬ä¸­å°ä¼å®å¨å´©æºãé¢åç¨æ·ç API ä¹ç¸å½å¤æ...ä½æ¯å®å¨å·¥ä½ï¼ä¸ memcache æå¶ä»ç¼å­åºä¸èµ·å·¥ä½ï¼å¹¶ä¸æ¯ä¸ä¸ªæä¸ä¸»...ç´å°ä»å¤©ã

## ååº-é¢æ¸²æ-ç»ä»¶

ç¶åââè®©æä»ç» React ä¸­ç»ä»¶çº§ç¼å­çå¦ä¸ç§æ¹æ³ï¼è¿ç§æ¹æ³**æ°¸è¿ä¸ä¼è¢«ç ´å**ãä»æ¥ä¸æ¯ææçã

```
import {CachedLocation, NotCacheable} from 'react-prerendred-component';

const MyCachedComponent = () => (
  <CachedLocation cacheKey="MyCachedComponent">
    any code you want
  </CachedLocation> );

// component like this shall not be cached.
const SomeNonPureImportantComponent = () => (
 <NotCacheable>hey {global.userName}</NotCacheable> ); 
```

ç®åï¼å®¹æåï¼å®æ¯å¦ä½å·¥ä½çï¼

#### å·¥ä½åç

1.  `CachedLocation`å°ä½¿ç¨`<x-cached-store>{children}<x-cached-store>`åè£æ¨çç»ä»¶
2.  å°éå¯¹æ­¤ç±»æ è®°åæ`renderToString`çç»æï¼å¹¶å°æ è®°çåå®¹ç§»å¨å°ç¼å­ä¸­ãå¯¹äº`renderToStream`æ¥è¯´ï¼åæ ·çå·¥ä½éè¿`transform streams`è¿è¡ã
3.  å¦æ`key`å­å¨äºå­å¨ä¸­ï¼é£ä¹`CachedLocation`å°åç°`<x-cached-restore-key />`ï¼å¹¶ä¸ç¸åçåç½®è¿æ»¤å¨å°ç¨å­å¨çå¼æ¿æ¢è¯¥ææ¬ã

è¿å¨ HTML/æµè½¬æ¢çº§å«ä¸ææï¼å¹¶ä¸**ä¸ä¾èµäºä»»ä½ React åé¨æºå¶**ã

åæ ·æ¯**ä¸ºå®¢æ·ç«¯**å·¥ä½ï¼`CachedLocation`ä¼ç¨ä¸ä¸ª`div`åè£ä½ çæ°æ®ï¼å¹¶ä½¿ç¨`dangerouslySetInnerHTML`å­å¨/æ¢å¤æ°æ®ãä¾å¦ï¼ä½ å¯è½*ç¼å­*é£äºå¤§ç SVGï¼æå¨å¼å§æå°è¿ã

```
<CachedLocation cacheKey="svg-fileName">
  // react component first time, just a string second.
  <MySVRGComponent />
</CachedLocation> 
renderToString(...);

// - first render, cache is empty, making a full render
// <x-cached-store-1></x-cached-store-1>
// - cache stored, extra tags removed
//  

renderToString(...);

// - second render, cache exists - restore entity
// <x-cached-restore-1/>
// - we know what to restore
//  è½ç¶`CachedLocation`å¨æ¸²æè¿ç¨ä¸­æ·»å äºé¢å¤çæ ç­¾ï¼ä½å®ä»¬ä¼å¨ç¨åè¢«ç§»é¤ãè¿æ¯ä¸ä¸ªéæçè¿ç¨ï¼

è¿å®å¨æ¯åºäºçº¯ HTML çè§£å³æ¹æ¡ï¼å¨ååºå®æåï¼å®å¨ T2 å·¥ä½ã

#### XSS æçæåï¼

æä»¥ï¼è®©æä»¬ååå¿ä¸é,`CachedLocation`ä¼å¨ç»æ HTML ä¸­æ¾ä¸äº`tag`,ä¸äºèæç(å®æ¯)RegEx ä¼å®æå©ä¸çå·¥ä½ã

å¦æä½ äº²ç±çå®¢æ·ä¹ä¼è¿æ ·åå¢ï¼å¦æå¨ JSONãscript ä¸­åç°ç¸åç`tag`ä¼ææ ·ï¼åä¹äº¦ç¶ï¼å½ç¶ï¼**ä¼å¹** :P

æä»¥ââå¨ç°å®ä¸çä¸­ï¼è¿ä¸ªåºä¸ä¼ä½¿ç¨å¦æ­¤ææ¾çæ è®°åï¼èæ¯ä¼å¨æ¯æ¬¡æ¸²ææ¶ä½¿ç¨ [nanoid](https://github.com/ai/nanoid) æ¥çæå¯ä¸çæ è®°åãæä»¥-æ²¡æ XSS åæ²¡æåé³æ§æ¯å¯è½çï¼æ¾æ¾ã

```
<x-cached-supersecretstring-11 /> // it's "safe" 
```

å¯ä¸çé®é¢æ¯ï¼å¨ä¸åçº¿ç¨ä¸­çæçç¼å­å½¼æ­¤ä¸å¼å®¹ï¼æä»¥å¨åå¸å¼ç¼å­çæåµä¸ï¼ä½ å¿é¡»èªå·±æå®`super-secret-seed`ã

#### æ¨¡æ¿å

è¿æ¯ä¸ä¸ªå¾é¿çï¼å¯è½ä¸å¤ªæ­£ç¡®çæ¯è¯­ï¼ä»£è¡¨è¾å°ååçè®°å¿ã

ä¾å¦ââ`dev.to`çå¤´åå«ä½ çå¤´åï¼ä½å¶ä»é½æ¯ä¸æ ·çââä½ å¯ä»¥å°å®âæ¨¡æ¿åâå°éæåç¼å­ç`<Header/>`ä¸­ãå¦ææ¨å°å¨æ¯ä¸ªç¨æ·çåºç¡ä¸ç¼å­å¤´ï¼ä¼åçä»ä¹ï¼ç¼å­ä¸æ¯æ éçã

[![](img/3fc4932d2e9e0bc0aff3ef796f7b8181.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jBuGNTrR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://arkwright.github.img/scaling-react-server-side-rendering/cache-explosion.svg)

> å¾çæ¥èª [Scaling React æå¡å¨ç«¯æ¸²æ](https://arkwright.github.io/scaling-react-server-side-rendering.html)ã

ä¸æ¯æ 100500 ä¸ªä¸åç`<Button>unuqieText</Button>`ï¼ä½ å¯è½åªæ`<Button>#text#</Button>`ââæä»¥ä½ å°åªè®°å¿å¤æç html æ è®°ï¼ä½¿ç¨ç®åç`replace`æ¹æ³æ·»å ä¸äºâåéâã

è½ç¶ä½¿ç¨*å­ç¬¦ä¸²ä»£æ¿*å¹¶ä¸ååå®å¨ï¼ä½æ¯è¿ä¸ªåºç»äºä½ ä¸äºç»ä»¶æ¥*æ³¨å¥*å ä½ç¬¦ï¼å®ä½¿ç¨ä¸ä¸æ API æ¥åä¸é»åéå·ï¼å¹¶ä¸åªéè¦å¾å°çä»£ç é£æ ¼ååå°±å¯ä»¥ä½¿ç¨ã

```
const UserHeader = () => (
 <div>
   .....
   <Placeholder name="userName"/>
   ....
 </div> )

renderToString(<CacheLocation variables={{userName:'Joe'}}><UserHeader/></CacheLocation>); 
// <x-cached-store-1 userName="Joe">....{x-cached-placeholder-userName}...</x-cached-store-1>

renderToString(<CacheLocation variables={{userName:'Jack'}}><UserHeader/></CacheLocation>); 
// <x-cached-restore-1 userName="Jack"/>
// ....{x-cached-placeholder-userName}...
// ....Jack... 
```

è¿æ¯ä¸ä¸ªéå¸¸å¼ºå¤§çç­ç¥ï¼éè¿å°ä½ å¨æ°ç React åºç¨ç¨åºè½¬æ¢æèå¼çæ¨¡æ¿å¼ææ¥åå°åå­ä½¿ç¨å¹¶æåä½ çåºç¨ç¨åºã

è¿æ¯å¬æ­£çðå¾ðå¿«çðã

PS:è¿å¯ä»¥ç»ä½ äºæ¯«ç§ SSRï¼`react-component-caching`å·²ç»è¯æäºè¿ä¸ç¹ã
PPS:ä½æ¯è¿å¯¹äºå®¢æ·ç«¯ç¼å­æ¥è¯´*è¿*ä¸èµ·ä½ç¨ãä½¿ç¨åéè¿è¡ CSR ç¼å­å°ç¦ç¨ç¼å­ã

è¿ç§ææ¯çå¦ä¸ä¸ªåå­æ¯ç¼å­`interpolation`ï¼å¦æä½ æ³çæ­£çè§£çæ®µç¼å­èåçä¸åï¼è¯·éè¯»[æç« ](https://arkwright.github.io/scaling-react-server-side-rendering.html)ã

#### ç¼å­å¨åªéï¼

è¿ç§æ¹æ³çå¦ä¸ä¸ªå¾é·çæ¶å»æ¯`cache`ãä¸ä¸ªç®åç React 16/ææå¼å®¹ç¼å­æ¨¡åã

*   åæ­¥`get`ç¼å­
*   åæ­¥`set`ç¼å­ã
*   æ²¡æå¯ç¨çç¼å­ï¼-æä¸ä¸ªæ¿è¯ºï¼

ä½ å¯ä»¥ä½¿ç¨ [react-cache](https://github.com/facebook/react/tree/master/packages/react-cache) ï¼åªè¦å®ä½¿ç¨ä¸åçæ¨¡å(æ²¡æ`set`)ï¼ä½æ¯ä»»ä½å¶ä»çç¼å­ï¼åæ¬åå­ä¸­çæè**å±äº«åå­**çï¼é½å¾å®¹æã

> ä½ memcache ä¸è¡ï¼åªè¦ä¸æ¯æ*å¼æ­¥*ç¼å­ã

é¢æ¸²æç»ä»¶ä¸ºæå¡å¨åå®¢æ·ç«¯æä¾äºç»ä»¶`memoization`å`templatization`çå¼ºå¤§åè½ï¼å¹¶ä¸å°æ¥ä¹ä¸ä¼å´©æºï¼å ä¸ºå®å°±æ¯è¿æ ·æå»ºçââæ°¸è¿ä¸ä¼è®©æ¨å¤±æã

è¯è¯çã
æèåªæ¯æ³æ³ã

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [å¡è¥¿](https://github.com/theKashey) / [ååº-é¢æ¸²æ-ç»ä»¶](https://github.com/theKashey/react-prerendered-component)

### ð¤æ¬çåæ¶ä»£çé¨åæ°´ååç¼å­

<article class="markdown-body entry-content container-lg" itemprop="text">

# ååºé¢æ¸²æç»ä»¶

Partial Hydration and Component-Level Caching

[![](img/85944b18b2d6e95eeafa893cf1e64cc1.png)](https://www.npmjs.com/package/react-prerendered-component) 

## æ³æ³

ç®èè¨ä¹:ä¸è¦è¯å¾**è¿è¡** js ä»£ç ï¼å¹¶çæä¸ä¸ªå¹éé¢æ¸²æç react æ ï¼èæ¯**ä½¿ç¨**é¢æ¸²æç htmlï¼ç´å° js ä»£ç åå¤å¥½æ¿æ¢å®ãè®©å®æ´»èµ·æ¥ã

å¨ HTML çº§å«ä¸è¿è½åä»ä¹ï¼ç¼å­ï¼*æ¨¡æ¿å*ï¼è¿æå¶ä»å¥½ä¸è¥¿ç»<g-emoji class="g-emoji" alias="rocket" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f680.png">ð</g-emoji>ï¼å°±å¨ä¸ä¸ª 3kb*éã

#### é¢æ¸²æç»ä»¶

> å¨æå¡å¨ä¸æ¸²æä¸äºä¸è¥¿ï¼å¨å®¢æ·ç«¯ä¸ä½ä¸º HTML ä½¿ç¨

*   æå¡å¨ç«¯æ¸²ææ°æ®
    *   è°ç¨`thisIsServer`æ¥è®¾ç½®ç¯å¢ã
    *   React-prerendered-component `will leave trails`ï¼ç¨å·²ç¥ id ç div åè£æ¯ä¸ªåã
*   æ°´åå®¢æ·ç«¯
    *   React-prerendered-component å°æç´¢å·²ç¥ç id*ï¼å¹¶ä»é¡µé¢è¿å`read rendered HTML`ã*
*   ä½ çç½ç«åå¤å¥½äº
    *   react-é¢æ¸²æ-ç»ä»¶åå¤å°±ç»ªãä»ä»¬æ­£å¨åç°ä½ ä»æå¡å¨åéçé¢åå­å¨ç HTMLã
*   ä¸æ¦ä»»ä½ç»ä»¶åå¤å¥½è¢«æ¿æ¢-æ°´åç©
    *   ä½ä¹åæ²¡æãé£æ¯â¦

</article>

[View on GitHub](https://github.com/theKashey/react-prerendered-component)

> ![unknown tweet media content](img/a20559a2332d862cb24ca6f5598beb8a.png)![Anton Korzunov profile image](img/951d3be8c74f47ad2b91e2cc0080bd91.png)å®ä¸Â·ç§å°ç¥è¯ºå¤«[@ the kashey](https://dev.to/thekashey)![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)ä¸ä¸ªæå [@olivtassinari](https://twitter.com/olivtassinari) ä»ç» JSS ç¼å­ç»æ¢-[github.com/mui-org/materiâ¦](https://t.co/SNp2Y6OE9K)ï¼ç¶å [@tannerlinsley](https://twitter.com/tannerlinsley) é®äºä¸äºå³äºâå·æ¨¡åéè£âçäºæãä½ææå®å½æâç¼å­âæ¥è¯»ã
> T24ã....è®©ææ¥è´´ä¸å¼  CachedLocation å¦ä½å·¥ä½çå¾çð¨âð§
> [github.com/theKashey/reacâ¦](https://t.co/QgjE8KG3w3)07:21AM-08 2018 å¹´ 11 æ[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1060432205829201920)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1060432205829201920)0[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1060432205829201920)7

PS:é¡ºä¾¿è¯´ä¸ä¸ï¼react-prerendered-component æ¯å¶ä»æç« çä¸»è§:

[![thekashey image](img/638fcfb14e5c451b9d2dc31d89e5b034.png)](/thekashey) [## ð¡2019 å¹´ååºä»£ç æå

### å®ä¸Â·ç§å°ç¥è¯ºå¤« 3 æ 19 æ¥ 19 æ¶ 7 åè¯»å

#react #javascript #ssr #codesplitting](/thekashey/react-code-splitting-in2019-3cmg)**
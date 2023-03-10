# ð¡2019 å¹´ååºä»£ç æå

> åæï¼<https://dev.to/thekashey/react-code-splitting-in2019-3cmg>

é½ 2019 å¹´äºï¼æ¯ä¸ªäººé½è®¤ä¸ºä»ä»¬ç¥éä»£ç åè§£ãæä»¥-è®©æä»¬ä»ç»æ£æ¥ä¸ä¸ï¼
[![](img/c7aed44be89df0e0e6ad7a817dacb313.png)T3ã](https://res.cloudinary.com/practicaldev/image/fetch/s--e9igJiqb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://habrastorage.org/webt/da/lx/0b/dalx0bnbenxiqebdymmcrp8in68.png)

## ä»£ç åè£ä»£è¡¨ä»ä¹ï¼

ç®èè¨ä¹ââä»£ç åå²å°±æ¯ä¸å è½½æ´ä¸ªä¸è¥¿ãç¶åä½ æ­£å¨éè¯»è¿ä¸ªé¡µé¢ï¼ä½ ä¸å¿å è½½æ´ä¸ªç½ç«ãå½æ¨ä»æ°æ®åºä¸­éæ©ä¸è¡æ¶ï¼æ¨ä¸å¿éæ©ææè¡ã
ææ¾ï¼ä»£ç åå²ä¹å¾ææ¾ï¼åªæ¯ä¸æ¯å³äºä½ çæ°æ®ï¼èæ¯ä½ çä»£ç ã

## æ¯è°å¨æä»£ç åè£ï¼

`React.lazy`ï¼ä¸âå®åªä½¿ç¨å®ãä»£ç åå²æ¯å¨æç»çº§å«ä¸å·¥ä½çââweb packãpackageï¼æèå¨âæ¬å°âçæåµä¸ä»ä»æ¯æ¨çæä»¶ç³»ç»ãä»£ç åå²åªæ¯æä»¶ï¼ä½ å¯ä»¥âä»¥åâå¨æä¸ªå°æ¹å è½½çæä»¶ã

## è°å¨ç¨ä»£ç æåï¼

`React.lazy`æ­£å¨ä½¿ç¨ãåªæ¯ç¨ä½ çæç»å¨çä»£ç åå²ãåªæ¯å¨æ¸²ææ¶è°ç¨å¯¼å¥ãä»æ­¤èå·²ã

## ä»ä¹å« React-loadableï¼

åä»£äºå®ãå¹¶ä¸æä¾äºæ´å¤çåè½ï¼å`Suspense`æ¥æ§å¶å è½½ç¶æãæä»¥ââç¨`React.Lazy`ä»£æ¿ã

> æ¯çï¼å°±è¿äºãæè°¢æ¨çéè¯»ï¼ç¥æ¨æå¿«ã

## ä¸ºä»ä¹è¿ç¯æç« æ²¡æåå®ï¼

å¥½å§ãå³äº`React.lazy`åä»£ç åå²ï¼æä¸äº*ç°è²å°å¸¦*ï¼æå¿äºè¯´äºã

### ç°è²åºå 1âæµè¯

æµè¯`React.lazy`å¹¶ä¸å®¹æï¼å ä¸ºå®ç*å¼æ­¥*ãåªè¦å®è¿æ²¡æè¢«å è½½(å³ä½¿å®è¢«å è½½äº)ï¼å®å°±åªæ¯âç©ºçâââ`Promises`å`import`è¿åï¼å¹¶ä¸ lazy æ¥åï¼**æ¿è¯º**ï¼å®æ»æ¯å¨**çä¸ä¸ä¸ªæ¶éå¨æ**è¢«æ§è¡ã

æµè¯`React.lazy`å¹¶ä¸å®¹æï¼å ä¸ºå®æ¯å¼æ­¥çã`mount(MyLazyComponent)`çç»æåªä¼æ¯âç©ºâï¼åªè¦ãçå®ã`Component`ã `MyLazy`åé¢ç*ï¼è¿æ²¡æå è½½ãå³ä½¿æ¯è¿æ ·ââ`import`è¿åï¼`lazy`æ¥åï¼**æ¿è¯º**ï¼æ»æ¯å¨**ä¸ä¸ä¸ªæ»´ç­**æ§è¡ãæä»¥ââä½ æ°¸è¿ä¸ä¼å¨**å½åèæ**ä¸­å¾å°åéãè¿æ¯æ³å¾ï¼* 

```
const LazyComponent = lazy(() => import('/path/to/dynamic/component'));
const Fallback = () => <div />;
const SuspenseComponent = () => (
    <Suspense fallback={<Fallback />}>
      <LazyComponent />
    </Suspense> );
const wrapper = mount(<SuspenseComponent />)
expect(wrapper.find('Fallback')).to.have.lengthOf(1)
expect(wrapper.find('DynamicComponent')).to.have.lengthOf(0)
// ^ not loaded

await wrapper.waitUntilLazyLoaded()
// ^ Oh god, why!?

expect(wrapper.find('Fallback')).to.have.lengthOf(0)
expect(wrapper.find('DynamicComponent')).to.have.lengthOf(1)
// ^ loaded! 
```

å»ºè®®çè§£å³æ¹æ¡ï¼ä½ å¯è½ä¸ç¸ä¿¡ï¼ä½æ¯å»ºè®®çè§£å³æ¹æ¡æ¯ä½¿ç¨åæ­¥*æ ç­¾*ã

# [![GitHub logo](img/375dfcc32199b4dedf2b526645c27ff7.png) æ¯æ sync# 14626](https://github.com/facebook/react/pull/14626)

[![gaearon avatar](img/2aa9b939a78baf4d2e67a9984cb321a3.png) ](https://github.com/gaearon)  **[gaearon](https://github.com/gaearon)** commented on [Jan 18, 2019](https://github.com/facebook/react/pull/14626)

ä¸ºä»ä¹ä¸å¢ï¼ç®åï¼ç±äºç«äºæ¡ä»¶(ç¶æè¢«è®¾ç½®ä¸ºå·²è§£å³ï¼ä½ç»æè¢«ä¸ä¸è¡è¦ç)ï¼å®ä»¬å¤±è´¥å¹¶åºç°ä»¤äººå°æçéè¯¯ãè¿åºè¯¥è½è§£å³é®é¢ã

ææ³è¿å¯è½å¯¹æµè¯æç¨ãè§[https://github . com/Airbnb/enzyme/issues/1917 # issue comment-454208642](https://github.com/airbnb/enzyme/issues/1917#issuecomment-454208642)ãäººä»¬å¨åæ­¥ç¯å¢ä¸­å¯»æ¾ç±»ä¼¼`waitForLazyLoaded`çåéåæ³æ¯å¾å°´å°¬çãæ¯æåæ­¥æ ç­¾å¯è½æ¯ä¸ä¸ªå¾å¥½çè§£å³æ¹æ¡ã

[View on GitHub](https://github.com/facebook/react/pull/14626)

é£ä¹ââè®©æä»¬æ¥å¶å®å¯¼å¥åæ­¥å§ï¼ï¼
T3ã

```
const LazyText = lazy(() => ({
   then(cb) {   
      cb({default: Text});
      // this is "sync" thenable
   },
}));     
const root = ReactTestRenderer.create(
  <Suspense fallback={<Text text="Loading..." />}>          
     <LazyText text="Hi" /> // this lazy is not very lazy
  </Suspense>, ); 
```

å°å¯¼å¥å½æ°è½¬æ¢æè®°å¿çåæ­¥è¡¨å¹¶ä¸é¾ã

```
const syncImport = (importFn) => {
   let preloaded = undefined;
   const promise = importFn().then(module => preloaded = module);
   // ^ "auto" import and "cache" promise 
   return () => preloaded ? { then: () => preloaded } : promise;
   // ^ return sync thenable then possible
}
const lazyImport = isNode ? syncImport : a => a; 
// ^ sync for node, async for browser
const LazyComponent = React.lazy(lazyImport(() => import('./file')); 
```

### ç°è²åºå 2âSSR

> å¦æä½ ä¸éè¦ SSR è¯·ç»§ç»­éè¯»æç« ï¼

`React.lazy`æ¯ SSR åå¥½çãä½å®éè¦`Suspense`æè½å·¥ä½ï¼æ¬å¿µæ¯**å¯¹æå¡å¨ç«¯ä¸åå¥½**ã

æä¸¤ç§è§£å³æ¹æ¡:

*   ç¨çæ®µä»£æ¿æ¬å¿µï¼æ¯å¦éè¿å²è®½ãç¶åï¼ä½¿ç¨å¸¦æåæ­¥`then`ç`import`çä¿®æ¹çæ¬ï¼ä½¿ lazy ä¹åæ­¥è¿è¡ã

```
import React from 'react';
const realLazy = React.lazy;
React.lazy = importer => realLazy(syncImport(importer));
React.Suspense = React.Fragment; // :P
// ^ React SSR just got fixed :D 
```

è¿æ¯ä¸ä¸ªå¾å¥½çéæ©ï¼ä½æ¯å®å¯¹å®¢æ·ç«¯ä¸å¤ªåå¥½ãä¸ºä»ä¹ï¼è®©æä»¬å®ä¹ç¬¬äºç§å¯è½çè§£å³æ¹æ¡:

*   ä½¿ç¨ä¸ä¸ª**ä¸ç¨åº**æ¥è·è¸ªä½¿ç¨è¿çèæ¬ãååæ ·å¼ï¼å¹¶å°å®ä»¬å è½½å°å®¢æ·ç«¯(å°¤å¶æ¯æ ·å¼ï¼)å¨è¿è¡æ°´åååºä¹åãå¦åââä½ å°åç°ç©ºæ´èä¸æ¯ä½ çä»£ç åå²ç»ä»¶ãåä¸æ¬¡ï¼ä½ æ²¡æå è½½ä½ åæåå²çä»£ç ï¼æä»¥ä½ ä¸è½æ¸²æä»»ä½ä½ å°è¦æ¸²æçä¸è¥¿ã

### çä»£ç åè§£åº

*   éç¨ç»ä»¶(Universal-component)-æå¤èçãä»å¯ç»´æ¤çåºãå®âåæâäºä»£ç åå²ï¼å¹¶æä¼äº Webpack ä»£ç åå²ã
*   éå¸¸æµè¡ï¼ä½æ¯æ äººç»´æ¤çåºãè®©ä»£ç åæ§½æä¸ºä¸ä»¶æµè¡çäºæãé®é¢æ¯å°é­çï¼æä»¥å¨å´æ²¡æç¤¾åºã
*   å¯å è½½ç»ä»¶(Loadable-components)âä¸ä¸ªåè½å®æ´çåºï¼ä½¿ç¨èµ·æ¥å¾æå¿«ï¼æ¥æææ´»è·çç¤¾åºã
*   [å¯¼å¥ç»ä»¶](https://www.npmjs.com/package/react-imported-component)âåä¸ªåºï¼ä¸ç»å®å° Webpackï¼å³è½å¤å¤çåè£¹æ esmã
*   React-async-componentâå·²ç»æ­»äºçåº(ä½å¾æµè¡)ï¼å®å¯¹ä»£ç åå²ãèªå®ä¹ React æ éåå SSR äº§çäºéå¤§å½±åã
*   å¦ä¸ä¸ªåºââæå¾å¤åºï¼å¶ä¸­å¾å¤æ²¡æéè¿ Webpack evolution æ React 16ââææ²¡æå¨è¿éååºå®ä»¬ï¼ä½æ¯å¦æä½ ç¥éä¸ä¸ªå¥½çåéåºââå°±ç»æåé®ä»¶ã

### æåªä¸ªåºï¼

è¿å¾ç®åâ**ä¸å¯ååºå è½½**ââå®å¾é¾ç»´æ¤ï¼èä¸å·²ç»è¿æ¶ï¼å³ä½¿å®ä»ç¶éå¸¸æµè¡ã(åæ¬¡æè°¢æ¨æ®åä»£ç åå²)

å¯å è½½ç»ä»¶âå¯è½æ¯ä¸ä¸ªéå¸¸å¥½çéæ©ãå®åå¾éå¸¸å¥½ï¼ç§¯æç»´æ¤å¹¶æ¯æå¼ç®±å³ç¨çä¸åãæ¯æâå®å¨å¨æå¯¼å¥âï¼åè®¸ä½ æ ¹æ®ç»å®çå±æ§å¯¼å¥æä»¶ï¼ä½æ¯ä¸å¯ç±»ååãæ¯ææ¬å¿µï¼æä»¥å¯ä»¥æ¿æ¢ React.lazyã

*éç¨ç»ä»¶*ââå®éä¸æ¯å®å¨å¨æå¯¼å¥çâåæèâââä»ä»¬å¨ Webpack ä¸­å®ç°äºå®ãä»¥åå¶ä»è®¸å¤ä½çº§å«çäºæââä»ä»¬åå°äºãæä¼è¯´ââè¿ä¸ªåºæç¹ç¡¬ï¼æç¹ä¸å¤ªç¨æ·åå¥½ãå¯å è½½ç»ä»¶ææ¡£æ¯æ ä¸ä¼¦æ¯çãå¦æä¸ä½¿ç¨è¿ä¸ªåºï¼é£ä¹éè¯»ææ¡£æ¯å¼å¾çââæå¾å¤ç»èä½ åºè¯¥ç¥éâ¦

*React-imported-component*âæç¹å¥æªãå®æ¯ç¬ç«äº bundler çï¼æä»¥å®æ°¸è¿ä¸ä¼ä¸­æ­(æ²¡æä»ä¹å¯ä»¥ä¸­æ­)ï¼å¯ä»¥ä¸ Webpack 5 å 55 ä¸èµ·å·¥ä½ï¼ä½è¿æ¯æä»£ä»·çãè½ç¶ SSR æé´ä»¥åçåºä¼å°ææä½¿ç¨çèæ¬æ·»å å°é¡µé¢ä¸»ä½ï¼å¹¶ä¸æ¨å°è½å¤å¹¶è¡å è½½ææèæ¬ââå¯¼å¥ä¸ç¥éæä»¶åï¼å¹¶ä¸å°è°ç¨åå§çâå¯¼å¥â(è¿å°±æ¯ä¸ºä»ä¹ç¬ç«äº bundle)æ¥å è½½ä½¿ç¨çåï¼ä½æ¯åªè½ä»ä¸» bundle åé¨è¿è¡è°ç¨ââå æ­¤ææéå èæ¬å°ä»å¨ä¸» bundle ä¸è½½å¹¶æ§è¡åå è½½ãä¸æ¯æå®å¨å¨æå¯¼å¥ï¼å¦ React.lazyï¼å æ­¤ä¹ä¸æ¯æ typeableãä¹æ¯ææ¬çãå¨ SSR ä¸ä½¿ç¨åæ­¥*æ ç­¾*ãå®è¿æä¸ä¸ªå®å¨ä¸åç CSS æ¹æ³ï¼ä»¥åå®ç¾çæµåç°æ¯æã

ååºçå¾ä¹¦é¦å¨è´¨éæåæ¬¢è¿ç¨åº¦ä¸æ²¡æåºå«ï¼æä»¬é½æ¯å¥½æåââæä»¥ç¨å¿æéå§ã

## ![GitHub logo](img/375dfcc32199b4dedf2b526645c27ff7.png) [å¹³æ»ç ](https://github.com/smooth-code) / [å¯å è½½ç»ä»¶](https://github.com/smooth-code/loadable-components)

### ååºä»£ç åè£ä½¿âï¸â¨åå¾å®¹æ

<article class="markdown-body entry-content p-5" itemprop="text">

# [![loadable-components](img/85e80c9e998d91b8f57e7c301e0771c9.png "loadable-components")](https://raw.githubusercontent.com/smooth-code/loadable-components/master/resources/loadable-components.png)

React ä»£ç åå²åå¾ç®åãæ¯«æ ååå°åå°ä½ çåè£¹å°ºå¯¸<g-emoji class="g-emoji" alias="scissors" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2702.png">âï¸</g-emoji>T2ãâ¨ã

[![License](img/f2b7f6cb97c2c07254a6c79d3651d1ff.png)](https://github.com/smooth-code/loadable-components/blob/master/LICENSE)[![Donate](img/cc5eb5c85924ec12f345cb35cdcc378a.png)](https://opencollective.com/loadable/donate)[![npm package](img/a62cef6ce5ed5431fa94a2a959cdcaf9.png)](https://www.npmjs.com/package/@loadable/component)[![npm downloads](img/4001c0be8ed4cbc98936c4090c536fef.png)](https://www.npmjs.com/package/@loadable/component)[![Build Status](img/f3706fa73231b5649a4306f7d10f97ed.png)](https://travis-ci.org/smooth-code/loadable-components)[![Code style](img/2113a413b334e649fd64b7626cd40572.png)](https://camo.githubusercontent.com/c83b8df34339bd302b7fd3fbb631f99ba25f87f8/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f636f64655f7374796c652d70726574746965722d6666363962342e737667)[![Dependencies](img/fd3f77f8b314a892ceb00cc02e56c6a8.png)](https://david-dm.org/smooth-code/loadable-components?path=packages/component)[![DevDependencies](img/014fa38cdadc9b7bfda1e9e7ca4bac54.png)](https://david-dm.org/smooth-code/loadable-components?type=dev)[T25ã](https://unpkg.com/@loadable/component/dist/loadable.min.js)

```
npm install @loadable/component
```

## [ææ¡£](https://www.smooth-code.com/open-source/loadable-components)

å³äºä½¿ç¨å¯å è½½ç»ä»¶çæ´å¤ä¿¡æ¯ï¼åè§[smooth-code.com/open-source/loadable-components](https://www.smooth-code.com/open-source/loadable-components)T3 çææ¡£ï¼

å¿«éé¾æ¥å°ä¸äºæå¸¸è®¿é®çé¡µé¢:

*   [**å¥é¨**T3ã](https://www.smooth-code.com/open-source/loadable-components/docs/getting-started/)
*   [ä¸ React.lazy çæ¯è¾](https://www.smooth-code.com/open-source/loadable-components/docs/loadable-vs-react-lazy/)
*   [æå¡å¨ç«¯æ¸²æ](https://www.smooth-code.com/open-source/loadable-components/docs/server-side-rendering/)

## ä¾å­

```
import loadable from '@loadable/component'
const OtherComponent = loadable(() => import('./OtherComponent'))
function MyComponent() {
  return (
    <div>
      <OtherComponent />
    </div>
  )
}
```

## æ¯æå¯å è½½ç»ä»¶

å¯å è½½ç»ä»¶æ¯ä¸ä¸ªéº»ççå·¥å­¦é¢è®¸å¯çå¼æºé¡¹ç®ãè¿æ¯ä¸ä¸ªç¬ç«çé¡¹ç®ï¼ç±äºè¿äºä»¤äººæ¬çç[æ¯æè](https://raw.githubusercontent.com/smooth-code/loadable-components/master//BACKERS.md)çæ¯æï¼æ­£å¨è¿è¡çå¼åææä¸ºå¯è½ãå¦æä½ æ³å å¥ä»ä»¬ï¼è¯·èè:

*   [æä¸º OpenCollective](https://opencollective.com/loadable) çæ¯æèæèµå©åã

### éçèµå©å

éçèµå©åæ¯é£äºæ¿è¯ºæ¯æ 100 ç¾åææ´å¤çäººã

[![gold-sponsors](img/e8592dca693d4de180790748b0466ef7.png)](https://camo.githubusercontent.com/db285608ed1b7de56900dbbb8179948af4886415/68747470733a2f2f6f70656e636f6c6c6563746976652e636f6d2f6c6f616461626c652f74696572732f676f6c642d73706f6e736f72732e7376673f6176617461724865696768743d3132302677696474683d363030)

## è®¸å¯è¯

æ ¹æ®éº»ççå·¥å­¦é¢è®¸å¯è¯ææï¼çæææâ¦

</article>

[View on GitHub](https://github.com/smooth-code/loadable-components)

## ![GitHub logo](img/375dfcc32199b4dedf2b526645c27ff7.png) [å¡è¥¿](https://github.com/theKashey) / [ååº-è¿å£-ç»ä»¶](https://github.com/theKashey/react-imported-component)

### âï¸ð¦SSR åå¥½ä»£ç æåçç¬ç«äº Bundler çè§£å³æ¹æ¡

<article class="markdown-body entry-content p-5" itemprop="text">

# è¿å£ç»ä»¶â

## å§ç»ææçä»£ç æå <sup>*</sup>

[![imported components](img/9484e17309a3eb1b982175dea5d11586.png)](https://raw.githubusercontent.com/theKashey/react-imported-component/master/./assets/imported-logo.png)

SSR-friendly code splitting compatible with any platform
Deliver a better experience within a single import

[![](img/18c096915e5ecca8b9c64ae7cbc018d1.png) ](https://www.npmjs.com/package/react-imported-component) [ ![Build status](img/aa1d04bfe2bdb043f83b2ff81b273937.png) ](https://circleci.com/gh/theKashey/react-imported-component/tree/master) [ ![npm downloads](img/d1dfa3df5184feb4b90ce2f38889e7f9.png) ](https://www.npmjs.com/package/react-imported-component) [ ![bundle size](img/bda8df20174fa7e1e4b4482bb2915a82.png) ](https://bundlephobia.com/result?p=react-imported-component) [![](img/cb954434d0af1afe5e173e7c0e261142.png)](https://camo.githubusercontent.com/ca8f583465c5a251169737a84d9725d17732945b/68747470733a2f2f6261646765732e677265656e6b65657065722e696f2f7468654b61736865792f72656163742d696d706f727465642d636f6d706f6e656e742e737667)

> <sup>*</sup> å®ççæ°¸è¿ä¸ä¼è®©ä½ å¤±æãä¸åé½å½åäºä½ ç bundlerã

<g-emoji class="g-emoji" alias="point_right" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f449.png">ð</g-emoji> [ç¨æ³](https://raw.githubusercontent.com/theKashey/react-imported-component/master/#usage) | [API](https://raw.githubusercontent.com/theKashey/react-imported-component/master/#api) | [è®¾ç½®](https://raw.githubusercontent.com/theKashey/react-imported-component/master/#setup)|[SSR](https://raw.githubusercontent.com/theKashey/react-imported-component/master/#ssr)|[CCS](https://raw.githubusercontent.com/theKashey/react-imported-component/master/#css)[å¹¶åå è½½](https://raw.githubusercontent.com/theKashey/react-imported-component/master/#concurrent-loading)|[web pack/package](https://raw.githubusercontent.com/theKashey/react-imported-component/master/#bundler-integration)

| å¾ä¹¦é¦ | ç¦è | èç»´åç¤¾ä¼ä¸»ä¹å±åå½ | é©ä½ | å¾ä¹¦é¦ | éæ¨¡å | å¯¼å¥(`./${value}`) | å·´å«å¡å® | ä» webpack |
| --- | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
| ååº.ææ° | <g-emoji class="g-emoji" alias="white_check_mark" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2705.png">â</g-emoji> | <g-emoji class="g-emoji" alias="x" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/274c.png">â</g-emoji> | <g-emoji class="g-emoji" alias="x" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/274c.png">â</g-emoji> | <g-emoji class="g-emoji" alias="x" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/274c.png">â</g-emoji> | <g-emoji class="g-emoji" alias="x" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/274c.png">â</g-emoji> | <g-emoji class="g-emoji" alias="x" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/274c.png">â</g-emoji> | <g-emoji class="g-emoji" alias="joy_cat" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f639.png">ð¹</g-emoji> |
| å¯åä½ç¨å è½½ | <g-emoji class="g-emoji" alias="white_check_mark" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2705.png">â</g-emoji> | <g-emoji class="g-emoji" alias="white_check_mark" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2705.png">â</g-emoji> | <g-emoji class="g-emoji" alias="x" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/274c.png">â</g-emoji> | <g-emoji class="g-emoji" alias="x" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/274c.png">â</g-emoji> | <g-emoji class="g-emoji" alias="white_check_mark" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2705.png">â</g-emoji> | <g-emoji class="g-emoji" alias="x" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/274c.png">â</g-emoji> | <g-emoji class="g-emoji" alias="x" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/274c.png">â</g-emoji> | <g-emoji class="g-emoji" alias="crying_cat_face" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f63f.png">ð¿</g-emoji> |
| @å¯å è½½/ç»ä»¶ | <g-emoji class="g-emoji" alias="white_check_mark" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2705.png">â</g-emoji> | <g-emoji class="g-emoji" alias="white_check_mark" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2705.png">â</g-emoji> | <g-emoji class="g-emoji" alias="x" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/274c.png">â</g-emoji> | <g-emoji class="g-emoji" alias="white_check_mark" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2705.png">â</g-emoji> | <g-emoji class="g-emoji" alias="x" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/274c.png">â</g-emoji> | <g-emoji class="g-emoji" alias="white_check_mark" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2705.png">â</g-emoji> | <g-emoji class="g-emoji" alias="x" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/274c.png">â</g-emoji> | <g-emoji class="g-emoji" alias="crying_cat_face" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f63f.png">ð¿</g-emoji> |
| è¿å£ç»ä»¶ | <g-emoji class="g-emoji" alias="white_check_mark" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2705.png">â</g-emoji> | <g-emoji class="g-emoji" alias="white_check_mark" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2705.png">â</g-emoji> | <g-emoji class="g-emoji" alias="white_check_mark" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2705.png">â</g-emoji> | <g-emoji class="g-emoji" alias="white_check_mark" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2705.png">â</g-emoji> | <g-emoji class="g-emoji" alias="white_check_mark" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2705.png">â</g-emoji> | <g-emoji class="g-emoji" alias="x" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/274c.png">â</g-emoji> | <g-emoji class="g-emoji" alias="white_check_mark" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2705.png">â</g-emoji> | <g-emoji class="g-emoji" alias="smile_cat" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f638.png">ð¸</g-emoji> |

> éè¯»æ´å¤å³äº[è¯¥è¡¨æ¾ç¤ºçåå®¹](https://raw.githubusercontent.com/theKashey/react-imported-component/master/#comparisonLegend)

ä¸»è¦ç¹ç¹:

*   <g-emoji class="g-emoji" alias="one" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/0031-20e3.png">1ï¸â£</g-emoji> çç¸çå¯ä¸æ¥æºââä½ ç**æç»èé©±å¨**ä¸å
*   <g-emoji class="g-emoji" alias="book" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4d6.png">ð</g-emoji> **åº**çº§ä»£ç **æå**
*   ð§ï¸æ··åå¨åè½¦ä¸**é¢æ¸²æ**å¼å®¹
*   <g-emoji class="g-emoji" alias="bulb" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4a1.png">ð¡</g-emoji> **æå­ç¨¿**è£è®¢
*   <g-emoji class="g-emoji" alias="atom_symbol" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/269b.png">**ååºè¿æ¥ãåºå±çæ°æ§**(å¦æç­æ¨¡åæ´æ°è¢«ç¦ç¨)</g-emoji>
*   <g-emoji class="g-emoji" alias="star2" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f31f.png">ð</g-emoji>å®¢æ·ç«¯å¼æ­¥ï¼æå¡å¨åæ­¥ãæ¯æ**æ¬å¿µ**(è¿ä¸â¦

</article>

[View on GitHub](https://github.com/theKashey/react-imported-component)

## ![GitHub logo](img/375dfcc32199b4dedf2b526645c27ff7.png)[faceyspacey](https://github.com/faceyspacey)/[react-universal-component](https://github.com/faceyspacey/react-universal-component)

### ðReact éç¨ç»ä»¶çæç»ç­æ¡:åæ­¥ SSR +ä»£ç æå

<article class="markdown-body entry-content p-5" itemprop="text">[![Reactlandia Chat](img/e89a531afc57043ee49077b15f3b9cfb.png) ](https://gitter.im/Reactlandia/Lobby) [ ![Edit React Universal Component on StackBlitz](img/bf36868e3b129d50b1d984e5b56c1ed9.png) ](https://stackblitz.com/edit/react-pnzphy) [![Edit React Universal Component on CodeSandBox](img/256aaad2b17afd53ba9f392eb8778e7b.png)](https://codesandbox.io/s/github/faceyspacey/redux-first-router-codesandbox/tree/master/?module=r1oVP5YEUZ) 

# ååºéç¨ç»ä»¶

[![Version](img/64dfe6cfd5f4f2d6fa025f5799762e43.png)](https://www.npmjs.com/package/react-universal-component)[![Build Status](img/af74684d3094391759ec1cc6f03da2d9.png)](https://travis-ci.org/faceyspacey/react-universal-component)[![Coverage Status](img/49d1e1d003527d2fefcc8150465b9a6f.png)](https://lima.codeclimate.com/github/faceyspacey/react-universal-component/coverage)[![Downloads](img/d2301e9258121bb06abcee531ba1ee65.png)](https://www.npmjs.com/package/react-universal-component)[![License](img/1b5ca352d0d6367c430fc120a6303a74.png)](https://www.npmjs.com/package/react-universal-component)

[![](img/6e7f9cef670b55dc2c6e3fc4ac1b4eb1.png)](https://camo.githubusercontent.com/20394442136fc23def46fef4750555939814f80b/68747470733a2f2f63646e2e72656163746c616e6469612e636f6d2f756e6976657273616c2d636f6d706f6e656e742d62616e6e65722e706e67)

<g-emoji class="g-emoji" alias="champagne" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f37e.png">ð¾</g-emoji> <g-emoji class="g-emoji" alias="champagne" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f37e.png">ð¾</g-emoji> <g-emoji class="g-emoji" alias="champagne" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f37e.png">ð¾</g-emoji> [GIT åé 3.0 æ¬å°æ¼ç¤º](https://github.com/faceyspacey/universal-demo) <g-emoji class="g-emoji" alias="rocket" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f680.png">ð</g-emoji> <g-emoji class="g-emoji" alias="rocket" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f680.png">ð</g-emoji>T12ãð

*   [ååºéç¨ç»ä»¶](https://raw.githubusercontent.com/faceyspacey/react-universal-component/master/#react-universal-component)
    *   [ç®ä»](https://raw.githubusercontent.com/faceyspacey/react-universal-component/master/#intro)
    *   [æ¯ä»ä¹è®©éç¨æ¸²æå¦æ­¤çè¦](https://raw.githubusercontent.com/faceyspacey/react-universal-component/master/#what-makes-universal-rendering-so-painful)
    *   [å®è£](https://raw.githubusercontent.com/faceyspacey/react-universal-component/master/#installation)
    *   [æ¨å°éè¦ææ³è¦çå¶ä»åè£](https://raw.githubusercontent.com/faceyspacey/react-universal-component/master/#other-packages-you-will-need-or-want)
    *   [API åéé¡¹](https://raw.githubusercontent.com/faceyspacey/react-universal-component/master/#api-and-options)
    *   [SSR çå²æ´](https://raw.githubusercontent.com/faceyspacey/react-universal-component/master/#flushing-for-ssr)
    *   [é¢è½½](https://raw.githubusercontent.com/faceyspacey/react-universal-component/master/#preload)
    *   [éæåè£](https://raw.githubusercontent.com/faceyspacey/react-universal-component/master/#static-hoisting)
    *   [éå· API](https://raw.githubusercontent.com/faceyspacey/react-universal-component/master/#props-api)
    *   [èªå®ä¹æ¸²æ](https://raw.githubusercontent.com/faceyspacey/react-universal-component/master/#custom-rendering)
    *   [ä¸ CSS-in-JS åºä¸èµ·ä½¿ç¨](https://raw.githubusercontent.com/faceyspacey/react-universal-component/master/#usage-with-css-in-js-libraries)
    *   [ä½¿ç¨ä¸¤é¶æ®µæ¸²æ](https://raw.githubusercontent.com/faceyspacey/react-universal-component/master/#usage-with-two-stage-rendering)
    *   [éç¨æ¼ç¤º](https://raw.githubusercontent.com/faceyspacey/react-universal-component/master/#universal-demo)
    *   [æç¨¿](https://raw.githubusercontent.com/faceyspacey/react-universal-component/master/#contributing)
    *   [æµè¯](https://raw.githubusercontent.com/faceyspacey/react-universal-component/master/#tests)
    *   [æ´å¤æ¥èª FaceySpacey](https://raw.githubusercontent.com/faceyspacey/react-universal-component/master/#more-from-faceyspacey-in-reactlandia)

## ä»ç»

å¯¹äºâè¶çº§ç¨æ·âæ¥è¯´ï¼ä¼ ç»çæ°´çå·²ç»æ­»äºãå¦æä½ æ²¡æå¨æå¡å¨ä¸å¨é¢æ¸²æï¼ä½ å°±æçªæ¯æç´¢å¼æå¯è§æ§çé£é©ãå°±ç®åæåµæ¥çï¼SEO åå®¢æ·ç«¯æ¸²æé½ä¸æ¯ SSR çå¯¹æãå³ä½¿è®¸å¤æç´¢å¼æå£°ç§°æ´å¥½ç SPA ç´¢å¼ï¼ä¹æè®¸å¤è­¦åã**æå¡å¨ç«¯æ¸²æè³å³éè¦: [JavaScript & SEO éå¾å¶åââHulu.com æ¡ä¾ç ç©¶](https://www.elephate.com/blog/javascript-seo-backfire-hulu-com-case-study/)**

çæ­£çé®é¢æ¯**åæ­¥ SSR +åè£**ãå¦æä½ æ¾ç»å°è¯è¿è¿æ ·çè¯ï¼*ä½ ç¥é*ãè¿éæä¸ä¸ªç¬ä¸æ äºçè§£å³æ¹æ¡ï¼å®è½å¸¦æ¥è¿ä¸åâ¦

</article>

[View on GitHub](https://github.com/faceyspacey/react-universal-component)

## ç°è²åºå 3âæ··åæ¸²æ

SSR æ¯ä¸ªå¥½ä¸è¥¿ï¼ä½æ¯ï¼ä½ ç¥éï¼å¾é¾ãå°é¡¹ç®å¯è½æ³è¦æä¸ä¸ª SSRââæå¾å¤çç±éè¦å®ââä½æ¯ä»ä»¬å¯è½ä¸æ³è®¾ç½®åç»´æ¤å®ã

> SSR å¯è½ä¼éå¸¸éå¸¸é¾ãå¦æä½ æ³å¿«éåèï¼è¯è¯`razzle`æéæ©`Next.js`ã

å æ­¤ï¼å¯¹äº SSRï¼å°¤å¶æ¯å¯¹äºç®åç SPAï¼æç®åçè§£å³æ¹æ¡æ¯é¢æ¸²æãæ¯å¦å¨æµè§å¨ä¸­æå¼ä½ çæ°´çä¸­å¿ï¼ç¹å»âä¿å­âæé®ãæ¯å¦:

*   ä½¿ç¨[æ¨å¶å¸](https://github.com/GoogleChrome/puppeteer)(ååæ å¤´æµè§å¨)å¨âæµè§å¨âä¸­æ¸²æä½ çé¡µé¢ï¼å¹¶å°ç»æä¿å­ä¸ºéæ HTML é¡µé¢ã
*   Rendertron -ååæ ·çäºæï¼ä½æ¯ä»¥ä¸åçæ¹å¼(*äº*)ã

é¢æ¸²ææ¯æ²¡æâæå¡å¨âçâSSRâãæ¯ SSR ä½¿ç¨å®¢æ·ç«¯ãç¥å¥ï¼å¼ç®±å³ç¨â¦ â¦ä½ä¸æ¯ä¸ºäºä»£ç åè§£ãæä»¥ââä½ åªéå¨æµè§å¨ä¸­åç°ä½ çé¡µé¢ï¼ä¿å­ HTMLï¼å¹¶è¦æ±å è½½åæ ·çåå®¹ãä½æ¯æå¡å¨ç«¯çç¹å®ä»£ç (æ¶éææä½¿ç¨è¿çå)æ²¡æè¢«ä½¿ç¨ï¼å ä¸ºæ²¡ææå¡å¨ï¼

[![](img/9d439aad467e99eae320e88c6078df35.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--e558xCgt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://habrastorage.org/webt/tr/sz/9i/trsz9i4ct8jc7cnwxtm-ljnepuq.png)

å¨åä¸é¨åä¸­ï¼æå·²ç»æåºäºå¨æ¶éå³äºä½¿ç¨çåçä¿¡æ¯æ¹é¢ç»å®å° webpack çåºââå®ä»¬æ ¹æ¬ä¸è½å¤çæ··åæ¸²æã

> react-snap é¨åæ¯æå¯å è½½ç»ä»¶çæ¬ 2(ä¸å½åçæ¬ 5 ä¸å¼å®¹)ãæ¯ææ²¡æäºã

React-imported-component å¯ä»¥å¤çè¿ç§æåµï¼åªè¦å®æ²¡æç»å®å° bundler/sideï¼æä»¥å¯¹äº SSR æ Hybrid æ²¡æåºå«ï¼åªæ¯å¯¹äº`react-snap`ï¼åªè¦å®æ¯æâç¶ææ°´åâï¼è`rendertron`ä¸æ¯æã

> react-imported-component çè¿ç§è½åæ¯å¨æ°åæ¬ææ¶åç°çï¼ä»¥åå¹¶ä¸ç¥éââåè§[ç¤ºä¾](https://github.com/theKashey/react-imported-component/tree/master/examples/hybrid/react-snap)ãè¿å¾å®¹æã

è¿éä½ ä¸å¾ä¸ä½¿ç¨å¦ä¸ç§è§£å³æ¹æ¡ï¼å®ä¸ææå¶ä»åºé½æ¯åç´çã

### ååº-é¢æ¸²æ-ç»ä»¶

è¿ä¸ªåºæ¯ä¸ºé¨åæ°´åèåå»ºçï¼å¯ä»¥é¨åæ°´åä½ çåºç¨ç¨åºï¼ä¿æå¶ä½é¨åä»ç¶è±æ°´ãå®éç¨äº SSR åæ··åæ¸²æå¨ï¼æ²¡æä»»ä½åºå«ãè¿ä¸ªæ³æ³å¾ç®å:

*   å¨ SSR - render ç»ä»¶æé´ï¼ç¨
*   å¨å®¢æ·ç«¯ââæ¾å° divï¼ä½¿ç¨ innerHTMLï¼ç´å°ç»ä»¶åå¤å¥½æ¿æ¢æ­» HTMLã
*   ä½ ä¸å¿å è½½ï¼å¹¶ç­å¾ä¸ä¸ªå¸¦æåå²ç»ä»¶çå*ä¸åç°ä¸ä¸ªç½æ´æ¥ä»£æ¿å®*ââåªéä½¿ç¨é¢ååç°ç HTMLï¼å®ç»å¯¹ç­äºä¸ä¸ªçå®ç»ä»¶å°åç°ç*ï¼å¹¶ä¸å®å·²ç»å­å¨ââå®å¸¦æä¸ä¸ªæå¡å¨(ææ··å)ååºã*

> è¿å°±æ¯ä¸ºä»ä¹æä»¬å¿é¡»å¨ hydrate - to **å¹é**æå¡å¨åç°ç HTML ä¹åç­å¾ææçåå è½½ãè¿å°±æ¯ä¸ºä»ä¹æä»¬å¯ä»¥ä½¿ç¨æå¡å¨æ¸²æç HTML çæ®µï¼ç´å°å®¢æ·ç«¯æ²¡æåå¤å¥½ââå®ç¸å½äºæä»¬åªåå¤çäº§çä¸ä¸ªã

```
import {PrerenderedComponent} from 'react-prerendered-component';
const importer = memoizeOne(() => import('./Component'));
// ^ it's very important to keep the "one" promise
const Component = React.lazy(importer); 
// or use any other library with ".prefetch" support
// all libraries has it (more or less)
const App = () => (
  <PrerenderedComponent live={importer()}> 
   {/* ^ shall return the same promise */ }
      <Component /> 
   {/* ^ would be rendered when component goes "live" */ }
  </PrerenderedComponent> ); 
```

## ![GitHub logo](img/375dfcc32199b4dedf2b526645c27ff7.png) [å¡è¥¿](https://github.com/theKashey) / [ååº-é¢æ¸²æ-ç»ä»¶](https://github.com/theKashey/react-prerendered-component)

### ð¤æ¬çåæ¶ä»£çé¨åæ°´ååç¼å­

<article class="markdown-body entry-content p-5" itemprop="text">

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

### TLDRï¼

*   ä¸è¦ä½¿ç¨ react-loadableï¼å®ä¸ä¼å¢å ä»»ä½æä»·å¼çä»·å¼
*   ææ°æ¯å¥½çï¼ä½æ¯å¤ªç®åäºã
*   SSR æ¯ä¸ä»¶å¾é¾çäºæï¼ä½ åºè¯¥ç¥é
*   æ··åæ¨å¶å¸é©±å¨çæ¸²ææ¯ä¸ä»¶äºãææ¶çè³æ´é¾çäºæã
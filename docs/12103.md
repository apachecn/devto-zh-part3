# 使用网络信息 API 服务自适应组件

> 原文：<https://dev.to/vorillaz/serving-adaptive-components-using-the-network-information-api-lbo>

在过去的几年里，我们一直在以性能为中心进行开发。自适应 web 开发需要考虑我们的最终用户，为低端设备和互联网连接开发体验和产品，而不牺牲我们的工作质量。

## 网络信息 API

[网络信息 API](http://w3c.github.io/netinfo/) 允许我们重新考虑我们的设计，并帮助我们创建用户界面，因为我们可以检测用户的连接速度并采取行动。该 API 还处于实验阶段，但已经可以在 Chrome 中使用，不久的将来会有更多的浏览器推出。

我们可以使用只读属性来使用 API。嵌套的`navigator.connection.effectiveType`属性公开了使用的网络。除了`effectiveType`属性，`navigator.connection.type`还公开了用户的物理网络类型。
还公开了关于往返时间度量和有效带宽估计的附加信息。

下表定义了规范中所示的有效连接类型。

| 电惊厥疗法 | 最小 RTT(毫秒) | 最大下行链路(Kbps) | 说明 |
| --- | --- | --- | --- |
| 慢速-2g | Two thousand | Fifty | 该网络仅适用于小型传输，如纯文本页面。 |
| 2g | One thousand four hundred | Seventy | 该网络适合传输小图像。 |
| 3g | Two hundred and seventy | Seven hundred | 该网络适合传输大型资产，如高分辨率图像、音频和标清视频。 |
| 第四代移动通信技术 | Zero | ∞ | 该网络适用于高清视频、实时视频等。 |

## 带有 React / Preact 的自适应组件。

我们可以使用网络 API 加速我们的性能指标，特别是对于网络消耗组件。例如，假设我们有一个简单的 React 组件，它以不同的分辨率和大小呈现不同的图像。该组件应该是网络感知的，并有效地处理连接类型。此外，使用`navigator.onLine`属性，我们可以检测离线使用，将 pwa 与自适应组件和离线检测相结合，从而为我们的用户提供一流的体验。

我们的`<Img />`组件将有效地呈现如下所示的输出:

*   4g:高分辨率图像(2400 像素)
*   3h:中等分辨率图像(1200 像素)
*   2g:低分辨率图像(600 像素)
*   脱机:警告用户的占位符

使用 React，我们将创建一个网络感知的组件。我们的天真组件将接受一个`src`属性，并作为前缀图像:
如果`src`等于`my-awesome-image.jpg`，则相对输出可能是`hq-my-awesome-image.jpg`和`md-my-awesome-image.jpg`，`lofi-my-awesome-image.jpg`。

我们将首先创建一个简单的 React 组件，如下所示:

```
import React, {Component} from 'react';

export default class Img extends Component {
  render() {
    const {src} = this.props;
    return (<img src={src}/>)
  }
} 
```

接下来，我们将创建一个私有方法来检测网络变化:

```
class Img extends Component {
  //...
  detectNetwork = () => {
    const {connection = null, onLine = false} = navigator;
    if (connection === null) {
      return 'n/a';
    }
    if(!onLine) {
      return 'offline';
    }
    return {effectiveType = '4g'} = connection;
  }
  //...
} 
```

最后，我们应该将输出呈现为:

```
class Img extends Component {
  //...
  render() {
    const {src, ...rest} = this.props;
    const status = this.detectNetwork();
    // The network API is not available :()
    if (status === 'n/a') {
      return <img src={src} {...rest}/>
    }
    if (status === 'offline') {
      return <div>You are currently offline</div>
    }
    const prefix = status === '4g' ? 'hq' : status === '3g' ? 'md' : 'lofi';
    return <img src={`${prefix}-${src}`} {...rest}/>
  }
  //...
} 
```

## 高阶分量

高阶组件可以扩展您的设计系统，并为以更优雅的方式处理网络感知组件提供事实上的解决方案。

```
const emptyComponent = () => null;

const detectNetwork = () => {
  const {connection = null, onLine = false} = navigator;
  if (connection === null) {
    return 'n/a';
  }
  if (!onLine) {
    return 'offline';
  }
  return ({effectiveType = '4g'} = connection);
};

const withNetwork = (
  components = {
    '4g': emptyComponent,
    '3g': emptyComponent,
    '2g': emptyComponent,
    offline: emptyComponent,
    'n/a': emptyComponent
  }
) => props => {
  const status = detectNetwork();
  const NetworkAwareComponent = components[status];
  return <NetworkAwareComponent {...props} />;
}; 
```

消耗高阶分量非常简单:

```
import React from 'react';
import withNetwork from './hocs//withNetwork';

export default withNetwork({
  offline: () => <div>This is offline</div>,
  '4g': () => <div>This is 4g</div>,
  '3g': () => <div>This is 3g</div>,
  '2g': () => <div>This is 2g</div>,
  'n/a': () => <div>Network API is not supported 🌐</div>,
}); 
```

我们还可以稍微简化高阶组件，并将`fast`和`slow`网络连接的组件区分为:

```
const detectNetwork = () => {
  const {connection = null, onLine = false} = navigator;
  if (connection === null) {
    return 'n/a';
  }
  if (!onLine) {
    return 'offline';
  }
  const {effectiveType = '4g'} = connection;
  return (/\slow-2g|2g|3g/.test(effectiveType)) ? 'slow' : 'fast';
}; 
```

## 动态加载与反应

使用`react-loadable`,我们可以把这个例子做得更进一步，用动态导入异步加载我们的组件。通过这种方式，我们可以根据需要为更快的网络加载重量级数据块。

```
import React from 'react';
import withNetwork from './hocs/withNetwork';

import Loadable from 'react-loadable';

const HiQ = Loadable({
  loader: () => import('./hiQualityImg')
});

// For slow networks we don't want to create a network overhead
const SlowNetworkComponent = () => <div>That's slow or offline</div>;

export default withNetwork({
  offline: () => <div>This is offline</div>,
  '4g': () => <HiQ />,
  '3g': () => <SlowNetworkComponent />,
  '2g': () => <SlowNetworkComponent />,
  'n/a': () => <SlowNetworkComponent />
}); 
```

## Vue 组件

[Addy Osmani](https://addyosmani.com/blog/adaptive-serving/) 有一个使用 Vue 和自适应组件的很好的例子。一个样例 Vue 组件如下所示:

```
<template>
  <div id="home">
    <div v-if="connection === 'fast'">
      <img src="./hq-image.jpg" />
    </div>
    <div v-if="connection === 'slow'">
      <img src="./lofi-image.jpg" />
    </div>
  </div>
</template> 
```

## Vue 动态加载

Vue 可以使用条件导入优雅地处理动态加载:

```
Vue.component(
  'async-network-example',
  // The `import` function returns a Promise.
  () => detectNetwork() === 'fast' ? import('./hq-component') : import('./lofi-component')
); 
```

## Web 组件

最后，我们可以使用 [web 组件](https://developer.mozilla.org/en-US/docs/Web/Web_Components)而不需要任何额外的框架来创建可重用的组件，供我们以后使用。
一个简单的方法是这样的:

```
const detectNetwork = () => {
  const { connection = null, onLine = false } = navigator;
  if (connection === null) {
    return "n/a";
  }
  if (!onLine) {
    return "offline";
  }
  const { effectiveType = "4g" } = connection;
  return /\slow-2g|2g|3g/.test(effectiveType) ? "slow" : "fast";
};

export class NetworkMedia extends HTMLElement {
  constructor() {
    super();
    const shadowRoot = this.attachShadow({ mode: "open" });

    const parsed = this.getAttributeNames().reduce((acc, key) => {
      return { ...acc, [key]: this.getAttribute(key) };
    }, {});
    const status = detectNetwork();
    const { hq, lofi, ...rest } = parsed;
    const htmlAttrs = Object.assign({}, rest, {
      src: status === "fast" ? hq : lofi
    });

    const attrs = Object.keys(htmlAttrs)
      .map(key => `${key}=${htmlAttrs[key]}`)
      .join("  ");
    shadowRoot.innerHTML = `
            <img ${attrs} />
        `;
  }
} 
```

我们需要声明 web 组件并最终使用它。

```
import { NetworkMedia } from "./network-media.js";

customElements.define("network-media", NetworkMedia);
const ref = document.getElementById("ref"); 
```

```
<p>Lorem ipsum</p>
<network-media
      hq="https://dummyimage.com/600x400/000/fff&text=fast"
      lofi="https://dummyimage.com/600x400/000/fff&text=slow"
    ></network-media> 
```

## HTM (Hyperscript 标记的标记)

HTM 是由杰森·米勒开发的一个奇妙的微型库，它允许用类似 JSX 的语法创建可重用的模块。

```
<script type="module">
      import {
        html,
        Component,
        render
      } from "https://unpkg.com/htm/preact/standalone.mjs";
      const detectNetwork = () => {
        const { connection = null, onLine = false } = navigator;
        if (connection === null) {
          return "n/a";
        }
        if (!onLine) {
          return "offline";
        }
        const { effectiveType = "4g" } = connection;
        return /\slow-2g|2g|3g/.test(effectiveType) ? "slow" : "fast";
      };
      class Media extends Component {
        render({ hq, lofi }) {
          const status = detectNetwork();
          return html`
            <img src="${status === "fast" ? hq : lofi}" />
          `;
        }
      }

      render(html`<${Media} hq="./hq.jpg" lofi="./lofi.jpg" />`, document.body);
    </script> 
```

## 香草 JavaScript

我们还可以为网络和状态检测创建实用程序助手，并逐步增强交付的用户体验。如果用户离线，我们可以显示警告，根据网络速度获取不同的资源，甚至为低端网络提供不同的捆绑包。

```
const detectNetwork = () => {
  const {
    effectiveType
  } = navigator.connection
  console.log(`Network: ${effectiveType}`)
}

if (navigator.connection) {
  navigator.connection.addEventListener('change', detectNetwork)
}

if (navigator.onLine) {
  window.addEventListener('offline', (e) => {
    console.log('Status: Offline');
  });
  window.addEventListener('online', (e) => {
    console.log('online');
  });
} 
```

## 进一步阅读

*   [使用 JavaScript 和网络信息 API 的自适应服务](https://addyosmani.com/blog/adaptive-serving/)
*   [https://dean Hume . com/dynamic-resources-using-the-network-information-API-and-service-workers/](https://deanhume.com/dynamic-resources-using-the-network-information-api-and-service-workers/)
*   [连接感知组件](https://mxb.at/blog/connection-aware-components/)

你也可以在[vorillaz.com](https://vorillaz.com/adaptive-serving-components-using-the-network-information-api)上找到这个帖子
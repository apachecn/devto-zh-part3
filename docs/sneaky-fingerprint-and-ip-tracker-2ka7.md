# 偷偷摸摸的指纹和 IP 跟踪器与反应

> 原文：<https://dev.to/molamk/sneaky-fingerprint-and-ip-tracker-2ka7>

## 简介

大多数流行的跟踪方法都涉及持久标识符，比如著名的[cookie](https://en.wikipedia.org/wiki/HTTP_cookie)。但是如果我们删除那些饼干呢？如果我们隐姓埋名去*会怎么样？*

[![Hiding](img/e87f6619c06f2834d9660e4bc78c625d.png)](https://i.giphy.com/media/3ohs7KViF6rA4aan5u/giphy.gif)

事实证明，还有其他不依赖持久标识符来跟踪用户的方法。 [**浏览器指纹识别**](https://en.wikipedia.org/wiki/Device_fingerprint) 就是其中之一。因为它不需要存储任何东西，**没有什么可删除的，而且变成*私有*也不会成功**。

我们将编写一个小的 React 应用程序来演示如何收集[设备的指纹](https://en.wikipedia.org/wiki/Device_fingerprint)和 [IP 地址](https://en.wikipedia.org/wiki/IP_address)元数据。该应用程序将完全在浏览器中运行**，并且不会存储任何数据。**

## 动机

> 通过收集这些信息并将其存储在自己的服务器上，网站可以跟踪你的浏览习惯，而无需使用存储在你计算机上的永久标识符，如 cookies。指纹还可以用于在用户删除跟踪 cookie 后为用户重新创建一个跟踪 cookie。知道 cookies 的用户可以在他们的浏览器设置中删除它们，但指纹识别颠覆了内置的浏览器机制，该机制允许用户避免被跟踪。GDPR 和浏览器指纹识别:它如何改变最狡猾的网络追踪者的游戏。

[![Hacker](img/75fdc15b7948de7a89b533dc0df3fc0f.png)](https://i.giphy.com/media/YQitE4YNQNahy/giphy.gif)

## 我们在建造什么

为了构建应用程序，我们将使用:

*   [Fingerprintjs2](https://github.com/Valve/fingerprintjs2) 采集指纹。这将为我们提供设备的平台，内存，核心数量和其他有趣的信息。
*   用于获取 IP 地址元数据的 [Extreme IP Lookup](https://extreme-ip-lookup.com/) API。这提供了诸如设备位置、ISP 等信息...
*   React Hooks 管理应用程序的状态，同时最小化样板文件。因为我们要采用轻量级的方法，所以我们会更喜欢这个选项，而不是 Redux 之类的东西。

## 工作原理

### 获取设备的指纹

`Fingerprintjs2`自动检测正在运行的浏览器的功能，然后有选择地查询可用的参数。它使用的一些识别方法包括:

*   [画布指纹](https://en.wikipedia.org/wiki/Canvas_fingerprinting)。通过绘制文本和添加背景颜色，脚本调用 Canvas API 来获得二进制像素数据的 Base64 编码表示。然后，这个表示被转换成一个散列，作为指纹。
*   [字体检测](https://browserleaks.com/fonts)。基于你所拥有的字体，以及它们是如何绘制的。通过测量填充物的尺寸，我们可以建立一个指纹模型。
*   浏览器插件探测。
*   音频采样。
*   WebGL 指纹识别。

为了让*实际上*获取指纹，我们将使用`Fingerprintjs2`的`get`函数，它看起来像这样:

```
import fp from "fingerprintjs2";

// We re-write the callback into a Promise style,
// so it plays nice with React Hooks
export const getFingerprint = () =>
  new Promise(resolve => {
    fp.get(components => {
      resolve(components);
    });
  }); 
```

### 获取 IP 地址元数据

> 基于 IP 的地理定位是将 IP 地址或 MAC 地址映射到互联网连接的计算或移动设备的真实地理位置。地理定位包括将 IP 地址映射到国家、地区(城市)、纬度/经度、ISP 和域名等有用的信息。[更多关于这里的内容](https://www.iplocation.net/)。

这里不涉及魔法。地理定位的工作原理是简单地使用一个预先填充了相关信息的数据库。也有许多免费(和付费)的服务为你做到这一点，就像我们在本教程中使用的。

我们将对`extreme-ip-lookup` REST API 使用一个标准的 HTTP GET 请求。我们还将指定我们希望将`json`作为返回的响应格式。

```
// Standard HTTP GET using "fetch"
fetch("https://extreme-ip-lookup.com/json")
  .then(res => res.json()) 
```

### 创建组件

现在让我们把所有东西放在一起，创建我们的主要组件。由于我们的数据获取是异步的，我们将使用 [`useEffect`](https://reactjs.org/docs/hooks-effect.html) 钩子来填充我们的组件。

```
import React, { useEffect, useState } from "react";
import { getFingerprint } from "./utils";   // The method we wrote above
import DataTable from "./dataTable";        // Custom component to render our data

function App() {
  const [fingerprint, setFingerprint] = useState(null);
  const [ipData, setIpData] = useState(null);
  const [showReport, setShowReport] = useState(true);

  useEffect(() => {
    if (showReport) {
      fetch("https://extreme-ip-lookup.com/json")           // Get the IP data
        .then(res => res.json())
        .then(ip => Promise.all([ip, getFingerprint()]))    // Get the fingerprint
        .then(([ip, fp]) => {
          setFingerprint(fp);                               // Update the state
          setIpData(ip);
          setShowReport(false);
        });
    }
  }, [showReport]);

  return (
    <div>
      {ipData && fingerprint ? (
        <div>
          <DataTable title="IP Data" data={ipData} />
          <DataTable title="Fingerprint" data={fingerprint} />
        </div>
      ) : (
        <div>
          <h2>Please wait...</h2>
        </div>
      )}
    </div>
  );
}

export default App; 
```

## 备注

应用程序**不会存储任何收集的信息**，但我们可以很容易地改变它，并赋予它存储功能。我们可以:

*   使用 [`localStorage`](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage) API 在浏览器中存储结果。
*   将收集到的数据发送到我们构建的服务器，该服务器会将其存储在数据库中以供将来使用。

## Github 资源库

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [莫拉克](https://github.com/molamk) / [指纹](https://github.com/molamk/fingerprint)

### 偷偷摸摸的指纹和 IP 地址跟踪器

<article class="markdown-body entry-content container-lg" itemprop="text">

# 偷偷摸摸的指纹和 IP 跟踪器与反应

## 介绍

最流行的跟踪方法包括持久标识符，比如著名的[cookie](https://en.wikipedia.org/wiki/HTTP_cookie)。但是如果我们删除那些饼干呢？如果我们隐姓埋名去*会怎么样？*

[![Hiding](img/4e9ad3a14fe8320813f121121544ee23.png)](https://camo.githubusercontent.com/c962e05d75da414d70f1e7f735e78c0729919055/68747470733a2f2f6d656469612e67697068792e636f6d2f6d656469612f336f6873374b5669463672413461616e35752f67697068792e676966)

事实证明，还有其他不依赖持久标识符来跟踪用户的方法。 [**浏览器指纹识别**](https://en.wikipedia.org/wiki/Device_fingerprint) 就是其中之一。因为它不需要存储任何东西，**没有什么可删除的，而且变成*私有*也不会成功**。

我们将编写一个小的 React 应用程序来演示如何收集[设备的指纹](https://en.wikipedia.org/wiki/Device_fingerprint)和 [IP 地址](https://en.wikipedia.org/wiki/IP_address)元数据。该应用程序将完全在浏览器中运行**，并且不会存储任何数据。**

## 动机

> 通过收集这些信息并将其存储在自己的服务器上，网站可以跟踪你的浏览习惯，而无需使用存储在你计算机上的永久标识符，如 cookies。指纹也可以用来重新创建一个跟踪 cookie 为…

</article>

[View on GitHub](https://github.com/molamk/fingerprint)

## 奔跑吧

```
git clone git@github.com:molamk/fingerprint.git
cd fingerprint

yarn install yarn start 
```

## 现场演示链接

[https://fingerprint.molamk.com](https://fingerprint.molamk.com)
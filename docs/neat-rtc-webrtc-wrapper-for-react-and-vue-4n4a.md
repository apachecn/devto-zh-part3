# neat-RTC:React 和 Vue 的 WebRTC 包装器

> 原文：<https://dev.to/tamasszoke/neat-rtc-webrtc-wrapper-for-react-and-vue-4n4a>

**欢迎使用**，这是我最近创建的模块，它使 WebRTC 更易于使用，具有如下特性:

*   内置信号
*   简化媒体流、数据通道的使用
*   准备使用 React、Vue 示例
*   详细描述

[在 NPM 试用套餐](https://www.npmjs.com/package/neat-rtc)
[检查浏览器兼容性](https://caniuse.com/#feat=rtcpeerconnection)

### 什么是 WebRTC？

WebRTC 是一种 HTML5 规范，你可以用它来直接在浏览器或设备之间添加加密的实时媒体/数据通信。

### 过得愉快！

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [塔玛斯索克](https://github.com/tamasszoke) / [奈茨-rtc](https://github.com/tamasszoke/neat-rtc)

### 用于对等(P2P)通信的 WebRTC 包装器，内置 React 和 Vue 信令。

<article class="markdown-body entry-content container-lg" itemprop="text">

# 纯 RTC

[![Build Status](img/76d0adc7fd51419055a938dbbab05fd1.png)](https://travis-ci.org/tamasszoke/neat-rtc)[![npm version](img/4c706c4fdb506fc4ed981aab55a635e4.png)](https://badge.fury.io/js/neat-rtc)[![](img/2442310043088cca7c8d598d3a0c5115.png)](https://camo.githubusercontent.com/2f12f53ad5c3b7f9ed43cca9363725b101ff60e5c9294f6ad0ad78a1eacdf6ca/68747470733a2f2f696d672e736869656c64732e696f2f6769746875622f6c6963656e73652f74616d6173737a6f6b652f6e6561742d7274632e737667)

WebRTC 包装器，内置 React 和 Vue 信号。

[支持项目](https://ko-fi.com/Z8Z7XSML)

## 路标

这些是计划中的项目更新。

*   选择设备选项
*   文件共享示例

## 概观

```
1\. Install the package
2\. Import, configure, create an instance
3\. Wire up signaling on client-side
4\. Setup signaling on server-side
5\. Using mediastream, datachannel 
```

## 安装

```
npm install neat-rtc 
```

## 使用

### 导入

```
import NeatRTC from 'neat-rtc' 
```

### 安装ˌ使成形

```
const config = {
  devMode: true,
  videoIdLocal: 'localVideo',
  videoIdRemote: 'remoteVideo',
  connected: connected,
  mediaStreamConnected: mediaStreamConnected,
  mediaStreamRemoved: mediaStreamRemoved,
  mediaStreamRemoteRemoved: mediaStreamRemoteRemoved,
  datachannels: [
    {
      name: 'text',
      callbacks: {
        open: datachannelOpen,
        message: datachannelMessage,
        error: datachannelError,
        close: datachannelClose
      }
    }
  ]
}; 
```

重要提示:你必须设置好上面所有的回调函数。

**RTCPeerConnection 配置**(可选)

向上面的`config`对象添加以下属性。

`connectionConfig`:设置 STUN/TURN 服务器和其他连接选项，更多信息查看[RTC 配置字典](https://developer.mozilla.org/en-US/docs/Web/API/RTCPeerConnection/RTCPeerConnection#RTCConfiguration_dictionary)。默认值:

```
{
  iceServers: [
    {
```

…</article>

[View on GitHub](https://github.com/tamasszoke/neat-rtc)
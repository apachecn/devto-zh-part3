# 使用 Electron.js 时的常见问题

> 原文：<https://dev.to/akram/common-gotchas-when-working-with-electron-js-4m7n>

# 使用 Electron.js 时的常见问题

所以在过去的几周里，我有机会使用 Electron.js 构建虚拟应用程序，并希望分享我在构建跨平台桌面应用程序时的小经验。

## 建筑

电子应用有两个过程:

*   主进程:每个应用程序总是有且只有一个主进程负责创建网页，把它想象成一个服务器-客户端架构，在这种情况下主进程就是服务器。

*   渲染进程:表示主进程创建的网页(客户端)，运行在 [chromium 多进程架构](https://dev.chromium.org/developers/design-documents/multi-process-architecture)上。

我推荐[官方文档](https://electronjs.org/docs/tutorial/application-architecture)来深入阅读关于架构的内容。

### IPC

IPC(简称 T0)是电子应用程序上使用的一种协议，用于实现其进程(主进程和呈现器)之间的通信
电子通过提供两个模块来实现这一点: [ipcMain](https://electronjs.org/docs/api/ipc-main) 用于主进程，以及 [ipcRenderer](https://electronjs.org/docs/api/ipc-renderer) 用于呈现器进程，这两个模块都基于 [EventEmitter](https://nodejs.org/api/events.html#events_class_eventemitter) ，并提供监听和发出事件的功能。

从主进程 ping 渲染器进程并监听响应的基本示例如下:

```
// in the main process
const { ipcMain } = require('electron')
myWindow.webContents.send('event_from_main', 'ping_from_main')
ipcMain.on('event_from_renderer', (event, arg) => {
  console.log(arg) // ping_from_renderer
    // we can use event.sender.send to reply to event 
})

// in the renderer process
const { ipcRenderer } = require('electron')
ipcRenderer.on('event_from_main', (event, arg) => {
    console.log(arg) // ping_from_main
    ipcRenderer.send('event_from_renderer', 'ping_from_render')
}) 
```

## 数据持久性

Electron.js 作为一个*框架*在如何存储数据的问题上并不固执己见，而是由你和你的需求来选择一个数据库，无论是本地数据库(localStorage，indexedDB)还是像 Postgres 或 MongoDB 这样的大型数据库(一般来说，只要有 Node.js 适配器， 欢迎你使用)
我个人总是发现自己在小型或原型项目中使用 [lowdb)](https://github.com/typicode/lowdb/) ，因为它易于使用(lodash 语法之类的),并且真正给你灵活性，一旦你弄清楚你真正需要的*是什么，之后就可以迁移到更大的项目。*

## 用户界面和样式

与其他桌面应用工具包不同，电子应用程序中的视图/页面只是普通的 html 文件，这意味着你可以带你最喜欢的风格朋友来参加聚会(sass，less，css grid，svgs 等)
还有一些样板文件可以在电子应用程序中使用 React，Vue 和其他 UI 库。

### 奖金:

@sindresorhus 建立了[吨](https://github.com/sindresorhus?utf8=%E2%9C%93&tab=repositories&q=electron&type=source&language=)的实用程序，可以帮助你的电子开发工作流程，我最喜欢的是[电子实用程序](https://github.com/sindresorhus/electron-util)、[运行电子](https://github.com/sindresorhus/run-electron)和[电子上下文菜单](https://github.com/sindresorhus/electron-context-menu)。
同样为了打包和构建你的应用程序，我推荐[电子构建器](https://www.npmjs.com/package/electron-builder)

## 链接

*   [https://electronjs.org/docs](https://electronjs.org/docs)
*   [https://github.com/sindresorhus/awesome-electron](https://github.com/sindresorhus/awesome-electron)
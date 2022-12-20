# 如何保存准确的 npm 软件包版本

> 原文：<https://dev.to/daleljefferson/how-to-save-exact-npm-package-versions-10fm>

我最近开始在我的 package.json 中使用精确的版本。这让我可以对我的版本有更多的控制，并且通过像[renewal](https://renovateapp.com/)这样的工具，这比以往任何时候都容易。

## 什么是确切版本？

```
"react": "^16.0.0" // carat: allow 16.1.0
"react": "~16.0.0" // tilde: allow 16.0.1
"react": "16.0.0" // exact: only 16.0.0 
```

## 为什么

renew 有很好的文档说明你为什么要这么做[依赖固定](https://renovateapp.com/docs/deep-dives/dependency-pinning)

## 如何

有两种方法可以强制 npm 或 yarn 在您的 package.json 中保存精确的版本。

## 1。每次你安装一个软件包

```
// npm
npm install --save --save-exact react

// yarn
yarn add --exact react 
```

## 2。在您的配置中设置默认值

```
npm config set save-exact=true 
```

这会将`save-exact=true`添加到您的`.npmrc`中
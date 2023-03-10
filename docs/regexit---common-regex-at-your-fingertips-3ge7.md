# Regexits-唾手可得的普通正则表达式

> 原文：<https://dev.to/perkinsjr/regexit---common-regex-at-your-fingertips-3ge7>

## 什么是 Regexits？

Regexits 是一个轻量级的 npm 包，它处理一组常用的 regex 模式。

## 你为什么需要它？

我想答案是，当你需要验证 SSN、电话号码甚至 URLSlug 时，你会讨厌谷歌正则表达式。

## 如何使用

好消息是，这部分很简单。

```
npm i regexits 
```

然后在你的代码中

```
const regexits= require('regexits')

regexits(string, pattern) 
```

## 你目前有哪些选择？

*   带斜线的日期
*   24 小时内的时间
*   ISO8601 日期和时间
*   HTML 标签
*   十六进制颜色
*   URLSlug
*   Src Img 标签
*   统一资源定位器
*   正整数
*   负整数
*   整数
*   正数
*   负数
*   数字
*   电话号码
*   换行
*   CSS 注释
*   USA & Canada Zip
*   severely subnormal 智力严重逊常

## 快速举例

```
const regexits = require('regexits')

let isSSN = regexits('012-34-5678', 'SSN')

return isSSN;

#This Returns true 
```

所以总的来说，这是一个非常灵活的工具，涵盖了一系列经常使用的正则表达式。我会不断更新 npm 包以及我的其他开源工具。

想要添加一个新的模式，只需通过 [github](https://github.com/perkinsjr/regexits/issues) 添加一个问题，我会添加它并重新发布 npm 包。

想了解 npm 包的最新情况吗？请在 [npm](https://www.npmjs.com/package/regexits) 查看
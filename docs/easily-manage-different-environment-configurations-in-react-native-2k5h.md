# 在 React Native 中轻松管理不同的环境配置

> 原文：<https://dev.to/spencercarli/easily-manage-different-environment-configurations-in-react-native-2k5h>

> 本教程最初发布于 [React 原生学校](https://www.reactnativeschool.com/easily-manage-different-environment-configurations-in-react-native)。访问 React 原生学校，访问 80 多个 React 原生教程！

我将很快向你们展示一些非常有价值的东西。准备好了吗？

**停止**在您的应用程序中注释和取消注释特定于环境的变量！

我每天都看到这种情况。你会犯错误的。我保证。我做过很多次了。

在之前，我已经讨论过[管理配置，但是这种方式可能有点复杂(它处理本机和 JavaScript)。今天我们将只讨论 JavaScript，这使它变得非常简单。](https://www.reactnativeschool.com/managing-configuration-in-react-native)

首先，在你的 app 里安装 [`react-native-dotenv`](https://github.com/motdotla/react-native-dotenv#readme) 。

```
yarn add react-native-dotenv --dev
# npm install react-native-dotenv --save-dev 
```

接下来在项目的根目录下创建一个`.env`文件。

```
API_URL=https://example.com/api
ANALYTICS_KEY=asdf123 
```

然后你会想要打开你的`babel.config.js`文件，并将`react-native-dotenv`添加到预置中。下面是一个最终文件示例:

```
module.exports = function(api) {
  api.cache(true);
  return {
    presets: ['babel-preset-expo', 'module:react-native-dotenv'],
  };
}; 
```

然后创建一个 config.js 文件，我们将从中导入配置信息，供应用程序的其余部分使用。

```
import { API_URL, ANALYTICS_KEY } from 'react-native-dotenv';

export default {
  API_URL,
  ANALYTICS_KEY,
}; 
```

*等等，为什么要有单独的文件？为什么不直接用`react-native-dotenv`呢？*我一会儿会谈到这一点。

现在，在你的应用程序的任何地方，你都可以很容易地访问这些变量。

```
import config from './config';

console.log(config.API_URL);
console.log(config.ANALYTICS_KEY); 
```

## 不同环境不同变量

假设您有一个开发服务器用于测试，另一个用于生产。你是怎么处理的？将您的`.env`复制到一个`.env.production`文件中。改变需要改变的。`react-native-dotenv`会根据环境自动挑选合适的。简单。

## 不同平台不同变量

这就是为什么我们建立了这个`config.js`文件。假设我们为 iOS 和 Android 提供了不同的`ANALYTICS_KEY`。我们该如何处理？

我不想把我的代码弄得乱七八糟——我想在一个地方做。因此，考虑到这一点，我们这样做。

```
import { Platform } from 'react-native';
import {
  API_URL,
  IOS_ANALYTICS_KEY,
  ANDROID_ANALYTICS_KEY,
} from 'react-native-dotenv';

export default {
  API_URL,
  ANALYTICS_KEY: Platform.select({
    ios: IOS_ANALYTICS_KEY,
    android: ANDROID_ANALYTICS_KEY,
  }),
}; 
```

嘣！现在我们代码中的每个地方都使用了`ANALYTICS_KEY`。很简单。

有时候最简单的改变会产生最大的影响。

> 如果你对 React Native 感兴趣，一定要去参观 React Native 学校。
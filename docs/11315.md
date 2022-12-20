# 如何检测 React 本机应用程序中的崩溃

> 原文：<https://dev.to/spencercarli/how-to-detect-crashes-in-a-react-native-app-1cgl>

[https://www.youtube.com/embed/2jMcmEsWlm0](https://www.youtube.com/embed/2jMcmEsWlm0)

> 最初发布于[www . reactnativeschool . com/how-to-detect-crashes-in-a-react-native-app](www.reactnativeschool.com/how-to-detect-crashes-in-a-react-native-app)。如果你有兴趣了解更多关于 React Native 的知识，请访问该网站，获取 75 个以上的教程！

在这节课中，我们将学习如何处理生产中的错误。我建议检查一下 [`react-native-exception-handler`](https://github.com/master-atul/react-native-exception-handler) 作为开始处理 React Native 所有运行时的捕获错误的一种方式。

如果你准备更进一步，我强烈建议你去看看 [Instabug](https://instabug.com/) 。 [Instabug](https://instabug.com/) 提供了一个平台和工具来捕捉和分析 React 原生应用中的错误，以及其他东西(比如捕捉用户反馈)。

他们免费赞助了一整堂关于调试 React 本地应用的课程，这样每个人都可以访问它。您可以在 React Native School 上访问课程[如何在开发和生产中调试 React 原生应用](https://www.reactnativeschool.com/debugging-in-development-and-production)。

课程中的命令和代码:

末端的

```
yarn add react-native-exception-handler
react-native link react-native-exception-handler 
```

App.js

```
import React from 'react';
import { View, Button } from 'react-native';
import {
  setJSExceptionHandler,
  setNativeExceptionHandler,
} from 'react-native-exception-handler';

const handleError = (error, isFatal) => {
  // fetch
  console.log(error, isFatal);
  alert(error.name);
};

setJSExceptionHandler((error, isFatal) => {
  console.log('caught global error');
  handleError(error, isFatal);
}, true);

setNativeExceptionHandler(errorString => {
  // do the things
});

export default class App extends React.Component {
  state = {
    number: 0,
  };

  makeRequest = () => {
    fetch('asdf')
      .then(res => res.json())
      .then(res => {
        alert(res);
      })
      .catch(error => {
        handleError(error, false);
      });
  };

  badStateChange = () => {
    this.setState(state => ({
      number: state.data.number + 1,
    }));
  };

  render() {
    return (
      <View
        style={{
          flex: 1,
          backgroundColor: '#fff',
          alignItems: 'center',
          justifyContent: 'center',
        }}
      >
        <Button title="Make an invalid request" onPress={this.makeRequest} />
        <Button title="Bad state change" onPress={this.badStateChange} />
      </View>
    );
  }
} 
```
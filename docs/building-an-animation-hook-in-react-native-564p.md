# 在 React Native 中构建动画挂钩

> 原文：<https://dev.to/spencercarli/building-an-animation-hook-in-react-native-564p>

[https://www.youtube.com/embed/tAY3eQoyujM](https://www.youtube.com/embed/tAY3eQoyujM)

**注意:**在记录/写入时，React Native 0.59 处于 RC 状态。因此，要尝试它，您需要指定您的 React 原生版本`react-native init MyProject --version react-native@next`

第二个注意事项:钩子是新的，我对它们也是新的。我可能在视频中说错了很多次——我道歉！我也在学习它们；)

**本文最初发表于 [React 本地学校](https://www.reactnativeschool.com/building-an-animation-hook-in-react-native)。要获得最新的文章&资源，请访问我那里！此外，如果你想成为一名 React 本地开发人员，请查看我们的其他教程！**

## 为什么是 useRef？

我在这段代码中使用`useRef`的原因是因为[来自文档](https://reactjs.org/docs/hooks-reference.html#useref)的这一行:

> 返回的对象将在组件的整个生存期内保持不变。

因为我们需要一个不断更新的动画值，所以我们希望它在组件的整个生命周期中保持不变，而不是在每次组件更新时都重新创建(在这种情况下，每次计数都会增加)。

## 更新#1:避免内存泄漏

请检查下面的代码。为了避免内存泄漏，您需要在组件卸载时清除超时。您可以通过从调用`clearTimeout`的`useEffect`回调中返回一个函数来实现这一点。感谢 [Milli](https://www.linkedin.com/in/milli-jolic/) 指出这一点！

## 附加资源

*   [了解更多关于 React 挂钩的信息](https://reactjs.org/docs/hooks-intro.html)。
*   [使用效果挂钩](https://reactjs.org/docs/hooks-reference.html#useeffect)
*   [useRef hook](https://reactjs.org/docs/hooks-reference.html#useref)

视频的最终代码可以在下面找到:

App.js

```
import React, { useEffect, useRef } from 'react';
import { View, Animated, Text } from 'react-native';

const Box = ({ backgroundColor = '#3cae6f', scale = 1 }) => (
  <Animated.View
    style={[
      {
        width: 100,
        height: 100,
        backgroundColor,
        transform: [{ scale }],
      },
    ]}
  />
);

const usePulse = (startDelay = 500) => {
  const scale = useRef(new Animated.Value(1)).current;

  const pulse = () => {
    Animated.sequence([
      Animated.timing(scale, { toValue: 1.2 }),
      Animated.timing(scale, { toValue: 0.8 }),
    ]).start(() => pulse());
  };

  useEffect(() => {
    const timeout = setTimeout(() => pulse(), startDelay);
    return () => clearTimeout(timeout);
  }, []);

  return scale;
};

const App = ({ count }) => {
  const scale = usePulse();
  const scale2 = usePulse(750);

  return (
    <View
      style={{ flex: 1, alignItems: 'center', justifyContent: 'space-around' }}
    >
      <Box scale={scale2} backgroundColor="#1f9cb8" />
      <Text>{count}</Text>
      <Box scale={scale} />
    </View>
  );
};

// class App extends React.Component {
//   scale = new Animated.Value(1);

//   componentDidMount() {
//     setTimeout(() => this.pulse(), 500);
//   }

//   pulse = () => {
//     Animated.sequence([
//       Animated.timing(this.scale, { toValue: 1.2 }),
//       Animated.timing(this.scale, { toValue: 0.8 }),
//     ]).start(() => this.pulse());
//   };

//   render() {
//     return (
//       <View style={{ flex: 1, alignItems: 'center', justifyContent: 'space-around' }}>
//         <Text>{this.props.count}</Text>
//         <Box scale={this.scale} />
//       </View>
//     );
//   }
// }

export default class Wrapper extends React.Component {
  state = { count: 1 };

  componentDidMount() {
    setInterval(() => {
      this.setState(state => ({
        count: state.count + 1,
      }));
    }, 500);
  }

  render() {
    return <App count={this.state.count} />;
  }
} 
```
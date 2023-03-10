# 用类型脚本反应导航

> 原文：<https://dev.to/andreasbergqvist/react-navigation-with-typescript-29ka>

因此..您希望使用 Typescript 构建 React 本机应用程序，并决定使用 React 导航作为导航库。

您已经设置了所有的类型脚本，并让您的应用程序运行！

你加了 React 导航就万事大吉了！

但是..您添加了一些需要参数的屏幕和一些想要编辑标题的屏幕，等等。没有太多信息可以查找...

以下是我如何使用 React 导航和 Typescript 的一些片段。

### *免责声明*

有多种方法可以实现类型化的 React 导航。这些只是一些例子，事物的命名应该考虑适合你的应用。

任何其他解决方法的意见，不胜感激！

## 首先，类 vs 函数...

我真的很喜欢用钩子来使用函数组件。但是目前如果使用功能组件，热重装存在一个问题。
参见:【https://github.com/facebook/react-native/issues/10991】T2

它就是不工作。可能有一些解决方案，你可以用类来包装你的功能组件，但是希望这个问题能很快得到解决！
[https://mobile . Twitter . com/Dan _ abramov/status/1125846420949434368](https://mobile.twitter.com/dan_abramov/status/1125846420949434368)

我将使用一个基于[https://github.com/react-navigation/hooks](https://github.com/react-navigation/hooks)的导航钩子。
但是由于回购不是很活跃，我就“偷”了这个功能，修改了一点:

```
import { useContext } from 'react';
import {
  NavigationScreenProp,
  NavigationRoute,
  NavigationContext,
} from 'react-navigation';

export function useNavigation<Params>() {
  return useContext(NavigationContext) as NavigationScreenProp<
    NavigationRoute,
    Params
  >;
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，我的例子将同时涉及类和功能组件。

## 更新标题标题并导航

#### 类组件

```
import React, { Component } from 'react';
import { Button, Text, View } from 'react-native';
import {
  NavigationParams,
  NavigationScreenProp,
  NavigationState,
} from 'react-navigation';

interface Props {
  navigation: NavigationScreenProp<NavigationState, NavigationParams>;
}

class TestScreen extends Component<Props> {
  public static navigationOptions = {
    title: 'Test Screen',
  };

  render() {
    const { navigation } = this.props;
    return (
      <View>
        <Text>Test Screen</Text>
        <Button
          title="Button"
          onPress={() => {
            navigation.navigate('anotherTestScreen');
          }}
        />
      </View>
    );
  }
}

export default TestScreen; 
```

Enter fullscreen mode Exit fullscreen mode

请注意，只有直接在导航上设置的屏幕才具有导航属性。如果你想让一个子组件访问导航，你可以这样做:

```
import React, { Component } from 'react';
import { Button, Text, View } from 'react-native';
import { NavigationInjectedProps, withNavigation } from 'react-navigation';

class TestComponent extends Component<NavigationInjectedProps> {
  render() {
    const { navigation } = this.props;
    return (
      <Button
        title="Button"
        onPress={() => {
          navigation.navigate('anotherTestScreen');
        }}
      />
    );
  }
}

export default withNavigation(TestComponent); 
```

Enter fullscreen mode Exit fullscreen mode

#### 功能组件

```
import React from 'react';
import { Button, Text, View } from 'react-native';
import { useNavigation } from '../hooks/useNavigation';

const AnotherTestScreen = () => {
  const navigation = useNavigation();
  return (
    <View>
      <Text>Test Screen</Text>
      <Button
        title="Button"
        onPress={() => {
          navigation.navigate('paramScreen', { text: 'Hi!' });
        }}
      />
    </View>
  );
};

AnotherTestScreen.navigationOptions = {
  title: 'Another Test Screen',
};

export default AnotherTestScreen; 
```

Enter fullscreen mode Exit fullscreen mode

## 为屏幕输入参数

#### 类组件

```
import React, { Component } from 'react';
import { Button, Text, View } from 'react-native';
import { NavigationScreenProp, NavigationState } from 'react-navigation';

interface NavigationParams {
  text: string;
}

type Navigation = NavigationScreenProp<NavigationState, NavigationParams>;

interface Props {
  navigation: Navigation;
}

class ParamScreen extends Component<Props> {
  public static navigationOptions = ({
    navigation,
  }: {
    navigation: Navigation;
  }) => ({
    title: navigation.state.params ? navigation.state.params.text : '',
  });

  render() {
    const { navigation } = this.props;
    const {
      state: { params },
    } = navigation;
    return (
      <View>
        <Text>Param: {params ? params.text : ''}</Text>
        <Button
          title="Button"
          onPress={() => {
            navigation.navigate('anotherParamScreen', { text: 'Hello!' });
          }}
        />
      </View>
    );
  }
}

export default ParamScreen; 
```

Enter fullscreen mode Exit fullscreen mode

你为什么要为那些你可能会问自己的额外的打字代码而烦恼呢？为什么不直接用`any`？
这个例子可能不是最好的，但是参数现在已经输入，您可以在编辑器中获得智能感知帮助:

[![](img/77c95d66799efa12c3938364a58fc2a2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YePrKbru--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0vvzgo3go4pfghdt16k3.png)

#### 功能组件

```
import React from 'react';
import { Button, Text, View } from 'react-native';
import {
  NavigationScreenProp,
  NavigationState,
  StackActions,
  NavigationActions,
} from 'react-navigation';
import { useNavigation } from '../hooks/useNavigation';

interface NavigationParams {
  text: string;
}

type Navigation = NavigationScreenProp<NavigationState, NavigationParams>;

const AnotherParamScreen = () => {
  const navigation = useNavigation<NavigationParams>();
  const {
    state: { params },
  } = navigation;
  return (
    <View>
      <Text>Param: {params ? params.text : ''}</Text>
      <Button
        title="Button"
        onPress={() => {
          const resetAction = StackActions.reset({
            index: 0,
            actions: [NavigationActions.navigate({ routeName: 'testScreen' })],
          });
          navigation.dispatch(resetAction);
        }}
      />
    </View>
  );
};

AnotherParamScreen.navigationOptions = ({
  navigation,
}: {
  navigation: Navigation;
}) => ({
  title: navigation.state.params ? navigation.state.params.text : '',
});

export default AnotherParamScreen; 
```

Enter fullscreen mode Exit fullscreen mode
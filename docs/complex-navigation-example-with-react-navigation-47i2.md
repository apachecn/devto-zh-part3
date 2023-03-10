# 带有反应导航的复杂导航示例

> 原文：<https://dev.to/spencercarli/complex-navigation-example-with-react-navigation-47i2>

我发现人们可能会害怕在 React 导航中组合不同的导航器来完成更“复杂”的导航模式。今天，我想简要地向大家介绍一个更复杂的导航设置。它将包括:

*   切换导航器，用于表示我们已验证和未验证的应用程序状态
*   堆栈导航器，用于在许多地方进行正常的从右到左导航(身份验证屏幕，每个标签的堆栈)
*   用于自下而上导航的堆栈导航器
*   选项卡导航器
*   抽屉导航器

> 本帖原载于 [React 原生学校](https://www.reactnativeschool.com/)。如果您有兴趣了解更多关于 React Native 学校的信息，请访问我们！

## 先决条件

在我们开始之前，我将添加一个`Exampe.js`文件作为我们所有路线的屏幕(毕竟这只是一个演示)。该组件生成一个随机的背景颜色，并将显示当前屏幕上所有可用的路线。

示例. js

```
import React from 'react';
import { View, TouchableOpacity, Text } from 'react-native';

const getAvailableRoutes = navigation => {
  let availableRoutes = [];
  if (!navigation) return availableRoutes;

  const parent = navigation.dangerouslyGetParent();
  if (parent) {
    if (parent.router && parent.router.childRouters) {
      // Grab all the routes the parent defines and add it the list
      availableRoutes = [
        ...availableRoutes,
        ...Object.keys(parent.router.childRouters),
      ];
    }

    // Recursively work up the tree until there are none left
    availableRoutes = [...availableRoutes, ...getAvailableRoutes(parent)];
  }

  // De-dupe the list and then remove the current route from the list
  return [...new Set(availableRoutes)].filter(
    route => route !== navigation.state.routeName
  );
};

const getRandomColor = () => {
  var letters = '0123456789ABCDEF';
  var color = '#';
  for (var i = 0; i < 6; i++) {
    color += letters[Math.floor(Math.random() * 16)];
  }
  return color;
};

const Example = ({ navigation }) => {
  return (
    <View
      style={{
        flex: 1,
        alignItems: 'center',
        justifyContent: 'center',
        backgroundColor: getRandomColor(),
      }}
    >
      {getAvailableRoutes(navigation).map(route => (
        <TouchableOpacity
          onPress={() => navigation.navigate(route)}
          key={route}
          style={{
            backgroundColor: '#fff',
            padding: 10,
            margin: 10,
          }}
        >
          <Text>{route}</Text>
        </TouchableOpacity>
      ))}
    </View>
  );
};

export default Example; 
```

做完这些，让我们开始吧。

## 切换导航器

为了完成用户旅程的不同“状态”之间的切换，我们将使用切换导航器，这样用户就不能返回。显然，我们将有一个主应用程序旅程的屏幕。我们还将为未认证的用户提供一个。

此外，我喜欢添加各种类型的`Loading`屏幕。通常这不会显示任何东西——它只是用来确定用户是否通过了身份验证，并把他们路由到正确的位置。

[![](img/cb6f2f955a89975efc835fccadf5faff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3EOe7wPX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/01.gif)

索引. js

```
import React from 'react';
import {
  createAppContainer,
  createBottomTabNavigator,
  createDrawerNavigator,
  createStackNavigator,
  createSwitchNavigator,
} from 'react-navigation';

import Example from './screens/Example';

const App = createSwitchNavigator({
  Loading: {
    screen: Example,
  },
  Auth: {
    screen: Example,
  },
  App: {
    screen: Example,
  },
});

export default createAppContainer(App); 
```

## 授权栈导航器

如果用户未通过身份验证，我们将为他们设置一个堆栈导航器，让他们从登录屏幕、登录、创建帐户、忘记密码或重置密码。当您需要进行身份验证时看到的典型选项。

[![](img/b62f61193e9c6733f2610aa1aa59bdad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CkbDIkHw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/02.gif)

索引. js

```
// ...

const AuthStack = createStackNavigator({
  Landing: {
    screen: Example,
    navigationOptions: {
      headerTitle: 'Landing',
    },
  },
  SignIn: {
    screen: Example,
    navigationOptions: {
      headerTitle: 'Sign In',
    },
  },
  CreateAccount: {
    screen: Example,
    navigationOptions: {
      headerTitle: 'Create Account',
    },
  },
  ForgotPassword: {
    screen: Example,
    navigationOptions: {
      headerTitle: 'Forgot Password',
    },
  },
  ResetPassword: {
    screen: Example,
    navigationOptions: {
      headerTitle: 'Reset Password',
    },
  },
});

const App = createSwitchNavigator({
  Loading: {
    screen: Example,
  },
  Auth: {
    screen: AuthStack,
  },
  App: {
    screen: Example,
  },
});

export default createAppContainer(App); 
```

## App 标签页

一旦用户进入应用程序，我们将使用选项卡让他们能够访问我们应用程序的主要功能-提要、搜索和发现页面。然后，我们将用创建选项卡的结果替换`App`导航器中的`App`项。

创建任何导航器的输出只是一个组件，所以我们可以在 React 导航中无限地嵌套它们。

[![](img/9b342d3cc539970a2e126dde006c3d5a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AdCCKqfX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/03.gif)

索引. js

```
// ...

const MainTabs = createBottomTabNavigator({
  Feed: {
    screen: Example,
    navigationOptions: {
      tabBarLabel: 'Feed',
    },
  },
  Search: {
    screen: Example,
    navigationOptions: {
      tabBarLabel: 'Search',
    },
  },
  Discover: {
    screen: Example,
    navigationOptions: {
      tabBarLabel: 'Discover',
    },
  },
});

const App = createSwitchNavigator({
  Loading: {
    screen: Example,
  },
  Auth: {
    screen: AuthStack,
  },
  App: {
    screen: MainTabs,
  },
});

// ... 
```

## 每个应用标签的堆栈导航器

就像我们将`MainTabs`嵌套在`App`导航器中一样，我们将允许应用程序中的每个选项卡都有自己的堆栈导航器。这样做意味着每个选项卡都有自己的状态，因此用户可以转到一个选项卡的详细信息屏幕，切换到另一个选项卡，并在返回时保持每个选项卡的相同状态。

此外，通过这个示例，您可以看到导航器将获取最接近的匹配路径名。这意味着我们可以重复使用屏幕名称，每个堆栈将抓取其堆栈中或导航器层次结构中最接近的可用`Details`屏幕。

[![](img/bbfb6aeda284d6b7f0a5f8d7a9bc5ee6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---JyY69lS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/04.gif)

索引. js

```
const FeedStack = createStackNavigator({
  Feed: {
    screen: Example,
    navigationOptions: {
      headerTitle: 'Feed',
    },
  },
  Details: {
    screen: Example,
    navigationOptions: {
      headerTitle: 'Details',
    },
  },
});

const SearchStack = createStackNavigator({
  Search: {
    screen: Example,
    navigationOptions: {
      headerTitle: 'Search',
    },
  },
  Details: {
    screen: Example,
    navigationOptions: {
      headerTitle: 'Details',
    },
  },
});

const DiscoverStack = createStackNavigator({
  Discover: {
    screen: Example,
    navigationOptions: {
      headerTitle: 'Discover',
    },
  },
  Details: {
    screen: Example,
    navigationOptions: {
      headerTitle: 'Details',
    },
  },
});

const MainTabs = createBottomTabNavigator({
  Feed: {
    screen: FeedStack,
    navigationOptions: {
      tabBarLabel: 'Feed',
    },
  },
  Search: {
    screen: SearchStack,
    navigationOptions: {
      tabBarLabel: 'Search',
    },
  },
  Discover: {
    screen: DiscoverStack,
    navigationOptions: {
      tabBarLabel: 'Discover',
    },
  },
}); 
```

## App 抽屉

抽屉也是如此。我们将创建导航器(我们还创建了一个设置堆栈来为抽屉提供一个理由)，并将其呈现为一个屏幕。

这一次我们将用`MainDrawer`替换呈现`MainTabs`，并在抽屉中呈现我们的选项卡。构建这种层次结构意味着我们只是添加了更多的导航器，但是已经存在的一切都将继续工作。

[![](img/5328f32b7a6f5b6eeb75029558868e76.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rNBOpoap--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/05.gif)

索引. js

```
// ...

const SettingsStack = createStackNavigator({
  SettingsList: {
    screen: Example,
    navigationOptions: {
      headerTitle: 'Settings List',
    },
  },
  Profile: {
    screen: Example,
    navigationOptions: {
      headerTitle: 'Profile',
    },
  },
});

const MainDrawer = createDrawerNavigator({
  MainTabs: MainTabs,
  Settings: SettingsStack,
});

const App = createSwitchNavigator({
  Loading: {
    screen: Example,
  },
  Auth: {
    screen: AuthStack,
  },
  App: {
    screen: MainDrawer,
  },
});

// ... 
```

## 模态风格堆栈导航器

最后，我们希望添加一个从下到上移动并覆盖任何其他屏幕的导航器。这意味着它需要在堆栈的最根位置。如果它在根节点，那么它可以从它的任何一个子节点中渲染出来。

[![](img/11137633baad17363af20a839d578308.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rjr5uoHc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/06.gif)

索引. js

```
// ...

const AppModalStack = createStackNavigator(
  {
    App: MainDrawer,
    Promotion1: {
      screen: Example,
    },
  },
  {
    mode: 'modal',
    headerMode: 'none',
  }
);

const App = createSwitchNavigator({
  Loading: {
    screen: Example,
  },
  Auth: {
    screen: AuthStack,
  },
  App: {
    screen: AppModalStack,
  },
});

export default createAppContainer(App); 
```

## 最终导航员代码

我们的最终代码。

索引. js

```
import React from 'react';
import {
  createAppContainer,
  createBottomTabNavigator,
  createDrawerNavigator,
  createStackNavigator,
  createSwitchNavigator,
} from 'react-navigation';

import Example from './screens/Example';

const AuthStack = createStackNavigator({
  Landing: {
    screen: Example,
    navigationOptions: {
      headerTitle: 'Landing',
    },
  },
  SignIn: {
    screen: Example,
    navigationOptions: {
      headerTitle: 'Sign In',
    },
  },
  CreateAccount: {
    screen: Example,
    navigationOptions: {
      headerTitle: 'Create Account',
    },
  },
  ForgotPassword: {
    screen: Example,
    navigationOptions: {
      headerTitle: 'Forgot Password',
    },
  },
  ResetPassword: {
    screen: Example,
    navigationOptions: {
      headerTitle: 'Reset Password',
    },
  },
});

const FeedStack = createStackNavigator({
  Feed: {
    screen: Example,
    navigationOptions: {
      headerTitle: 'Feed',
    },
  },
  Details: {
    screen: Example,
    navigationOptions: {
      headerTitle: 'Details',
    },
  },
});

const SearchStack = createStackNavigator({
  Search: {
    screen: Example,
    navigationOptions: {
      headerTitle: 'Search',
    },
  },
  Details: {
    screen: Example,
    navigationOptions: {
      headerTitle: 'Details',
    },
  },
});

const DiscoverStack = createStackNavigator({
  Discover: {
    screen: Example,
    navigationOptions: {
      headerTitle: 'Discover',
    },
  },
  Details: {
    screen: Example,
    navigationOptions: {
      headerTitle: 'Details',
    },
  },
});

const MainTabs = createBottomTabNavigator({
  Feed: {
    screen: FeedStack,
    navigationOptions: {
      tabBarLabel: 'Feed',
    },
  },
  Search: {
    screen: SearchStack,
    navigationOptions: {
      tabBarLabel: 'Search',
    },
  },
  Discover: {
    screen: DiscoverStack,
    navigationOptions: {
      tabBarLabel: 'Discover',
    },
  },
});

const SettingsStack = createStackNavigator({
  SettingsList: {
    screen: Example,
    navigationOptions: {
      headerTitle: 'Settings List',
    },
  },
  Profile: {
    screen: Example,
    navigationOptions: {
      headerTitle: 'Profile',
    },
  },
});

const MainDrawer = createDrawerNavigator({
  MainTabs: MainTabs,
  Settings: SettingsStack,
});

const AppModalStack = createStackNavigator(
  {
    App: MainDrawer,
    Promotion1: {
      screen: Example,
    },
  },
  {
    mode: 'modal',
    headerMode: 'none',
  }
);

const App = createSwitchNavigator({
  Loading: {
    screen: Example,
  },
  Auth: {
    screen: AuthStack,
  },
  App: {
    screen: AppModalStack,
  },
});

export default createAppContainer(App); 
```

你可以在点心上找到一个[的工作实例。](https://snack.expo.io/@spencercarli/complex-react-navigation-example)
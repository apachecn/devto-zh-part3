# 使用 Redux 和 React 导航对您的 Expo 应用程序进行主题化

> 原文：<https://dev.to/ghost/theme-your-expo-app-with-redux-and-react-navigation-3p61>

最近在开发一个 React 本地应用程序(带 Expo)时，我用 React 导航库构建了一个简单的选项卡导航器。我想应用程序的主题，以便标题会改变颜色取决于你在哪个页面。例如，在主页面上它会是红色的，而在次页面上，当你改变标签时，标题会变成蓝色。

我最终用 Redux 做到了。在本文中，我将向您展示如何使用 Redux 和 React 导航对 Expo 应用程序进行主题化。

*P.S* 这篇文章是用英国英语写的，然而，代码是用美国英语写的，以使它更一致(颜色对颜色)。

#### 反应导航

是一个帮助您简化应用导航的库。使用这个库的主要原因是因为它完全是用 JavaScript 编写的，所以不需要本机代码(Swift/Android)就可以运行。还有，它是世博会的[推荐导航库](https://docs.expo.io/versions/latest/guides/routing-and-navigation)。

#### Redux

Redux 用于管理 React 应用的全局状态，它实际上可以与 React 和 React Native 一起使用。当然，在这个例子中，我们将使用 Redux 和 React 本地应用程序。

[![](img/5b965958b3ce43f18c46fc773fdbd180.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tM-VgMiB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2A-5TV7URjutGKbjbeu-tiPg.png) 

<figcaption>Redux Store，来自[【https://css-tricks.com/learning-react-redux】](https://css-tricks.com/learning-react-redux)/</figcaption>

上图很好地解释了为什么你可能需要使用 Redux。在更复杂的 React 本机应用程序中传递状态可能会很棘手，因为有许多组件和子组件。使用 redux，我们可以改变一个组件的状态，Redux 将更新所有其他组件的状态。

关于 Redux 有很多很棒的教程，我特别喜欢这个 [one](https://www.youtube.com/watch?v=KcC8KZ_Ga2M) 。以下是 Redux 将如何与这个应用程序一起工作的简要总结。

*   Redux 将初始(状态)颜色设置为红色
*   将选项卡从主页更改为次级页面
*   这将一个动作分派给 Redux 存储(该动作称为 toggleTheme)
*   然后，商店会调用一个减压器
*   缩减器(也称为 toggleTheme)将旧状态更新为新状态，它将颜色从红色变为蓝色
*   Redux 更新所有组件中的状态

这里有一张 GIF 图，可能有助于弄清楚它是如何工作。

[![Redux Flow, from http://slides.com/jenyaterpil/redux-from-twitter-hype-to-production#](img/5f1d186bbe6ac9474666b516d117ffb7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--r_OOHF7Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AMtR6XLdEyZF-0-7X) 

<figcaption>Redux 流量，来自[http://slides . com/jenyaterpil/Redux-from-Twitter-hype-to-production #](http://slides.com/jenyaterpil/redux-from-twitter-hype-to-production#)</figcaption>

#### 先决条件

*   Expo/React 本地应用
*   用于测试的 Android 模拟器/设备
*   安装以下依赖项

```
"react-navigation": "^2.18.0",
"react-redux": "^5.1.1",
"redux": "^4.0.1", 
```

#### 解

现在到了本文有趣的部分，让我们看看代码。

#### 结构

项目结构将如下所示

```
├── src
│ ├── actions
│ ├── components
│ ├── containers
│ ├── reducers
│ ├── screens
│ ├── store
| └── themes
├── package.json
├── App.js 
```

#### 主题

[![](img/10e5be7f3c06e89d47ede6e8056d8374.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BjhyWVoM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/528/1%2AFxf8Da_QXHpwMbVWLQoRxQ.png) 

<figcaption>主题/index.js</figcaption>

在这里，我们定义了两种将被用作主题的颜色，红色和蓝色。我给每种颜色取了一个名字，因为这使得切换逻辑更容易理解。

#### 减速器

[![](img/d74b0787284de1c8e1fe64fecc422288.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IMsU1Ga3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/568/1%2AzWLmD-KERRnJxFnx842XNA.png) 

<figcaption>减速器/Theme.js</figcaption>

缩减器是一个纯粹的函数，它接受某个状态并返回一个新状态。在这个例子中，它传递当前的主题颜色，并把它换成新的主题颜色。

在这个例子中，如果动作是 TOGGLE_THEME，我们从有效载荷中获取颜色名称，并使用 switch 语句交换颜色。因此，如果当前颜色是红色，我们将状态(colorData)更新为蓝色。

[![](img/9cf3f023baadcef42d65dd3d0048684e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KEU-SOgm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/476/1%2AOtWZtS43CUVQI1u9WqGfaA.png)T3】reducers/index . js

这里我们组合了所有的减速器，在这个例子中，我们只使用了一个减速器，但是如果我们有多个减速器，组合减速器功能将需要将它们组合在一起。在这个例子中，我们可以在需要的时候简单地在函数中添加新的缩减器。

#### 动作

[![](img/3d9edb4287f99585df7f949e17d565ec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--w7FBX0Zm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/508/1%2Aj6yKSOpCkwtP9rSHDsj7Cg.png) 

<figcaption>动作/动作类型. js</figcaption>

这个文件定义了我们可以发送到我们商店的所有动作。在这个例子中，我们只需要一个动作来切换我们的主题。

[![](img/90cb741110745ad53459269e8239f909.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iXlCdvaH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/528/1%2AL8mg3PYJFkNWJ0uRfkJQZQ.png) 

<figcaption>动作/index.js</figcaption>

在这个文件中，我们定义了我们的动作，所以这里我们有一个单独的动作 toggleTheme，它将一个主题作为输入，并将其作为我们的有效载荷传递，因此在我们的 reducer 中使用 action.payload.name 来访问颜色的名称。

#### 商店

[![](img/c08e4757658aa3f9e4f373dec6c66856.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Dc35bxTA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/558/1%2AiPrFTnkPVpJEO5VZ-JZVhA.png) 

<figcaption>商店/index.js</figcaption>

Redux 存储用于存储我们的应用程序的当前状态，我们必须将我们的存储与我们的 reducer 链接起来。我们可以使用 createStore 函数来完成此操作，并从 reducers/index.js 导入 reducer。

#### App.js

[T1](https://res.cloudinary.com/practicaldev/image/fetch/s--KNDKkQ9c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/773/1%2A289XAR5oxLjw-xejBU6nsg.png)T3】app . js

这充当我们的应用程序的主文件，要在我们的应用程序中使用 Redux，我们必须包装 Provider 标记，并将 store props 设置为 store/index.js 文件。包含两个屏幕和选项卡导航器的逻辑。

#### 组件

[![](img/ad4e383eaa25ca593bbd3d1df9fb651c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IprefMNm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/732/1%2ADS8nZV5MIeeqC6-hCMC-zA.png) 

<figcaption>组件/CustomTabNavigator.js</figcaption>

这里是我们使用 react 导航创建选项卡导航器的地方。我们定义了两个屏幕 A 和 B，每个屏幕都有不同的标签颜色，红色代表 A，蓝色代表 B

```
tabBarOnPress: ({ defaultHandler }) => {
 store.dispatch(toggleTheme(COLORS.red));
 defaultHandler();
}, 
```

在选项卡改变时(从 A -> B)，我们检测到选项卡按下，并将 toggleTheme 动作分派给 Redux 存储。因此，当我们从 A -> B 改变标签时，商店中的颜色将从红色->蓝色改变，反之亦然。

另一件要注意的事情是标签颜色是使用下面的函数设置的。颜色通过 tab 键选项传递。

```
const commonTabOptions = color => ({
 activeTintColor: 'white',
 pressColor: '#fff',
 inactiveTintColor: '#ddd',
 style: {
 backgroundColor: color,
 },
});

tabBarOptions: commonTabOptions(COLORS.red.hexCode) 
```

[![](img/6bf0ab904cc1fbf7220a5b1aeed870af.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--a8mxllC---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/814/1%2A4IVlvFV68MYHxgnjsXQwzg.png)T3】组件/ToggleTheme.js

这个文件完成了这个应用程序的大部分工作，这是大部分逻辑所在的地方。首先，我们有一个按钮，它在按下时调用 toggleTheme，并将当前颜色(状态)作为参数传递。

```
<Button
 title="Toggle Color"
 color={this.props.color.hexCode}
 onPress={() => this.props.toggleTheme(this.props.color)}
/> 
```

按钮的颜色也使用当前(状态)颜色，所以在这种情况下，它将从红色开始，this.props.color。我们可以使用 this.props.
访问当前状态

```
const mapStateToProps = state => ({
 color: state.Theme.colorData,
}); 
```

该函数用于将 Redux 存储映射到该组件的状态。我们绘制州地图。Theme.colorData，其中 Theme 是我们的 reducer 的名称，colorData 是项目名称。在这个例子中，我们将当前的 colorData 映射到这个组件中的 color props。

```
const mapDispatchToProps = dispatch => ({
 toggleTheme: color => dispatch(toggleTheme(color)),
}); 
```

这个函数将一个函数映射到一个动作(并调度它)，在这个例子中，当在组件中调用 toggleTheme 函数时，它将把 toggleTheme 动作调度到 redux。因此，当按钮被按下时，它将调度一个动作来切换主题。

#### PageA

[![](img/179c365788048c2c370421c68083c8e6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Hl-CeGOS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/600/1%2A9zeTitnYwaevyPrT3sBRoQ.png) 

<figcaption>屏幕/PageA.js</figcaption>

这是我们的一个屏幕的例子，它非常简单，只有页面名称和组件。

#### 举例

[![](img/8e376bb7dc8eca4bd3ccac472679adb7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--n4fcmW23--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/406/1%2AzL4nT7aYvzY36WCSf_Hd0A.gif) 

<figcaption>运行在 Android 模拟器上的应用程序。</figcaption>

这是一个应用程序运行的例子，你可以看到有两种方法可以切换主题。我们可以使用按钮点击，或改变标签。

*额外任务*:如果你想练习一下，目前它不会检测到改变标签页的滑动动作，看看你能怎么做。可以在[这里](https://gitlab.com/hmajid2301/stegappasaurus/blob/development/src/screens/Decoding.js)找到它工作的例子。

#### 附录

*   [示例源代码](https://github.com/hmajid2301/medium/tree/master/10.%20Redux%20with%20React%20Navigation)
*   [反应导航](https://reactnavigation.org/)
*   [Redux 动画](https://css-tricks.com/learning-react-redux/)
*   [还原图](http://slides.com/jenyaterpil/redux-from-twitter-hype-to-production#)
*   [还原](https://redux.js.org/)
*   用 [screentogif](https://www.screentogif.com/) 创建的 gif
*   [用碳制作的代码图像](https://carbon.now.sh)
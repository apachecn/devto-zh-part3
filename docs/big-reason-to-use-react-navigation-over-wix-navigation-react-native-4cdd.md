# 使用 React 导航而不是 Wix 导航的重要原因(React Native)

> 原文：<https://dev.to/karsens/big-reason-to-use-react-navigation-over-wix-navigation-react-native-4cdd>

[![header](img/cc645a44a135da67acf4ddf6355b4329.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--g0qceQXq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w872x980resi7ddbqwdq.png)

今天我发现了 React 导航从根本上优于 Wix 导航的一个重要原因。React 导航有 createbottomtab navigator/createStackNavigator 等功能。它返回一个组件。然而，Wix navigation 只有 registerComponent，它接受您想要呈现的屏幕(如果您需要，可以用包装器包裹它),并且只返回一个 void，并在本机创建这个屏幕。后者的问题是，如果你在你的屏幕周围使用一个包装器，这个包装器将被安装在每个注册的屏幕上。对于我们的应用程序，这意味着我们的 LocalAuthWrapper 在应用程序启动时也装载了 5 次，因为我们的应用程序有 5 个选项卡，它装载了所有选项卡。因此，使用本地授权进行授权的对话框安装了 5 次，这是 4 次太多了。由于这个库的工作方式，它在第一次之后就再也不能工作了，因此我们必须对这个库进行本地修改。

我认为每次加载新的屏幕时，我们的包装器都会被挂载，这有点难看...效率也较低。有了 react 导航，你就没有这个问题，因为你可以用你的包装器包装整个导航，因为导航也是一个组件。

这种根本的区别不应该被忽视！它可能非常重要。

原刊[此处](https://karsens.com/big-reason-to-use-react-navigation-over-wix-navigation/)
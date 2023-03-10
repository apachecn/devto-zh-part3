# 使用带别名的 react-native 更快地构建 Android

> 原文：<https://dev.to/enieber/build-faster-for-android-using-react-native-with-alias-5h39>

[![alt text](img/4a95990e0fa9efd82c72e81d36454b46.png "Tasks manager")](https://res.cloudinary.com/practicaldev/image/fetch/s--KxbaaSjH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1364/1%2AsSx4CEuBbKLS4j1E-qrljQ.jpeg)

当我开始使用 React Native 开发时，我在 android 缓存方面遇到了一些问题，包括库的缓存和项目本身生成的缓存。为了进行简单的构建，我必须删除文件夹，并在 StackOverflow 中检查一些答案，以处理缓存错误。

随着时间的推移，我会遇到一些错误，为了避免这些错误，我每天都会使用一些脚本来帮助我。为了帮助使用 react-native 的其他项目，我创建了一个带有别名的项目，你可以在 Github 上找到它。

## React 原生 Android 别名

再解释一下别名，我用的最多的是:

*   rn-clear:在这个别名中，我删除了整个 android build 文件夹，所以我有一个没有缓存的项目。

*   rn-start:我使用这个命令在开发模式下启动应用程序。它还为一些设备编译应用程序。

*   ad-clear:这个命令的目的是清除整个项目，甚至删除本机 libs 缓存。

*   rn-server:这个别名用于启动节点 js 服务器，负责将 js 代码发送到当前正在开发的 app。

## 结论

虽然这个项目看起来不是很健壮，但是我用这些别名节省了很多时间，因为有了它们，我可以在开发模式下为项目编译:

```
 rn-clear && rn-start 
```

或者甚至没有任何问题地进行生产编译，我只需要确保项目被正确地配置，并且已经设置了键和版本。之后，我只需运行这个命令并享受:

```
 ad-clean && rn-clear && rn-release 
```

如果你喜欢并希望[为项目](https://github.com/enieber/alias/)做贡献，只需发送一个 Pull 请求或在 Github 中打开一个问题。

感谢奥古斯托·奇米莱斯基帮助翻译
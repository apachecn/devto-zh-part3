# GitHub 问题-桌面应用

> 原文：<https://dev.to/mariorodeghiero/github-issue---desktop-app-28eh>

用 [ElectronJS](http://electron.atom.io/) 和 [ReactJS](https://facebook.github.io/react/) 创建的桌面应用程序可以跨平台管理和跟踪 GitHub 问题。

## 截图

[![Git Comannds](img/33f58e912dfd37156f6a2b9e72254f3b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uwDkv9G0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://user-images.githubusercontent.com/24671133/49445258-adfecc00-f7b8-11e8-81a0-63240ff2ddff.gif)

## 建有

*   [反应堆](https://reactjs.org) -反应堆
*   [电子系统](https://electronjs.org) -电子系统
*   电子反应样板文件

## 先决条件

*   **要求节点版本> = 7，npm 版本> = 4。**
*   **如果您对本项目有安装或编译问题，请参见[调试指南](https://github.com/electron-react-boilerplate/electron-react-boilerplate/issues/400)**

## 安装

首先，通过 git 克隆回购:

```
git clone --depth=1 https://github.com/mariorodeghiero/git-issue-react-electronjs.git your-project-name 
```

然后用 yarn 安装依赖项。

```
$ cd your-project-name
$ yarn 
```

**注意**:如果不能使用[纱](https://github.com/yarnpkg/yarn)，运行`npm install`。

## 运行

在`dev`环境中启动应用:

```
$ yarn dev 
```

或者

```
$ npm run dev 
```

## 包装

您可以通过简单地设置 DEBUG_PROD env 变量:
来使用 devtools 调试您的产品构建

```
$ DEBUG_PROD=true yarn package 
```

为本地平台打包应用:

```
$ npm run package 
```

要为所有平台打包应用程序:

首先，参考[多平台构建](https://www.electron.build/multi-platform-build)了解依赖关系。

然后，

```
$ npm run package-all 
```

使用选项打包应用程序:

```
$ npm run package -- --[option] 
```

若要提高此应用的可用性，您可以创建键盘快捷键来快速启动。

*   对于 Mac_OS，请使用“Automator”。
*   用于 Windows 访问应用程序的“属性”。

如果你喜欢这个项目，贡献改进或给我一个 GitHub 上的星⭐️。

谢谢！
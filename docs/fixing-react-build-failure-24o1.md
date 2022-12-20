# 修复 React 构建失败

> 原文：<https://dev.to/haamida/fixing-react-build-failure-24o1>

我非常热衷于部署我的第一个 react 项目，一个非常初级的版本。在不得不一遍又一遍地进行“npm 安装”和“npm 构建”之后，所有这些都消失了，我也没能构建出一个简单的单页项目。几个小时的搜索，没有任何有用或有帮助的东西。

原来，我在一些教程之后添加到项目中的额外的 Babel 依赖项产生了某种冲突，导致每次我尝试 build 命令时 react 脚本都失败。
这可以在这份[https://github.com/facebook/create-react-app/issues/5135](https://github.com/facebook/create-react-app/issues/5135)问题报告中找到踪迹。
现在，终于，在改变了整个应用程序的想法并保留了相同的怪异名称(Sneaky-React)后，我不得不部署我过于天真的尝试。(【https://nifty-hypatia-73966f.netlify.com/】T4)
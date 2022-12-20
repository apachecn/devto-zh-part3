# 我在 Vue 做了一个游戏。动画/力学用 GreenSock，高分用 local feed。

> 原文：<https://dev.to/codestuff2/i-made-a-game-in-vue-animation-mechanics-with-greensock-high-scores-with-localforage-37h2>

## 我的一个同事问我是否可以用 Vue 制作一个简单的游戏。接受挑战！

这个“弹跳游戏”本质上只是一个通过 GreenSock 实现的碰撞检测机制。
[https://greensock.com](https://greensock.com)

更具体地说，我使用 TimelineLite 来完成大部分动画，然后使用 Draggable 实用程序来实现用于在整个游戏中触发得分的碰撞检测。

高分通过 local feed 保存，这是一个使用 web 技术以“半持久”方式保存数据的伟大库。
[https://localforage.github.io/localForage/](https://localforage.github.io/localForage/)

诚然，游戏驻留在单个组件中，我可能会对它进行一点重构，使其更具性能和模块化，但我只是想与大家分享这个小小的“周末项目:)

试试看，让我知道你的最高分是多少！

#### 

<center>看看 codesandbox:</center>

[![Edit bounce-game](img/0b3f0135583496627e3621355d8e9248.png)](https://codesandbox.io/s/zlxk5lwqkx?autoresize=1&fontsize=14&hidenavigation=1&view=preview)

#### 

<center>或</center>

叉 github 回购

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[【adamwhitlock 1】](https://github.com/adamwhitlock1)/[移动游戏视图](https://github.com/adamwhitlock1/bounce-game-vue)

### 一个简单的 vue 游戏。Chris 想用 jquery 做一个这样的游戏，所以我用 vue 做了一个。使用 Vue、本地饲料和绿草

<article class="markdown-body entry-content container-lg" itemprop="text">

# 弹跳游戏

## 项目设置

```
yarn install 
```

### 为开发进行编译和热重新加载

```
yarn run serve 
```

### 为生产进行编译和精简

```
yarn run build 
```

### 运行您的测试

```
yarn run test 
```

### 链接并修复文件

```
yarn run lint 
```

</article>

[View on GitHub](https://github.com/adamwhitlock1/bounce-game-vue)

[https://github.com/adamwhitlock1/bounce-game-vue](https://github.com/adamwhitlock1/bounce-game-vue)

谢谢你来看。
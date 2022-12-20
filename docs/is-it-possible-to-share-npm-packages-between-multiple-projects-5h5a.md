# 可以在多个项目之间共享 npm 包吗

> 原文：<https://dev.to/ermal/is-it-possible-to-share-npm-packages-between-multiple-projects-5h5a>

我正在玩 react，试图创建许多探索不同 react 功能的迷你应用程序。创建一个新的 react 项目并反复安装相同的包似乎太多了。

有没有办法在不同的项目中重用相同的节点模块？这就是我想我想要的

```
/node_modules
/project1
  - package.json - where I run npm start from
/project2
  - package.json - where I run npm start from
/project3
  - package.json - where I run npm start from 
```

根据我在谷歌上的研究，我尝试过在每个`package.json`都安装这个，但是它不工作

```
 "scripts": {
    "dev": "NODE_PATH=$HOME/to/my/parent/folder/node_modules/",
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject"
  }, 
```

有办法做到吗？
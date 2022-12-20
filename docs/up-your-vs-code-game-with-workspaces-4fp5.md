# 使用工作区提升您的 VS 代码游戏

> 原文：<https://dev.to/lukethacoder/up-your-vs-code-game-with-workspaces-4fp5>

> 安妮·斯普拉特的照片

### 什么是工作区

VS Code 的工作区允许用户在项目级别定制 UI 和 IDE 的功能。工作区设置覆盖`User Settings`并有助于与其他开发人员共享项目设置，并允许项目特定的功能和定制。

使用快捷键`Ctrl+,`打开工作区设置。您可以使用该视图上的 UI 编辑设置，或者点击右上角的`{ }`编辑`JSON`文件。

### 我用工作区做什么(atm)

*   调整`titleBar`颜色以区分多个项目。
*   `search.exclude`从搜索结果中隐藏`dist` / `plugins` / `vendor`文件夹。
*   可导出的配置允许基于每个项目与同事共享。
*   一个项目中的多个文件夹路径

```
 "folders":  [  {  "path":  "C:\\laragon\\www\\backend_server"  },  {  "path":  "C:\\Github\\front_end_ui"  }  ], 
```

Enter fullscreen mode Exit fullscreen mode

### 我的工作区启动设置

这是当我把一个新项目作为基础时我复制进去的- [要点](https://gist.github.com/lukethacoder/eecfc34c9c2bc63e87947d55d5b8b786)

```
{  "folders":  [  {  "path":  "C:\\Github\\PROJECT_NAME"  }  ],  "settings":  {  "workbench.colorCustomizations":  {  "titleBar.activeBackground":  "#141414",  "titleBar.activeForeground":  "#FFC87F",  "titleBar.inactiveBackground":  "#696969",  "titleBar.inactiveForeground":  "#FFC87F"  },  "search.exclude":  {  ".cache":  true,  ".vscode":  true,  "node_modules":  true  },  "window.title":  "${rootName}",  "editor.cursorSmoothCaretAnimation":  true  }  } 
```

Enter fullscreen mode Exit fullscreen mode

### 那么你用什么呢？

其他开发人员应该在他们的工作区中使用哪些方便的设置？

### 参考文献

[用户和工作区设置](!https://code.visualstudio.com/docs/getstarted/settings)
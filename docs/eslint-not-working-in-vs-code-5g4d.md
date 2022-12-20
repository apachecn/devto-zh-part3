# ESLint 在 VS 代码中不起作用？

> 原文：<https://dev.to/studio_m_song/eslint-not-working-in-vs-code-5g4d>

*(查看[我的博客](https://raffaelepizzari.com/) )*

嗨，拉斐尔，

这是你以前的笔记。

我知道 ESLint 在 VS 代码中不工作。您已经安装了所有插件，可能重新安装了 VSCode，检查了您的 VSCode 设置并仔细检查了您的。eslintrc 几次，也许你正坐在角落里考虑辞职。

我知道你的感受，你已经去过那里，但你不会死在这座山上。

只需打开 VSCode 设置(。/vscode/settings.json)并添加此:

```
"eslint.validate":[  {  "language":"vue",  "autoFix":true  },  {  "language":"html",  "autoFix":true  },  {  "language":"javascript",  "autoFix":true  },  {  "language":"typescript",  "autoFix":true  }  ] 
```

Enter fullscreen mode Exit fullscreen mode

重启 VSCode，尽情享受吧。

你真诚的，你。
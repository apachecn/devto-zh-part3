# 在重命名重构中禁用 VScode TypeScript 别名

> 原文：<https://dev.to/jaga/disable-vscode-typescript-alias-in-rename-refactoring-27kh>

自 TypeScript 3.4 起，tsserver 中实现了一个设置，使*重构>重命名*在本地范围内创建一个别名，而不是更改原始名称

[![rename](img/38257f2aee25bb31b84aef7a6f9eed46.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3fPjfQpc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://jagascript.com/rename-27218f90fe8175b126c9b51345d0de35.gif)

这个讨厌的东西在 VScode 上是默认启用的，经过几个月的痛苦，我找到了禁用它的方法。

🎉🎉🎉🎉🎉🎉🎉

该配置称为`typescript.preferences.renameShorthandProperties`

[![rename-alias-ts-vscode-setting.png](img/f56b651597a8e99d9448caaa114f52fe.png)](///static/266dd9c7562743db1e99b13bef49a94a/8cbec/rename-alias-ts-vscode-setting.png)
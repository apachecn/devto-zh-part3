# 工作区和汇总

> 原文：<https://dev.to/kthjm/workspaces-and-rollup-1ofk>

如何通过汇总解析位于工作区中的模块？

情况是:`"workspaces"`中设置了包含模块的
`workspace-a``workspace-b`。

```
// package.json
"private": true,
"workspaces": [
  "workspace-a/*", // workspace-a/[module]/package.json
  "workspace-b/*"
], 
```

为了解析模块，通过`customResolveOptions.moduleDirectory`将目录传递给`rollup-plugin-node-resolve`。

```
// rollup.config.js
import Resolve from 'rollup-plugin-node-resolve'

const resolve = Resolve({
  customResolveOptions: {
    moduleDirectory: [ // as array
      'workspace-a',
      'workspace-b',
    ]
  }
}) 
```

并且[module]的 dirname 必须等于它在 package.json 中的`"name"`。

* * *

可能有更聪明的办法，这只是一个解决办法。😗

*   [rollup](https://rollupjs.org/guide/en)
*   [汇总-插件-节点-解析](https://github.com/rollup/rollup-plugin-node-resolve)
*   [工作区](https://yarnpkg.com/lang/en/docs/workspaces/)
# 我的 Visual Studio 代码现在有点重了

> 原文：<https://dev.to/0xkoji/my-visual-studio-code-is-a-little-bit-heavy-now-587>

我真的很爱`Visual Studio Code`。然而，最近它越来越重 orz....

我只是修改了设置来解决我的问题。

### `Default`

```
 "files.watcherExclude": {
    "**/.git/objects/**": true,
    "**/.git/subtree-cache/**": true,
    "**/node_modules/**": true
   } 
```

Enter fullscreen mode Exit fullscreen mode

### `Add some lines`

```
"files.watcherExclude": {
        "**/.git/objects/**": true,
        "**/.git/subtree-cache/**": true,
        "**/node_modules/**": true,
        "**/Library/**":true,
        "**/GoogleDrive/**":true,
        "**/Dropbox/**":true,
        "**/.cocoapods/**":true,
        "**/Pictures/**":true,
} 
```

Enter fullscreen mode Exit fullscreen mode
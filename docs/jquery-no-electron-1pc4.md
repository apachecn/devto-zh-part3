# JQuery 无电子

> 原文：<https://dev.to/tiagodanin/jquery-no-electron-1pc4>

许多人试图在电子中使用 JQuery，但最终得到一个错误，即找不到变量`$`。

灵魂！灵魂

在 index.html 中，添加“
”

```
<script>
    window.$ = require("./jquery-3.min.js")
</script> 
```

Enter fullscreen mode Exit fullscreen mode

已完成:)
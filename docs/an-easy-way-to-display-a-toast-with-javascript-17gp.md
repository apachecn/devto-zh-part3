# 用 JavaScript 显示祝酒词的简单方法

> 原文：<https://dev.to/riversun/an-easy-way-to-display-a-toast-with-javascript-17gp>

```
<!DOCTYPE html>
<html>
<body>
<script src="https://riversun.github.io/jsframe/jsframe.js"></script>
<script>
    const jsFrame = new JSFrame();
    jsFrame.showToast({
        html: 'This is a simple toast', align: 'top', duration: 2000
    });
</script>
</body>
</html> 
```

[![toast](img/ef9b27e31d02b3d36eff31cb7b09d305.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--b34p7gFw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://riversun.github.io/jsframe/capture/toastd.gif)

下面是一个简单的演示。

而且你也可以像[这样定制这个](https://riversun.github.io/jsframe/examples/v150/toast.html)。

更多详情，请查看 github 上的[库](https://github.com/riversun/JSFrame.js)。
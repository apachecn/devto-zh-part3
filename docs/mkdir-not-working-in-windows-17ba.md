# mkdir 在 windows 中不工作

> 原文：<https://dev.to/vjnvisakh/mkdir-not-working-in-windows-17ba>

我一直很难用 mkdir()让它在使用 CakePHP 的 windows 中运行。

**这在 windows**
中无法正常工作

```
mkdir($this -> webroot . "uploads/abc"); 
```

Enter fullscreen mode Exit fullscreen mode

**用这个代替**

```
mkdir(getCwd() . "/uploads/abc"); 
```

Enter fullscreen mode Exit fullscreen mode

在这上面浪费了太多时间。希望别人不会。

回去工作！
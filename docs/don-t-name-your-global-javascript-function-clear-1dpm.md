# 不要把你的全局 JavaScript 函数命名为“clear”

> 原文：<https://dev.to/nikola/don-t-name-your-global-javascript-function-clear-1dpm>

[![](img/1153a019ba545ba38a421966808a26bf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eq_sw9fm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/b396ac8c3pp0r7ph40rk.jpg)

*最初发布于[我的博客](http://www.nikola-breznjak.com/blog/javascript/dont-name-global-javascript-function-clear/)T3】*

## TL；速度三角形定位法(dead reckoning)

同题，不要命名你的全局 JavaScript 函数`clear`。

## TL；速度三角形定位法(dead reckoning)

我艰难地学会了不使用`clear`作为我的全局 JavaScript 函数的名称。

给定下面的例子:

```
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    Test 1

    <script>
        function clear() {
            document.getElementById('result').innerHTML = '';
        }
    </script>
</head>

<body>
    <button id="clear" onclick="clear()">Clear</button>
    <div id="result">Testing</div>
</body>

</html> 
```

Enter fullscreen mode Exit fullscreen mode

点击`Clear`按钮，`Testing`文本不会被删除。

我发现这是一个非常酷的 [StackOverflow 解释](https://stackoverflow.com/questions/7165570/is-clear-a-reserved-word-in-javascript)为什么会这样。其要点是，这样调用 [document.clear](https://developer.mozilla.org/en-US/docs/Web/API/Document/clear) 函数，简单地将其重命名为类似于`clearResult`的名称就可以了。
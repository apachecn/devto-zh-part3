# 语法错误:保留字

> 原文：<https://dev.to/lambdatest/syntaxerror-reserved-word-24i1>

还记得当你只是一个 JavaScript 初学者的时候，在学习的时候你一定遇到过“保留字”这个术语。这些是你不能用作变量名称的词。除了像 break，var，if…else 等主流关键词。还有更多的保留关键词。

```
SyntaxError: "x" is a reserved identifier  (Firefox)
SyntaxError: Unexpected reserved word (Chrome) 
```

Enter fullscreen mode Exit fullscreen mode

如果你不小心使用了这样的词语，下面是你将会收到的错误信息。经验丰富的 JS 开发人员在编写脚本时也会犯这样的错误。

***你知道吗 [URL 地址](https://www.lambdatest.com/free-online-tools/url-encode?utm_source=devto&utm_medium=organic&utm_campaign=jul07_sd&utm_term=sd&utm_content=free_tools)只有使用 ASCII 字符才能复制到互联网上。这些地址包含 ASCII 集以外的字符，因此必须首先将其转换为 ASCII 格式。URL 编码用百分号(%)后跟两个十六进制数字来替换不安全的 ASCII 字符。***

### 严格模式

由于严格模式的诸多好处，它已经成为一种必然。它检查并简化了代码流，错误调试也变得非常容易。我不会说它有缺点，但你需要格外小心，而实现这一点。

除了已经存在的保留关键字之外，严格模式还保留了几个关键字，如 *implements、interface、let、package、private、protected、public、as、yield* 和 *static* 。

```
<!DOCTYPE html>
<html>
<body>

<h2>With "use strict":</h2>
<h3>Using a reserved word as variable name, is not allowed.</h3>

<p>Activate debugging in your browser (F12) to see the error report.</p>

<script>
"use strict";
var public = 'Thanos';  // This will cause an error (public is reserved in strict mode).
</script>

</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

***看看这个: [Base64 编码](https://www.lambdatest.com/free-online-tools/base64-encode?utm_source=devto&utm_medium=organic&utm_campaign=jul07_sd&utm_term=sd&utm_content=free_tools)——一种将二进制数据转换成文本形式的方法，这样可以更容易地通过电子邮件和 HTML 格式数据传输。***

[![Unexpected syntax error](img/98f8e1a1500a4b5b71dc95cb62788882.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mR2xTH5w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2018/04/error-report-2.png)

现在我们将变量名改为 avengerEnemy，现在它可以工作了。

```
<!DOCTYPE html>
<html>
<body>

<h2>With "use strict":</h2>
<h3>No reserved word used</h3>

<p>Activate debugging in your browser (F12) to see the avengerEnemy.</p>

<script>
"use strict";
var avengerEnemy = 'Thanos';  //  avengerEnemy is not a reserved word in strict mode.
console.log(avengerEnemy);
</script>

</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

[![error debugging, reserved keywords](img/bb07d3a67ea395cedd4405efc2a25af3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ecDl8gJb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2018/04/thanos-showing.png)

***你知道吗 [URL 解析](https://www.lambdatest.com/free-online-tools/url-parse?utm_source=devto&utm_medium=organic&utm_campaign=jul07_sd&utm_term=sd&utm_content=free_tools)在线允许将查询字符串拆分成人类可读的格式，并负责解码参数。***

如果您仍然得到相同的错误，这可能是由于过时的浏览器版本。尝试更新浏览器，因为旧版本有旧的保留字需要修改。

快乐脚本！

[![](img/5ca15e434455da9426a2aad75efd7d47.png)](https://goo.gl/4umtgi)
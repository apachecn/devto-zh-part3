# (未知):JavaScript 中的脚本错误

> 原文：<https://dev.to/lambdatest/unknown-script-error-in-javascript-58p7>

如果有人试图违反你制定的规则，你会怎么回应？给他发送脚本错误。

但在现实生活中没有。😉

但在 JavaScript 中，如果你试图违反同源策略，浏览器会给你发送‘脚本错误’作为回报。

[![](img/13fef4529fccdd9f57ab9fd96f857fb5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--u50wRDRO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2018/03/Unknown-Script-Error-in-JavaScript.png)

### 当 JavaScript 代码中出现脚本错误时？

当异常违反浏览器的同源策略以响应 onerror 回调时，浏览器会以“脚本错误”作为响应。

同源策略:根据同源策略，浏览器只接受位于两个不同网页上的相同服务器上的脚本。

每个浏览器都有一组可接受的或者说是必需的“请求头”。当您点击服务器上的任何请求时，它应该包含那些请求头的要素。否则你将面临一个脚本错误。

[![](img/c682517f511b858f5640120c6e8c9589.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--v0TaQLZF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2018/03/Script-error.png)

嗯，这是浏览器为了防止脚本泄露到外部域而故意为之的行为。因为没有人愿意接受不必要的请求😉你知道吗？

**我正面临 JavaScript 脚本错误，我该怎么办？**
如果你也面临同样的问题，那么就用这些可能的解决方案吧。

**1。设置跨源 HTTP 头**
`Access-Control-Allow-Origin: *`

通过将`Access-Control-Allow-Origin:`设置为' * '，可以确保您可以从任何域访问资源。如有必要，您还可以用特定的域名替换“*”，您希望该域名可以访问您的域的脚本。

在不同的环境下，有不同的方法将 his 设置为*。

*   Apache:创建一个。存放 JS 文件的文件夹中的 htaccess 文件:`Header add Access-Control-Allow-Origin "*"`
*   Ngnix:添加 add_header 指令为:

    ```
    location ~ ^/assets/ {
    add_header Access-Control-Allow-Origin *;
    }

    ```

*   HA 代理:添加该资产: `rspadd Access-Control-Allow-Origin:\ *`

**2。设置 crossorigin="anonymous"**
对于你 HTML 脚本中的每一个脚本，你已经设置了 Access-Control-Allow-Origin，设置
`crossorigin="anonymous"`

```
 <script src="http://another-domain.com/app.js" crossorigin="anonymous"></script> 

```

这段代码告诉您的浏览器匿名获取目标文件，避免在浏览器请求时传输任何用户标识信息，如 HTTP 凭据或 cookies。

如果您在 JavaScript 代码中遇到任何脚本错误，希望本文能有所帮助。

[原文发表于此](https://www.lambdatest.com/blog/script-error-in-javascript/)。

相关文章:

1.  [引用错误:JavaScript](https://www.lambdatest.com/blog/reference-error-javascript/)
2.  [常见的 JavaScript 错误及其处理方法](https://www.lambdatest.com/blog/common-javascript-errors-and-how-to-handle-them/)
3.  [使用浏览器的开发者控制台调试 JavaScript】](https://www.lambdatest.com/blog/debugging-javascript-using-the-browsers-developer-console/)
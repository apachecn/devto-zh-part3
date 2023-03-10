# 邮递员提示-在会话中自动保存身份验证令牌

> 原文：<https://dev.to/rf_schubert/postman-tips---autosave-auth-token-on-the-session-4bi4>

作为一名开发人员，您每次都必须测试您的 API 端点，如果它有一点改进，您需要调用一个登录端点来让 auth 令牌工作。

假设您有来自`/api/login`端点
的响应

```
{  "token":  "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3N1ZWQiOiIyMDE5LTAzLTA0VDE2OjQ2OjAzLjMyMDU1NC0wMzowMCIsImV4cGlyZXMiOiIyMDE5LTAzLTA0VDE2OjQ3OjAzLjMyMDYxOS0wMzowMCIsInNjb3BlcyI6ZmFsc2V9.cbPmdag39wvttxLHHe2BfOHUjM6vHDJ52WvoSxqLLF4g"  } 
```

Enter fullscreen mode Exit fullscreen mode

通常，您需要在会话中获得响应、复制/粘贴，以便能够在 Postman 上的每个端点上使用。

但是您可以做一些最好的事情，您可以配置您的邮递员在您刚刚登录时在您的会话上保存令牌！

点击你的邮递员上的`Tests`标签:
[![Postman request tabs](img/9fb380081d4cf6da5918afc49e1dc617.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zgRByYnh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jot5qasx9k02p7wq51q8.png)

把这个配置:

```
var jsonData = JSON.parse(responseBody);
pm.environment.set("token", jsonData.token); 
```

Enter fullscreen mode Exit fullscreen mode

Tada！！！这将在当前环境中保存请求的响应，变量名称为`token`，现在您可以在您的路线上使用，只需像这样配置它:

[![Postman request headers](img/32d36290b104a580aa29c2c888107dfd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6wEf5s0s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bf6bcfzpzu3fampauukt.png)

现在，您可以随时登录，Postman 会自动将令牌保存在您的环境中，所有端点都可以轻松使用它。

尽情享受吧！
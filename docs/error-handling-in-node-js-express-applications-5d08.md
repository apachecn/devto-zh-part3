# Node.js Express 应用程序中的错误处理

> 原文：<https://dev.to/strahinjalak/error-handling-in-node-js-express-applications-5d08>

各位开发者好。我想我们可以分享一些关于 Express 应用程序中错误处理的想法。

目标:让人们分享各种想法可以帮助每个阅读/讨论的人做出他们自己的错误处理或加强当前的错误处理。

需求:人们应该处理在一个或多个服务中抛出错误的情况，包括他们自己的和第三方库使用的服务。

我的想法是:

```
1\. Creation of StatusError such as
https://thepracticaldev.s3.amazonaws.com/i/u6rxsufyjqvplr3364da.png

2\. Throwing this error in services that are part of our business domain with         
    meaningful statuses, such as 400, 404, 401 etc.

3\. Throwing this error in services that handle consuming 3rd party API with 
    such status that has semantic meaning, even if it may be slightly different 
    from the one in API. One could also add additional info comprised of data 
    successfully computed before such error. 

    e.g. Throwing 404 while trying to find 3rd party resource connected to some 
         in our base even though third party API may throw different error but 
         with such response that corresponds 404\. One could also add name of 
         resource from our base in message to help further solving the issue.

4\. These errors could be caught in route handlers and sent further down the 
   pipe to error handler.

5\. Error handler could call the appropriate error handling module which can do 
    multiple, separated actions, such as logging, special handling for some of 
    the errors, etc. 
```

我非常渴望听到你的想法。
# Node.js 中的跟踪请求？

> 原文：<https://dev.to/henryjw/tracing-requests-in-nodejs-2inh>

TLDR；

*   如何维护请求上下文，而不必传递日志记录器(或 requestId)并且不使用`cls-hooked`？
*   如果没有比传递记录器更好的方法，我如何携带记录器的类型信息(最好没有 JSDocs)？

任何一个使用不同的模式，比传递伐木工干净？我尝试过使用 [express-http-context](https://www.npmjs.com/package/express-http-context) (和其他基于`cls-hooked`的库)，但是它不是很可靠，因为在某些情况下上下文会丢失。
我想到的唯一一个[稍微好一点的方法](#improved-solution)是将所有模块都做成类，这样只有构造函数需要记录器，但是记录器仍然要从调用者那里传递下来。

传递日志记录器的另一个问题是输入信息丢失了，所以我必须记住对象的结构。这对我来说没什么大不了的，但是它会导致一些微妙的错误，尤其是对于那些不像我一样熟悉代码库的开发人员来说。

## 当前解

```
// routes.js

app.get(
    '/api/v1/customer/:id',
    async (req, res, next) => {
        const id = req.params.id
        // The logger contains multiple fields request-specific fields like
        // request ID and authorization level
        const logger = req.logger
        const customer = await customerService.get(id, logger)

        /// ... process and return response
    }
) 
```

Enter fullscreen mode Exit fullscreen mode

```
// customer-service.js

module.exports.get = async (id, logger) {
    /// ...
}

module.exports.create = async (customer, logger) {
    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

## 改良方案

```
// routes.js

app.get(
    '/api/v1/customer/:id',
    async (req, res, next) => {
        const id = req.params.id
        // The logger contains multiple fields request-specific fields like
        // request ID and authorization level
        const logger = req.logger
        const customerService = new CustomerService(logger)
        const customer = await customerService.get(id)

        /// ... process and return response
    }
)

app.post(
    '/api/v1/customer',
    async (req, res, next) => {
        const customer = req.body
        const logger = req.logger
        // Downside: still have to pass logger to constructors
        const customerService = new CustomerService(logger)
        const customer = await customerService.create(customer)

        // ... process and return response
    }
) 
```

Enter fullscreen mode Exit fullscreen mode

```
// customer-service.js

class CustomerService {
    constructor(logger) {
        // logger is only passed into the constructor
        this.logger = logger
    }

    module.exports.get = async (id) {
        // ...
        this.logger.info(/*data to log*/)
        // ...
    }

    module.exports.create = async (customer, logger) {
        // ...
        this.logger.info(/*data to log*/)
        // ...
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

非常感谢任何解决方案或提示:)
# 教程:Node.js' Express 中的国际 API - i18n +验证

> 原文：<https://dev.to/israelmuca/tutorial-international-api---i18n--validation-in-nodejs-express-4pn1>

*本文最初发表于[Israel muca . dev](https://israelmuca.dev/blog/how-to-international-api-i18n-validation-in-node-js-for-your-api)T3】*

最近，我在做一个项目，这个项目有一个 [i18n](https://en.wikipedia.org/wiki/Internationalization_and_localization) 需求。我需要 API 来验证输入的用户数据，并根据这些数据，以用户提供的语言返回特定的成功或错误消息。

关于实际的翻译，我想轻松地为后端提供两种语言的消息(首先是西班牙语和英语)，并且我想最终能够支持更多的语言，如果需要的话能够雇佣一名翻译，并让他在不需要开发人员帮助的情况下“随时”修改它们。

所以我开始研究如何满足这些需求，在这个过程中我遇到了一些小问题，因此，我认为用我提出的(并实现的)解决方案创建一个教程是不错的。

## 咱们码！

本教程使用 ES6、Node.js 和 Express，创建一个响应调用的服务器。
我已经包含了一个基本测试的工作解决方案，你可以在这个[库](https://github.com/israelmuca/express-i18n-api)中查看，或者和我一起一步一步地完成代码！

### 库

我们将使用一些经过实战检验的库来加速我们的开发:

*   **express** ，创建/管理服务器
*   **express-locale** ，获取用户的语言环境
*   **body-parser** ，获取用户的输入
*   **express-validator** ，用于验证用户的输入
*   Airbnb 的 node-polyglot ，帮助我们管理语言
*   **object.fromentries** ，将数组转换成对象

由于我们将使用 ES6，我们也将需要巴贝尔！

*   **@babel/cli**
*   **@巴别/核心**
*   **@babel/preset-env**

因此，让我们进入控制台并创建项目

```
mkdir i18n-validation
cd i18n-validation
npm init 
```

对于这个用例，我们将保留 npm 给我们的所有缺省值，除了我更改为 **server.js** 的缺省条目

现在，让我们安装我们的主要依赖项

```
npm i express express-locale body-parser express-validator node-polyglot object.fromentries 
```

现在，让我们安装我们的开发依赖项

```
npm i @babel/cli @babel/core @babel/preset-env --save-dev 
```

现在，我们需要做的就是添加另一个文件:

```
touch .babelrc 
```

在里面，我们会写:

```
{
    "presets": [
        "@babel/preset-env"
    ]
} 
```

如果你要对你的项目进行源代码控制，不要忘记添加一个带有`node_modules`的`.gitignore`，以避免提交它们。

请记住，我们将使用 ES6，我们需要做一些额外的步骤才能做到这一点，所以让我们继续并更改我们在`package.json`中的脚本:

```
{
...
  "main": "server.js",
  "scripts": {
    "clean": "rm -rf dist && mkdir dist",
    "transpile": "babel -d ./dist ./src",
    "build": "npm run clean && npm run transpile",
    "start": "npm run build && node ./dist/server.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
...
} 
```

> 我们使用了几个脚本，并把其中一些用菊花链连接起来。
> 
> *   `clean`创建`dist`文件夹来保存 ES5 传输的代码。
> *   将代码从 ES6 转换到 ES5。
> *   `build`跑`clean`然后，`transpile`。
> *   `start`运行`build`之后，带有新 ES5 节点的`server.js`文件传输到`dist`文件夹。
> *   `test`是空的，不会在教程中涉及，但是[库](https://github.com/israelmuca/express-i18n-api/)包括[基本测试](https://github.com/israelmuca/express-i18n-api/blob/master/src/tests/i18n.tests.js)。

最后，让我们创建`src`文件夹，并在其中创建`server.js`文件:

```
mkdir src
cd src
touch server.js 
```

现在让我们通过修改`server.js`让 express 运行起来

```
// Import dependencies
// =============================================================
import express from 'express'

// Setup the express router
// =============================================================
const router = express()

// Set the port to be used
const port = process.env.PORT || 8080

// Start the server!
// =============================================================
router.listen(port, () => {
    console.log(`App running on port ${port}`)
}) 
```

现在，我们可以运行:

```
npm start 
```

如果一切正常，控制台应该会告诉我们正在端口 8080 上运行。
这样，我们就有了一个什么也不做的服务器……*！*

现在，我们需要**实际上**让它运行起来。

所以我们需要添加更多的依赖项:

```
// Import dependencies
// =============================================================
import express from 'express'
import createLocaleMiddleware from 'express-locale'
import bodyParser from 'body-parser' 
```

我们需要在服务器上设置它们

```
// Setup the express router
// =============================================================
const router = express()

// Set the port to be used
const port = process.env.PORT || 8080

// Add data parsing to express
router.use(bodyParser.urlencoded({ extended: true }))
router.use(bodyParser.json())

// Get the user's locale, and set a default in case there's none
router.use(createLocaleMiddleware({
    "priority": ["accept-language", "default"],
    "default": "en_US"
})) 
```

有了这些改变，现在我们检查用户的地区并解析他们发送的数据。但是，我们需要加上`polyglot`来表示。

为此，我们将首先创建我们的。翻译所在的 js 文件

```
mkdir i18n
cd i18n
touch i18n.js
cd .. 
```

让我们打开这个新文件，其中有两个常量，一个显示可用语言的数组

```
export const availableLangs = ['es', 'en'] 
```

和一个包含实际翻译的对象

```
export const messages = {
    en: {
        // Error messages
        'emailRequiredField': "'email' is a required field.",
        'emailIsEmail': "This is not a valid email address.",
        'passwordRequiredField': "'password' is a required field.",

        // Success messages
        'loginSuccessful': "You've successfully logged in.",
        'emailSent': "Your password recovery email was sent."
    },
    es: {
        // Mensajes de error
        'emailRequiredField': "'email' es un campo requerido.",
        'emailIsEmail': "Este no es un email válido.",
        'passwordRequiredField': "'password' es un campo requerido.",

        // Mensajes de éxito
        'loginSuccessful': "Has iniciado sesión exitosamente.",
        'emailSent': "Tu correo de recuperación de contraseña ha sido enviado."
    }
} 
```

准备好消息后，我们将继续为 express 创建一个中间件，它将导入多语言和这些翻译，以将它们包含在实际的 express 请求中。

```
mkdir utilities
cd utilities
touch startPolyglot.js
cd .. 
```

打开这个新文件，我们将导入多语言和翻译

```
import Polyglot from 'node-polyglot'
import { messages } from '../i18n/i18n' 
```

我们将创建一个函数，它将作为一个快速的“中间件”用于每个请求。它将获得用户的地区(我们在`server.js`中获得的)，创建一个 Polyglot 实例，并根据用户的语言加载适当的消息

```
exports.startPolyglot = (req, res, next) => {
    // Get the locale from express-locale
    const locale = req.locale.language

    // Start Polyglot and add it to the req
    req.polyglot = new Polyglot()

    // Decide which phrases for polyglot
    if (locale == 'es') {
        req.polyglot.extend(messages.es)
    } else {
        req.polyglot.extend(messages.en)
    }

    next()
} 
```

如果您还记得的话，我们的`server.js`使用`createLocaleMiddleware`来设置当前的区域设置，它位于`req.locale.language`上。

因此，我们得到这个值，对于我们的用例，检查它是西班牙语的 *es* 还是英语的 *en* (如果两者都不是，我们的默认设置)，并加载该语言的正确消息，这些消息通过 polyglot 的*扩展*函数添加到 Express' 'req '对象中。

### 添加多种语言来表达

现在，我们需要添加这个中间件来在`server.js`上表达，通过导入它，并在之后添加它**我们创建了*语言环境中间件*，因为多语言使用它。**

```
import { startPolyglot } from './utilities/startPolyglot' 
```

> 在最上面加上这一行，和其他的导入。

```
// Start polyglot and set the language in the req with the phrases to be used
router.use(startPolyglot) 
```

> 在调用`createLocaleMiddleware`函数后添加这一行。

现在，我们的服务器已经准备好用西班牙语或英语发送错误或成功消息，但是，这些消息从哪里发出呢？

### 路线

因此，Express 需要知道如何处理不同路线上不同类型的呼叫。为此，我们将首先创建一个 routes 文件夹和文件，开始监听服务器中的呼叫。

```
mkdir routes
cd routes
touch auth.routes.js
cd .. 
```

让我们打开该文件并添加以下代码:

```
// Routes =============================================================
module.exports = router => {

    // POST route to mock a log endpoint
    router.post("/api/login")

    // POST route to mock a forgotten password endpoint
    router.post("/api/forgot-password")
} 
```

这段代码要做的是导出一个函数，该函数将 Express 实例作为参数来创建我们将在测试 API 中使用的实际路线。现在，它缺少参数，因为它只添加了第一个参数，该参数告诉 express 要监听的路由。在这个参数之后，我们可以根据需要添加任意数量的 Express 中间件。我们将添加中间件来进行输入数据验证，如果有错误的话进行错误处理，最后，如果一切顺利的话，如果验证没有错误，就用一个成功消息进行响应。

现在，让我们在开始之前将它添加到`server.js`中

```
// Routes
// =============================================================
require("./routes/auth.routes")(router) 
```

> 这一行应该在我们用`router.listen`启动服务器之前添加

所以现在我们的 API 正在监听`localhost:8080/api/login`和`localhost:8080/api/forgot-password`上的 POST 请求，但是我们仍然没有功能，让我们到达那里。

### 验证用户的输入

现在是验证数据的时候了，为此，我们将使用 express-validator，这是一个方便的中间件，它允许我们验证来自 req 对象的数据，为我们期望的每个参数设置特定的错误消息。

```
mkdir validator
cd validator
touch auth.validator.js
cd .. 
```

现在，打开`auth.validator.js`，我们将首先从`express-validator`导入`check`函数。

```
import { check } from 'express-validator/check' 
```

接下来，我们将创建一个将被导出的函数，我们将在我们的`auth.routes.js`中使用它作为中间件。这个函数接收一个字符串，我们根据路由的用例来定义这个字符串，在这个字符串中，我们将使用刚刚导入的 check 函数来验证我们正在接收的数据。
我们将为此使用一个`switch`，所以我们可以为`login`和`forgot-password`重用同一个验证器。

代码如下:

```
exports.validator = functionName => {

    switch (functionName) {

        case 'login': {
            return [
                check('email')
                    .exists().withMessage('emailRequiredField')
                    .isEmail().withMessage('emailIsEmail'),

                check('password')
                    .exists().withMessage('passwordRequiredField')
            ]
        }

        case 'forgotPassword': {
            return [
                check('email')
                    .exists().withMessage('emailRequiredField')
                    .isEmail().withMessage('emailIsEmail')
            ]
        }
    }

} 
```

我们不会深入讨论`check`函数如何工作的细节，但它基本上是在`req`中添加了另一个对象来存储错误(如果有的话)。

不过，值得注意的是，我们没有设置正常的错误消息，而是使用了在 i18n 文件中创建的变量！

为什么？因为我们想在用户选择的任何语言中使用来自我们的`i18n.js`的那些`keys`,所以我们需要检查所有可能的错误消息的对象，并检查我们翻译的错误对象，并用我们用用户语言编写的实际错误消息交换错误字符串...但还没有。

现在，我们将把这个验证器添加到我们的路由文件中，方法是转到`auth.routes.js`并导入它:

```
import { validator } from '../validator/auth.validator' 
```

> 这一行应该放在最上面，在我们的`module.exports`之前

现在，我们将在实际路线上使用它:

```
// POST route to mock a login endpoint
router.post("/api/login", validator('login'))

// POST route to mock a forgotten password endpoint
router.post("/api/forgot-password", validator('forgotPassword')) 
```

所以现在我们的服务器监听这两条路由上的 post 请求，并验证传入的有效负载。现在我们需要确保转换这些字符串。

### 翻译错误

为此，我们将创建另一个 Express 中间件，它将检查所有错误(如果有的话),并将它们转换成用户语言的字符串。

```
cd utilities
touch processErrors.js
cd .. 
```

继续打开这个新文件，我们将从`express-validator`和 npm 包`object.fromentries`导入另一个函数。

```
import { validationResult } from 'express-validator/check'
import fromEntries from 'object.fromentries' 
```

现在，我们需要创建完成翻译的函数:

```
const translateMessages = (errObj, req) => {
    // Convert the errObj to an Array
    const errArr = Object.entries(errObj)
    // For each array(err), compare the error msg with the polyglot phrases, and replace it.
    errArr.forEach(err => {
        Object.keys(req.polyglot.phrases).forEach(phrase => {
            if (phrase == err[1].msg) {
                err[1].msg = req.polyglot.t(phrase)
            }
        })
    })

    // Return a function that converts the Array to an Object
    return fromEntries(errArr)
} 
```

在这段代码中，我们接收用`express-validator`创建的 error 对象(稍后我们将使用`validationResult`函数从`req`对象中提取)和 Express' `req`对象。

我们从`errObj`中创建一个`Array`，然后，对于每个条目，我们将我们设置的字符串作为错误变量，并将其与来自翻译消息的密钥进行比较，更改`errArr`T3 中的字符串(每个“err[1】)。msg") 到所需语言的多种语言的实际短语*(每个“短语”)*。

最后，我们使用导入的`fromEntries`函数，将数组转换回一个对象并返回它。

现在，在同一个文件中，我们将导出一个中间件函数，它将使用这个`translateMessages`函数来处理错误(如果有的话)。

```
exports.procErr = (req, res, next) => {

    // Verifies if there were validation errors added to the request
    const validationErrors = validationResult(req)

    // If there were errors in the validation
    if (!validationErrors.isEmpty()) {
        // Return the result of the function below
        return res.status(400).send(translateMessages(validationErrors.mapped(), req))
    } else {
        // If no errors, go!
        next()
    }

} 
```

在这段代码中，我们从 Express 接收常规的`req, res, next`，我们首先使用 express-validator 的`validationResult`验证是否有任何错误。然后，我们检查是否有错误，如果有，我们用 Express' response 返回它们。
仔细检查返回，正如您所见，我们发送了接收`validationErrors`和`req`对象的`translateMessages`函数的结果。
我们还有一个`else`，当没有验证错误时，调用`next()`继续下一个 Express 中间件。

### 发送错误

因此，我们能够通过将错误从字符串转换为翻译后的版本来管理错误，并将其打包到一个对象中，以便在需要时发送给用户。

现在，我们只需要使用那个文件！
让我们回到我们的`auth.routes.js`文件，通过导入来使用这个新函数:

```
import { procErr } from '../utilities/processErrors' 
```

正如我前面提到的，我们将它构建为一个快速中间件，所以我们可以将它添加到我们的事件链中。

然后在实际路线中使用它:

```
// Routes =============================================================
module.exports = router => {

    // POST route to mock a login endpoint
    router.post("/api/login", validator('login'), procErr)

    // POST route to mock a forgotten password endpoint
    router.post("/api/forgot-password", validator('forgotPassword'), procErr)

} 
```

### 移动过去的错误

现在，我们的代码已经可以处理两种语言的错误了，但是成功消息呢？

我们在 i18n.js 文件中已经有了它们，但是我们没有使用它们。让我们编写最后一段代码:

```
mkdir controller
cd controller
touch auth.controller.js
cd .. 
```

打开这个新文件，在这里我们将创建几个导出来处理`login`和`forgot-password`流程的最后步骤。
如果 express 在最后一步没有返回错误，理论上，用户的数据没有错误，所以我们将在这里继续发送成功消息。

当然，在真实世界的应用程序中，我们会去数据库检查用户的数据，并确认它实际上是正确的并且*不仅仅是有效的*，但是这超出了本教程的范围。

所以让我们在`auth.controller.js`上写一些代码。

```
exports.login = (req, res) => {

    // If no validation errors, get the req.body objects that were validated and are needed
    const { email, password } = req.body

    // Here, we would make use of that data, validating it against our database, creating a JWT token, etc...

    // Since all the validations passed, we send the loginSuccessful message, which would normally include a JWT or some other form of authorization
    return res.status(200).send({ auth: true, message: req.polyglot.t('loginSuccessful'), token: null })
}

exports.forgotPassword = (req, res) => {

    // If no validation errors, get the req.body objects that were validated and are needed
    const { email } = req.body

    // Here, we would make use of that data, validating it against our database, creating a JWT token, etc...

    // Since all the validations passed, we send the emailSent message
    return res.status(200).send({ auth: true, message: req.polyglot.t('emailSent') })
} 
```

如您所见，这两个函数都被导出以在`routes`文件中使用，并且都解构了`req.body`以获得我们需要使用的值。

我应该强调的是，在这两种情况下，都将在控制器中进行进一步的验证，比如访问数据库，检查用户是否确实存在，是否被授权登录(并且他们的密码是正确的)，或者他们是否未被禁止，是否被授权请求新密码。

我们假设所有这些事情都已经发生了，只是使用 Express' `res`发送响应，其中包括带有
`req.polyglot.t('key')`的消息。
这将以用户选择的语言获取分配给该键的值，并返回该消息。

现在，我们需要回到我们的`routes`来添加这两个函数。
最终版本的`auth.routes.js`现在应该是这样的:

```
import { validator } from '../validator/auth.validator'
import { procErr } from '../utilities/processErrors'
import { login,
    forgotPassword } from '../controller/auth.controller'

// Routes =============================================================
module.exports = router => {

    // POST route to mock a log endpoint
    router.post("/api/login", validator('login'), procErr, login)

    // POST route to mock a forgotten password endpoint
    router.post("/api/forgot-password", validator('forgotPassword'), procErr, forgotPassword)

} 
```

如您所见，我们导入了`login`和`forgotPassword`，并将它们作为最终参数添加到`post`中。
当一切正常时，这些最后的功能会响应成功消息！

### 测试

让我们检查一下我们的 API 是否像预期的那样工作。

继续运行`npm run start`。这将构建我们的 transpile 代码并启动服务器。如果我们遵循所有步骤，我们应该在控制台中看到:`App running on port 8080`。

现在打开**邮差**。

*   将*方法*设置到**岗位**
*   将*请求 URL* 设置为 **localhost:8080/api/login**
*   将*报头*键设置为**接受语言**，并将值设置为 **es_MX**
*   将*正文*设置为{ " email ":"【test@email.com】T2" }

并点击**发送**。如果一切顺利，您应该会看到以下响应:

```
{
    "password": {
        "location": "body",
        "param": "password",
        "msg": "'password' es un campo requerido."
    }
} 
```

你可以使用*请求 URL* 尝试两种路由或者*报头*设置`en_US`或者`es_MX`或者其他选项，同样，尝试修改*主体*来查看来自 API 的不同响应。

原来如此！
现在，希望您已经清楚地了解了如何设置一个 Express API，无论您的`headers`设置为`es_MX`还是`en_US`，它都能正确响应。错误和成功消息。

如果您有任何问题，请在下面留下评论，或者在存储库上创建一个问题，或者给我发一条 [tweet](https://twitter.com/IsraelMuCa/) 。
我非常乐意帮忙。

一会儿见！
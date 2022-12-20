# 如何保护 Node.js 应用程序免受恶意依赖

> 原文：<https://dev.to/bnevilleoneill/how-to-protect-your-node-js-applications-from-malicious-dependencies-3d3a>

[![](img/5a11b6677473f7649b825a8f8279dda1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--g0UyyBNT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AGG67wdykOg7Zk4h8cjqJIQ.jpeg)

你可能听说过[最近的一个事件](https://blog.npmjs.org/post/180565383195/details-about-the-event-stream-incident)，一个流行的 npm 包 [*事件流*](https://github.com/dominictarr/event-stream/issues/116#issue-382854428) ，包含了可能影响数千个应用程序(甚至更多)的恶意代码。).希望这次攻击是有针对性的，只影响一个特定的项目。

该库的原始作者是社会工程攻击的受害者，一个恶意黑客获得了发布权限。许多人认为原作者应该更加谨慎。

但这不是真正的问题。

为什么？

因为库的原始作者可能故意发布了恶意代码，所以任何拥有库的人都可以随时发布恶意代码。我们很多人都依赖于荣誉系统，希望没有人会发布恶意代码。

我们如何防止这种情况发生？

嗯，总是会有多种方式侵入系统，并向我们的应用程序中注入恶意代码。不仅通过依赖性，还通过无意的漏洞。

然而，我们仍然可以考虑如何防止这些事情发生，但更重要的是，我们需要考虑减轻其影响的方法。

[![](img/2063f22e152ef36aa267c13ae9842601.png)](https://logrocket.com/signup/)

### 预防

你现在可以采取一些预防措施:

*   **锁定你的依赖关系**。使用 package-lock.json 或 yarn.lockto 防止在部署时获得自动更新(在服务器中安装 npm/yarn 时)。至少这样你会有更少的机会得到 npm 团队还没有清理的恶意更新。然而，这并不能阻止事件流对您的影响，因为恶意代码已经在 npm 注册表中存在数周了。但是这可能会阻止你在 7 月份发生在 T2 的另一起事件。
*   **使用** [**npm 审计**](https://docs.npmjs.com/cli/audit) **、** [**Snyk**](https://snyk.io/) **和/或** [**GitHub 安全警报**](https://help.github.com/articles/about-security-alerts-for-vulnerable-dependencies/) 在您的任何依赖项可能包含安全漏洞时得到通知。

### 缓解

现在，一旦攻击被触发，我们如何减轻它的影响？

大多数攻击包括窃取数据、挖掘并将结果发送回服务器等。因此，您可以使用权限非常有限的用户来执行 Node.js:限制文件系统访问，配置 iptables 以限制应用程序只能连接到某些域，等等。问题是，在云服务时代，你可能无法在你的云提供商那里做到这一点。

在 Node.js 内部我们能做些什么吗？

Node.js 的贡献者已经开始考虑一个 [Node.js 安全模型](https://docs.google.com/document/d/1h__FmXsEWRuNrzAV_l3Iw9i_z8fCXSokGfBiW8-nDNg/view#heading=h.v9j0bjkph3tv)。因此，我们可以期待将来在 Node.js 中实现不同级别的安全性。

我个人喜欢一个权限系统，在这里你可以定义你需要在你的 package.json 中访问什么

```
{
  "permissions": {
    "fs": {
      "directories": {
        "$TEMP": "rw",
        "$SRC_ROOT": "r"
      }
    },
    "network": {
      "tcp": {
        "v4:*:$PORT": "LISTEN"
      }
    }
  }
} 
```

这类似于现代浏览器中的内容安全策略。

当然，这只是我的建议，Node.js 安全模型的想法才刚刚开始评估。不要指望在不久的将来会实现。

那么，我们现在能做点什么吗？更具体地说，在不改变 Node.js 内部结构的情况下，我们能在 Userland 中做些什么呢？

答案是肯定的！

### 沙盒化你的应用——硬核方式

由于 Node.js 也遵循 JavaScript 的动态特性，我们能够*侵入*运行时。我们可以:

*   **劫持**require()调用并操纵里面的代码。ts-node/register 和@babel/register 就是这么工作的。
*   **用 [vm 模块](https://nodejs.org/api/vm.html)在沙盒环境中运行**代码，并传递一个定制的 require 函数来阻止访问某些模块，或者包装核心模块来阻止访问某些东西。

**或**

*   直接**覆盖**核心模块。让我们看看如何做到这一点:

我将展示覆盖 readFileSync 以防止访问特定目录中的文件的概念证明。在实践中，我们应该覆盖一些其他功能，我们也可以选择白名单，而不是黑名单某些目录。

但作为一个例子，我只想防止恶意代码:

```
// malicious.js
const fs = require('fs')
const secrets = fs.readFileSync('/system/secrets.txt', 'utf8')
console.log(secrets); 
```

我将实现一个覆盖 fs 核心模块的 cage.js 文件，并拦截该函数，防止访问/system/:
中的文件

```
// cage.js
const fs = require('fs')
const path = require('path')
const wrap = (module, name, wrapper) => {
  const original = module[name]
  module[name] = wrapper(original)
}
wrap(fs, 'readFileSync', (readFileSync) => (...args) => {
  const [filepath] = args
  const fullpath = path.resolve(filepath)
  if (fullpath.startsWith('/system/')) {
    throw new Error('You do not have permissions to access this file')
  }
  return readFileSync(...args)
})
// Prevent further changes
Object.freeze(fs) 
```

瞧啊。就在那里。现在，如果我们直接运行恶意代码:

`node malicious.js`

我们将看到该文件的内容被打印到标准输出中。但是如果我们告诉 Node.js 首先像这样运行 cage.js:

`node -r cage.js malicious.js`

我们将看到恶意代码无法访问文件的内容，并抛出了一个错误。

显然，这只是一个概念证明。下一步将是覆盖更多的函数，使其可配置，而不是硬编码文件路径，理想情况下，对其他核心模块也是如此。例如覆盖 [http(s)。请求](https://nodejs.org/api/http.html#http_http_request_options_callback)。

### 结论

*   我们的应用中的恶意代码(或只是易受攻击的代码)是一个日益严重的问题，因为我们的应用变得越来越复杂，依赖于更多的依赖项，使得攻击面越来越大
*   诸如 [npm 审计](https://docs.npmjs.com/cli/audit)、 [Snyk](https://snyk.io/) 和/或 [GitHub 安全警报](https://help.github.com/articles/about-security-alerts-for-vulnerable-dependencies/)之类的服务和工具非常有用，你现在就可以开始使用它们
*   我们需要减轻攻击的影响，Node.js 需要在这方面做些什么。然而，解决方案不是在不久的将来
*   如果你想走“硬核之路”，你可以！Node.js 足够灵活，允许你做一些疯狂的事情来保护自己。我们刚刚演示过了🙂

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *
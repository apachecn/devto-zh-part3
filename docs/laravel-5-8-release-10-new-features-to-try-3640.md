# Laravel 5.8 版本:10 个新特性可供尝试

> 原文：<https://dev.to/bnevilleoneill/laravel-5-8-release-10-new-features-to-try-3640>

[![](img/95341a7962871efb50bc88d51de6b2eb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--duryLWIF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A2gKr79YTmj2MkP8zLOn-bg.png)

Laravel 的最新版本[5.8 版](https://laravel.com/docs/5.8)，最近已经发布。这个版本有许多令人兴奋的新功能，它是 Laravel 以前版本的持续改进。这些功能包括:

*   自动策略解析
*   碳 2.0 支持
*   通过雄辩的关系
*   令牌保护令牌散列
*   缓存 TTL
*   调度程序时区配置
*   工匠::呼叫改进
*   工匠服务改进
*   模拟测试助手方法
*   高阶或有说服力的方法

还有很多。在本文中，我将更深入地讨论这些新特性。

[![](img/2063f22e152ef36aa267c13ae9842601.png)](https://logrocket.com/signup/)

### 1。自动策略解析

策略是 Laravel 处理用户授权的两种主要方式之一。它们是围绕特定模型或资源组织授权逻辑的类。在 Laravel 的早期版本中，需要在 AuthServiceProvider 中注册策略，如下所示:

[https://medium . com/media/2fb 3d D8 e4c 7a e2a 4c 29 e 2e 47 f 10 e 4281/href](https://medium.com/media/2fb3dd8e4c7ae2a4c29e2e47f10e4281/href)

在本例中，我们注册的策略是一个名为 TransactionPolicy 的策略，我们将它输入到$policies 数组中。

然而，从 Laravel 5.8 开始，您不需要手动注册模型的授权策略。只要模型和策略遵循标准的 Laravel 命名约定，并且策略目录位于其默认位置，Laravel 就可以自动发现策略。

如果您的模型或策略位于不同于默认 Laravel 结构的位置，那么您可以使用 Gate::guessPolicyNamesUsing 方法注册一个自定义回调。通常，应该从应用程序的 AuthServiceProvider 的 boot 方法中调用此方法，如下所示:

[https://medium . com/media/30091 A8 C5 d 2328 cf 93d 24167 e41e 2648/href](https://medium.com/media/30091a8c5d2328cf93d24167e41e2648/href)

### 2。碳 2.0 支持

[Carbon](https://carbon.nesbot.com/) 是一个包，它扩展了 PHP 自己的 [DateTime](http://www.php.net/manual/en/class.datetime.php) 类，使得处理日期和时间变得非常容易。Laravel 5.8 为 Carbon 的 2.0 版本提供了支持。Carbon 2.0 的新特性包括可碳化的类和新的日期外观。让我们看看这是如何工作的。

在 Laravel 5.8 安装的\routes\web.php 文件中输入以下内容:

[https://medium . com/media/9 BAE 81848 ab 493 c 02 ce 2 EB 0 b 36 a 543 c 3/href](https://medium.com/media/9bae81848ab493c02ce2eb0b36a543c3/href)

这里我们创建了一个 carbon 路径，它将当前日期保存在一个$date 变量中，然后显示它。然后，它将当前日期加上三(3)天，并显示出来。如果您访问我们刚刚创建的/carbon 路线，您会看到类似这样的内容:

[![](img/15b0d9618b17a08830744a2b8cfe795a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eDidVTSc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AccaU-iu7JP7Bi4Rg4OzERA.png)

这里发生的是我们正在改变我们的对象。这可能是您所希望的，但在许多情况下，这不是我们想要的，因为日期通常是受保护的属性。我们实际上应该能够创建一个新的日期，而不修改现有的日期。例如，假设我们将出生日期存储在数据库的某一列中，这是不会改变的信息，但是，我们可以为它创建一个副本，并对该副本进行一些修改。这就是可碳化的类的用武之地。要使用此功能，请转到您的 AppServiceProvider 并输入以下内容:

[https://medium . com/media/0bc 325 a 079 ab 177 a 98 b 1185 a 5308 b 484/href](https://medium.com/media/0bc325a079ab177a98b1185a5308b484/href)

然后更新\routes\web.php 文件以使用新的日期外观，并创建一个我们可以更改的日期副本:

[https://medium . com/media/CB 99d 34 aa 4540 c 46 a7 c 42d 954 aa 2 ba 50/href](https://medium.com/media/cb99d34aa4540c46a7c42d954aa2ba50/href)

刷新您的浏览器，您应该会看到:

[![](img/e7728e716cb4858540ac9a60437c1d41.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uR8rYM4I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AlefUyoI2Q0vm_XTXX6MV9g.png)

### 3。有一段雄辩的关系

Laravel 5.8 引入了一个新的雄辩关系:HasOneThrough。尽管这在 Laravel 中是新的，但它也存在于其他框架中，如 Rails。假设我们有三个模型:供应商模型、账户模型和账户历史模型。一个供应商有一个帐户，一个帐户有一个帐户历史。

以前，要获得供应商的帐户历史，您必须找到供应商，然后编写如下内容:$ supplier-> account-> account history。现在，您可以使用 hasOneThrough 关系跳过这一步，直接访问供应商的帐户历史，如下所示:$ history = $ supplier-> account history 通过帐户模型:

[https://medium . com/media/972 c8 ad 6 e 5 FB 9 AC 896703 E3 a 96354586/href](https://medium.com/media/972c8ad6e5fb9ac896703e3a96354586/href)

### 4。令牌保护令牌散列

关于 Laravel API 认证的一个鲜为人知的事实是，您并不总是必须使用 Laravel Passport。有一个更简单的令牌保护，它提供基本的 API 认证，在 Laravel 5.8 中，它现在支持将令牌存储为 SHA-256 哈希。这比存储纯文本令牌更安全。

### 5。缓存 TTL

在 Laravel 以前的版本中，缓存是以分钟为单位设置的。在 5.8 版中，这一点已更改为秒，以便在存储项目时更精确、更精细地设置到期时间，并符合 PSR-16 缓存库标准。因此，在应用程序中引用缓存时，请记住更新为秒:

[https://medium . com/media/06d 385 E8 aa 1 ff 6 fa 381 ba 30 DD 066299 a/href](https://medium.com/media/06d385e8aa1ff6fa381ba30dd066299a/href)

### 6。调度程序时区配置

在 Laravel 中，您可以使用 timezone 方法为计划任务定义时区，如下所示:

[https://medium . com/media/d 849 ed 8 ACA 1 f 2095666 cf 4 af 4 f 983251/href](https://medium.com/media/d849ed8aca1f2095666cf4af4f983251/href)

在以前的版本中，您必须为每个计划的任务重复这一过程，如果您有很多这样的任务，这很快就会变得很麻烦。在 Laravel 5.8 中，你可以在你的 app/Console/kernel.php 文件中定义一个名为 scheduledTimezone 的方法，并返回你的默认时区。这将附加到您拥有的每个计划程序:

[https://medium . com/media/9 b 2 b 0 e 7 c 4c 4 ab 91 ca 315 f 11497485183/href](https://medium.com/media/9b2b0e7c6c4ab91ca315f11497485183/href)

### 7。工匠呼吁改进

Laravel 允许您使用 Artisan::call 方法从代码中生成 Artisan 命令。在 Laravel 的早期版本中，如果您需要向命令传递一些选项，通常会这样做:

[https://medium . com/media/c3ba 29186 eff 00 c 6 b5 f 8493015 e 13 b 14/href](https://medium.com/media/c3ba29186eff00c6b5f8493015e13b14/href)

现在，在 5.8 中，您可以像这样在一个字符串中传递选项，而不是将选项作为数组传递:

[https://medium . com/media/28802 DD 4 F3 F3 DD 857 CD 161240226 BF 31/href](https://medium.com/media/28802dd4f3f3dd857cd161240226bf31/href)

### 8。工匠服务改进

快速为 Laravel 应用程序提供服务的一种方法是运行 php artisan serve 命令。在 Laravel 的早期版本中，该命令将在默认端口 8000 上运行您的应用程序，如果您试图使用相同的命令服务另一个应用程序，将会失败。现在，在版本 5.8 中，serve 命令将扫描高达端口 8009 的可用端口，以便您可以一次服务多个应用程序。

### 9。模拟测试助手方法

这是使您的测试代码更干净和可读的另一个改进。假设我们想要模拟一个事务服务，并让它返回一些虚拟的事务数据。在 Laravel 以前的版本中，我们会这样写:

[https://medium . com/media/60 e 5 eaba 04d 08 f 8 df 9 b42 c 14320 D1 f 98/href](https://medium.com/media/60e5eaba04d08f8df9b42c14320d1f98/href)

在 Laravel 5.8 中，这可以简化为:

[https://medium . com/media/5d 62913168 EBD 158 be 89 b 31744051 ad 0/href](https://medium.com/media/5d62913168ebd158be89b31744051ad0/href)

这就负责调用嘲弄并将其绑定到容器中。注意，我们不必调用$this->instance

### 10。高阶或有说服力的方法

以前，如果我们想将限定范围的查询与 or 结合起来，我们通常会像这样定义一个闭包:

[https://medium . com/media/33129d 7 b 0 ee 762 b 8 ad 4b 8 b 35214 f 03 e 4/href](https://medium.com/media/33129d7b0ee762b8ad4b8b35214f03e4/href)

Laravel 5.8 引入了一个“高阶”orWhere 方法，所以你不需要再写上面的闭包了。相反，您可以这样写:

[https://medium . com/media/01f 97027 a 48 b 961 f 259 a 0 a 9 AE 7 c 222 a 7/href](https://medium.com/media/01f97027a48b961f259a0a9ae7c222a7/href)

### 结论

这个新版本的 Laravel 加载了许多令人兴奋的特性，我们已经完成了框架中一些最显著的改进。有关如何将您现有的 Laravel 应用程序升级到版本 5.8 的详细信息，请参见[升级指南](https://laravel.com/docs/5.8/upgrade#upgrade-5.8.0)。你对 Laravel 5.8 有什么看法？请在评论区告诉我！

### 更多资源

如果您想了解更多关于 Laravel 的新特性，请查看以下资源:

*   [Laravel 5.8 的新功能](https://laracasts.com/series/whats-new-in-laravel-5-8)
*   【5.8.x 的发行说明
*   [正式文件](https://laravel.com/docs/5.8/)

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *
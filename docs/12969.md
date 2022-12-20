# LWC—通过闪电数据服务获取数据

> 原文：<https://dev.to/brettmn/lwc--getting-data-with-lightning-data-service-20gl>

[https://www.youtube.com/embed/_akXrhRUTms](https://www.youtube.com/embed/_akXrhRUTms)

你好，我是布雷特和 WIPDeveloper.com。到目前为止，对于我们的第一个 lightning web 组件，我们只获得了 HTML 和 JavaScript 文件之间的标记和一些通信，我们还没有与 Salesforce 进行任何实际的通信。由于我们正在构建使用我们在 Salesforce 中的组件，我们将需要能够实际访问托管在那里的数据。

## 使用闪电数据服务

因此，我们将了解如何使用闪电数据服务。为我们的组件获取信息。我想做的是，不是在 JavaScript 文件的标签中进行硬编码，而是从登录的用户那里获取公司名称，这样我们首先需要获取用户 Id

在 JavaScript 文件中。我们可以从`@salesforce/user/id`导入用户 id

现在我们有办法了。但是我们需要在我们的应用程序中公开它。所以我要做的是让`id`在这里，我要把`userId`赋给它。如果我在标记中添加对`id`的引用，我应该能够获得当前用户的标记`id`。在这里的最底部，我将只添加段落标记，它告诉我这是用户 id。

现在，这将是已经登录的用户的`id`。我要快速推进这个。

回到这里，刷新它，我应该看到…

我将刷新它并清空缓存，我应该会在底部看到 ID。好了，用户 ID。

现在，要使用 Lightning 数据服务，我们必须我们现在将使用 Lightning 数据服务，我们将使用`lightning-record-view-form`来获取用户信息。

自动完成在这里很有帮助`lightning-record-view-form`，然后我们给它提供一个`record-id`

我们已经提供了一个不准备保存的`object-api-name`

与其在这里关闭它，我想把

把整个包起来。现在我们拥有的整个组件被包装成一个`lightning-record-view-form`，我们可以用`ightning-output-field`来代替

是啊`ightning-output-field`并提供它一个`CompanyName`的`field-name`。

现在我们也添加了这个，这样它就可以代替标签使用了。

当我们部署这个的时候，

我们可以按刷新返回我们的用户界面。

我打破了一些东西。

我搞砸了什么？

好吧，我明白了。我们要在这里读它

除了这里。我们将实际拼出`record-id`。

我拼错了`record-id`。

重新部署，刷新。

请记住，清除缓存并刷新。

事实是，我拼错了`record`，所以不要这样做

重新部署和刷新我们现在可以访问我们的公司名称。让我们看看是否可以去掉写着公司名称的地方，以防万一我们不想要它。因此，为了使我们没有公司名称，我们所做的是添加`variant`。

我们将使用`label-hidden`的变体

我们要把它加到他们两个身上。

移除底部的用户 ID 和底部的名称，以便一次性部署所有内容。

刷新页面。

那里。现在这是来自登录的用户。我们能证明它来自登录的用户吗？好吧，让我们转到我们的设置并更新我们的公司名称。

出几个感叹号有什么，因为，为什么不呢？

现在回家吧

刷新。

有东西坏了。就在那里。花了点时间加载。所以你走吧。我们现在使用闪电数据服务获得公司名称。

## 现在就这样。

记得报名参加 **[每周一次的单口相声！你可以得到我们关于 WIPDeveloper.com 的任何最新信息。](https://wipdeveloper.com/newsletter/)**

帖子[LWC——用闪电数据服务获取数据](https://wipdeveloper.com/lwc-getting-data-with-lightning-data-service/)最早出现在[WIPDeveloper.com](https://wipdeveloper.com)上。
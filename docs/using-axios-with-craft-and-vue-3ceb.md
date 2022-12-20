# 将 Axios 与 Craft 和 Vue 配合使用

> 原文：<https://dev.to/jakedohm_34/using-axios-with-craft-and-vue-3ceb>

我花了一上午的时间在 StackOverflow 文章、文档和 Twitter 线程中挖掘，试图找出如何用 Axios 提交登录表单。如果你曾经尝试过使用 Axios 或类似的库在 Craft CMS 网站上发出请求，你就会知道这是一种非常危险的体验。

但现在不是了！我将向您展示如何配置 Axios 来很好地使用 Craft，然后如何将其绑定到 Vue。*注意:Vue 部分是可选的，本文的大部分(步骤 1-3)将适用于任何框架或不适用框架。*

## 前言

在我们开始讨论如何完成一个干净的设置之前，最好先了解一下我们面临的一些挑战。以下是使用 Axios 和 Craft 的两个主要问题:

1.  Craft 希望你的数据被序列化，而不是 JSON:如果你试图发送一个 JSON 对象给 Craft，它不会接受。
2.  Craft 需要一个 CSRF 令牌，这样它就可以验证请求。

带着这些问题，让我们开始吧！

## 向导

### 步骤 1:创建一个 Axios 实例

我们的第一步是安装 Axios ( `npm install axios || yarn add axios`)，并创建它的一个实例，我们将使用它来发出请求。

```
import axios from 'axios'

const axiosInstance = axios.create() 
```

您现在可以使用这个 Axios 实例来发出请求。运行`axiosInstance.get('/endpoint')`会按预期工作！

您可能会想“既然可以直接使用 Axios，为什么还要创建一个实例呢？”很棒的问题！创建 Axios 的一个实例将允许我们专门为 Craft 配置它，并将我们的实例附加到 Vue 以在任何组件中使用。

### 第二步:添加您的 CSRF 令牌

通常当向 Craft 发出请求时，我看到人们在每个请求中都传递他们的 CSRF 令牌，就像这样:

```
const data = { greeting: 'Hello, World!' }
data[window.csrfTokenName] = window.csrfTokenValue
axios.post('/', data)

// note: this code won't actually work, because "data" isn't serialized 
```

这种方法工作得很好，但是必须将您的 CSRF 令牌附加到每个请求上，这很笨拙。谢天谢地，这个问题有一个解决方案！

**右边的 Way™️**

Craft 将允许您在请求中传递一个`X-CSRF-Token`头，以避免将其作为查询参数发送。让我们配置我们的 Axios 实例，让它自动随每个请求一起发送这个消息头，这样我们甚至不用考虑它。

首先，我们需要用 Twig 输出我们的 CSRF 令牌，并把它放在我们可以访问的地方。

```
{# index.twig #}

<script>
window.csrfToken = "{{ craft.app.request.csrfToken }}"
</script> 
```

然后我们可以配置我们的 Vue 实例来添加`X-CSRF-Token`头:

```
const axiosInstance = axios.create({
    headers: {
        'X-CSRF-Token': window.csrfToken
    }
}) 
```

我们已经解决了两个挑战中的一个:通过 CSRF 令牌。现在让我们解决第二个问题！

### 第三步:告诉 Axios 序列化你的数据

在写这篇文章并试图找出一个流畅的工作流程之前，这里是我解决这个问题的方法。

```
 import { stringify } from 'qs'

axios.post('/', stringify({ data: 'things' })) 
```

同样，这是可行的，但并不理想。

经过一番挖掘，我发现了一个名为`transformRequest`的 Axios 配置选项，它允许我们在发送之前将传递的数据转换成一个`post`请求。

让我们更新我们的 Axios 实例来自动字符串化我们的数据:

```
import { stringify } from 'qs'

const axiosInstance = axios.create({
    headers: {
        'X-CSRF-Token': window.csrfToken
    },
    transformRequest: [
        function(data) {
            return stringify(data)
        },
    ],
}) 
```

现在我们有了一个完全正常工作的 Axios 实例，它负责传递我们的 CSRF 令牌并将我们的数据转换成 Craft 期望的格式！

看看这些片段之间的区别，并告诉我这种配置工作不值得！

```
// before
import { stringify } from 'qs'

const data = { greeting: 'Hello, World!' }
data[window.csrfTokenName] = window.csrfTokenValue
axios.post('/', stringify(data)) 
```

```
// after
axiosInstance.post('/', data); 
```

### 步骤 4:将 Axios 实例附加到 Vue

这最后一步使得在我们的 Vue 组件中使用 Axios 更加容易。我们可以将我们配置的 Axios 实例附加到我们的 Vue 实例。

```
const axiosInstance = axios.create(config) // see above for config

Vue.prototype.$axios = axiosInstance 
```

然后，您可以从方法、生命周期等内部发出请求。在你的 Vue 组件里比如`this.$axios.get('/')`。这里有一个更真实的例子:

```
Vue.component('things', {
    methods: {    
        getThings(data) {
            this.$axios.post('/', data);
        }
    }
} 
```

## 完整示例

下面是完整的 Twig/JS 代码，供参考！

```
{# index.twig #}

<script>
    window.csrfToken = "{{ craft.app.request.csrfToken }}"
</script> 
```

```
// index.js

import axios from 'axios'
import { stringify } from 'qs'

const axiosInstance = axios.create({
    headers: {
        'X-CSRF-Token': window.csrfToken,
    },
    transformRequest: [
        function(data) {
            return stringify(data)
        },
    ],
})

Vue.prototype.$axios = axiosInstance 
```
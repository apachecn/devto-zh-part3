# 使用模拟 API 提高开发人员的生产力

> 原文：<https://dev.to/asantos00/using-mocked-apis-to-increase-developer-productivity-13a6>

当我们开发前端时，我们可能足够幸运地拥有一个成熟的 API，尽管在现实世界中，大多数时候，我们会让自己陷入这样一个境地，即我们不得不在没有 API 的情况下进行开发，而我们未来需要的所有功能都是可用的。

有一些工具可以让我们最小化这种依赖性，我记得`ember`有很棒的`ember-cli-mirage`。它提供了你需要的功能，与`ember`深度集成，让你用一个完全模仿的 API 默认开发**，我认为这是一个很好的开始原则。
能够在不依赖外部服务器的情况下运行应用程序也很棒(想象一下在飞机上、在没有互联网的旅途中，或者只是在低内存的计算机上)。你不希望你的 UI 开发人员为了开发一个按钮而运行 3 台服务器。**

 **## 用例

过去几天，我们面临着类似的问题。我们正在开发一个屏幕，我们有 API 签名，但是 API 还没有准备好。

一种选择是在等待完成的 API 时，将有效载荷硬编码在某个地方。当然，我们可以用它来开发你的按钮，但这样就不可能实现应用程序中处理请求的部分。我们还希望，在未来，花费尽可能少的努力，从模拟数据变成真正的 API。

这个解决方案看起来并不正确，尤其是当我们还记得`ember-cli-mirage`的时候。不幸的是，我们不能将`ember-cli-mirage`与其他库一起使用，尽管我们真的很怀念它提供的功能。

我们最终的解决方案是将`json-server`和`route-parser`结合起来，并使用`axios`请求拦截器。

在我们的 http-client 中，我们调用了`axios`，所以，我们做的第一件事就是添加这一行。

```
// httpClient.js
import mockApiRequestInterceptor from './mockApiRequestInterceptor';

axios.interceptors.request.use(mockApiRequestInterceptor);
// Please keep in mind that if you use axios instance, 
// you have to do it on the instance 
```

我们已经创建了请求拦截器

```
// mockApiRequestInterceptor.js
import UrlPattern from 'route-parser';
import { API_BASE_URL } from 'appConfig';

const MOCK_API_BASE_URL = 'http://localhost:3001/api';
const MOCK_URLS = [
  `${API_BASE_URL}/:user/posts/:id`
];

export default (config) => {
    if (!IS_DEVELOPMENT) {
        return config;
    }

    const matchedUrl = MOCK_URLS.find(mockUrl => {
        const pattern = new UrlPattern(mockUrl);
        return pattern.match(config.url);
    });

    if (matchedUrl) {
        config.url = config.url.replace(API_BASE_URL, MOCK_API_BASE_URL);
    }

    return config;
}; 
```

被模仿的网址将被添加到`MOCK_URLS`，如果有一个*被模仿的版本*，它们将被重定向。

因此，这段代码会将来自 URL`https://mycrazyserver.com/api/asantos00/posts/123`的请求重定向到`http://localhost:3001/api/asantos00/posts/123`，部分问题得到了解决。

在这之后，我们利用了另一个很棒的包`json-server` (TLDR: *通过 REST routes* 提供 JSON 文件)。

我们已经创建了一个 npm 脚本，通过一些配置来运行这个`json-server`(我将在后面解释)。

```
//  package.json  {  "scripts":  {  "mock-api:serve":  "json-server --watch mock-api/db.json --port 3001 --routes mock-api/routes.json"  }  } 
```

并创建了一个`db.json`文件

```
//  mock-api/db.json  {  "posts":  [  {  "id":  1,  "title":  "Mocking an API with axios",  "author":  "asantos00",  "createdAt":  1557937282,  "body":  "Lorem ipsum dolor sit amet, consectetur."  },  {  "id":  2,  "title":  "Whatever post",  "author":  "asantos00",  "createdAt":  758851200,  "body":  "Lorem ipsum dolor sit amet, consectetur."  }  ]  } 
```

它自动为`db.json`文件上的每个实体创建 rest 端点，并观察 db 文件的变化。

如前所述，`json-server`为实体创建 REST 端点，这意味着在`db.json`上有了实体`post`之后，会创建以下端点:

*   `GET /posts` -返回帖子列表
*   `GET /posts/:id` -用发送的 id 返回帖子
*   `POST /posts` -创建帖子
*   `PUT /posts/:id` -用发送的 id 替换帖子

创建自定义路线的文件是`routes.json`。

```
//  mock-api/routes.json  {  "/api/*":  "/$1",  "/:user/posts/:id":  "/posts?user=:user"  } 
```

routes 文件只说无论什么请求到达`/:user/posts/:id`，我们都重定向到自动创建的端点`/posts`。是的，`json-server`也支持过滤，我们在这里使用查询参数`user`来实现。

**完成了！**现在，我们的应用程序将继续向已实现的端点发出请求。但是我们可以继续用模拟的 API 开发这个页面。
一旦我们想使用真正的 API，只需将 url 从`mockApiRequestInterceptor`文件中的`MOCK_URLS`(也可以提取到一个单独的文件)中移除即可。

## 结论

最后，这个解决方案真正改进了流程，帮助我们不再担心后端端点是否完成，同时在更接近真实的环境的*中实现所有情况。
另一件好事是，它也是**库不可知的**，因为它只依赖于 *http-client* ，所以你可以从`react`到`backbone.js`甚至到`react-native`使用它。*

如果没有我使用过的**令人惊叹的开源包**，这一切都是不可能的，这只是将所解释的用例的各个部分组合在一起的问题。

你用什么办法解决这个问题？这是你经常碰到的事情吗？**
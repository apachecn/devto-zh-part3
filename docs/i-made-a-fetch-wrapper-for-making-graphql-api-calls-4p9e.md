# 我做了一个获取包装器来调用 graphql api！

> 原文：<https://dev.to/ianfabs/i-made-a-fetch-wrapper-for-making-graphql-api-calls-4p9e>

有一天，我正在为我正在构建的一个附带项目使用 GraphQL 查询，这时我意识到最终我将需要在客户端调用那个 API。我的第一反应是寻找使用 fetch 的方法，因为我喜欢 fetch API。然而，老实说，用 fetch 来做这件事的方式有点恶心。我在堆栈溢出上找到的第一个答案是这样的:

<small>注意，对于这些例子，使用了我创建的示例 API</small>

```
 fetch('https://csb-xpwq1o2824-xravvsjkul.now.sh/', {
    method: "POST",
    body: JSON.stringify({query: "query {items{title}}"})
}) 
```

这看起来很恶心。你可以用一些变量让它变得更好，也许是一个模板字符串，就像这样:

```
const url = "https://csb-xpwq1o2824-xravvsjkul.now.sh/";
const query = {
    query: `
        query{
            items{
                title
            }
        }
    `
}
const body = JSON.stringify(query);

fetch(url, {
    method: "POST",
    body
}) 
```

现在，虽然**可能**看起来令人满意，但它感觉不像是 fetch 和 graphql 之间足够健康的平衡。所以我创建了一个函数来完成这个任务。我意识到这对其他开发人员是多么有用，所以我把它包装在一个 npm 模块中，并发布了它。我把它叫做`jraph`，它是这样工作的，感谢 [@qm3ster](https://dev.to/qm3ster) 的评论，它现在是这样工作的:

```
import { jraph } from "jraph";

let jql = jraph("https://csb-xpwq1o2824-xravvsjkul.now.sh/");

(async () => {
    let result = await jql`{
      items{
        title
        info
      }
    }`
    console.log(result)

})(); 
```

喜欢的可以上 npm，[这里](https://www.npmjs.com/package/jraph)！

祝你们今天愉快！
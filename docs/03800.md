# dev.to API:如何将 dev 帖子转换成明信片📫

> 原文：<https://dev.to/danielharding/dev-to-api-how-to-turn-posts-into-postcards-2ci7>

我从开发社区中获益良多。无论是有用的提示、项目想法还是只是学习他人的经验。这是我所知道的唯一一个为各种能力的博客作者和程序员提供平等平台的地方，融合的方式使得访问有趣和相关的内容看起来很简单。

但要让它存在，这个社区依赖于那些积极分享新鲜有趣内容的用户。这不是一件容易的事情，在成为会员近两年后，直到二月我才足够自信地公开发表文章。然而，一旦写完了，我收到的积极反应鼓励我更经常地写反思。

虽然我的工作涉及技术，但我不是开发人员。事实上，我的大部分职业关系网都在教育领域，这使得同事或同行不太可能接触到 DEV 网站。所以当我继续写作的时候，我觉得找到一种更灵活的分享内容的方式变得越来越重要，并且这种方式可以接触到尽可能多的受众。Twitter 有所帮助，但如何伪装开发内容，使其显得不那么“技术化”？

**答案:开发到 API。**🎉

但是有一个问题。据我所知，dev.to API 仍处于试验阶段。然而，尽管没有官方文档，来自其他用户的帖子提供了足够的信息来测试它。

通过向基本 URL 添加用户名参数(例如[https://dev.to/api/articles?username=devteam](https://dev.to/api/articles?username=devteam))，API 返回包含与来自指定用户的文章相关的元数据的 JSON 文件。用一个基本的 fetch()方法“获取”数据后，就可以用 JavaScript 操作数据并添加到 DOM 中。

下面的代码片段展示了这是如何工作的，将 JSON 打印到控制台:

```
 <script>
        fetch('https://dev.to/api/articles?username=devteam')
        .then(function(response) {
            return response.json();
        })
        .then(function(myJson) {
            console.log(myJson)
        });
    </script> 
```

Enter fullscreen mode Exit fullscreen mode

对我来说，这为将开发内容添加到其他网站(包括我自己的网站)提供了一个完美的解决方案，在定义它如何出现方面具有额外的灵活性。例如，下面的 CodePen 展示了如何将每篇文章呈现为一个独立的卡片，以匹配包含站点的方式进行设计。而且通过对目标 div 使用 CSS Grid，可以向页面添加任意数量的帖子，同时保持布局的响应性。

[https://codepen.io/danieladamharding/embed/GaJgLN?height=600&default-tab=result&embed-version=2](https://codepen.io/danieladamharding/embed/GaJgLN?height=600&default-tab=result&embed-version=2)

如果你想看看如何使用 API 来创建一个投资组合网站的博客部分，请访问[danharding.co.uk](https://danharding.co.uk)或随意使用 CodePen。

如果你有任何反馈，我总是有兴趣听听！👨🏻‍💻
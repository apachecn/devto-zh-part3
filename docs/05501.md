# 在 Adonis.js 中重构搜索查询

> 原文：<https://dev.to/michi/refactoring-search-queries-in-adonis-js-547h>

> 最初发布于[michaelzanggl.com](https://michaelzanggl.com/articles/refactoring-search-queries-in-adonisjs)。订阅[我的简讯](https://michaelzanggl.com/)永远不要错过新内容。

在本系列的前一篇文章中，我们看到了在 Adonis 中保持控制器小尺寸的各种方法，但是这些方法在以下方面对我们没有帮助:

```
const Post = use('App/Models/Post')

class PostsController {
    async index({ response, request }) {    
        const query = Post.query()

        if (request.input('category_id')) {
            query.where('category_id', request.input('category_id'))
        }

        let keyword = request.input('keyword')

        if (keyword) {
            keyword = `%${decodeURIComponent(keyword)}%`
            query
                .where('title', 'like', keyword)
                .orWhere('description', 'like', keyword)
        }

        const tags = request.input('tags')
        if (tags) {
            query.whereIn('tags', tags)
        }

        const posts = await query.where('active', true).fetch()

        return response.json({ posts: posts.toJSON() })
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，让我们深入探讨各种方法，我们可以清理这一点。

## 范围

Adonis 有一个名为[查询范围](https://adonisjs.com/docs/4.1/lucid#_query_scopes)的特性，允许我们提取查询约束。让我们用关键字约束来尝试一下。

```
keyword = `%${decodeURIComponent(keyword)}%`
query
    .where('title', 'like', keyword)
    .orWhere('description', 'like', keyword) 
```

Enter fullscreen mode Exit fullscreen mode

为了创建一个新的作用域，我们将进入我们的`Posts`模型，并将下面的方法添加到类

```
static scopeByEncodedKeyword(query, keyword) {
    keyword = `%${decodeURIComponent(keyword)}%`

    return query
        .where('title', 'like', keyword)
        .orWhere('description', 'like', keyword)
} 
```

Enter fullscreen mode Exit fullscreen mode

现在回到控制器，我们可以简单地写

```
if (keyword) {
    query.byEncodedKeyword(keyword)
} 
```

Enter fullscreen mode Exit fullscreen mode

方法名以`scope`为前缀是很重要的。调用 scopes 时，放下`scope`关键字，调用 camelCase 中的方法(`ByEncodedKeyword` = > `byEncodedKeyword`)。

这是简化查询和隐藏复杂性的好方法！它还使得查询约束可重用。

* * *

让我们来谈谈这些条件句...

事实上，我创造了两个特性来克服所有这些条件。如果你对 traits 不熟悉，请在资源库中查看如何设置它们。

## [可选](https://github.com/MZanggl/adonis-lucid-optional-queries)

知识库:[https://github.com/MZanggl/adonis-lucid-optional-queries](https://github.com/MZanggl/adonis-lucid-optional-queries)

使用 Optional，我们将能够把`index`方法变成

```
async index({ response, request }) {    
    const posts = await Post.query()
        .optional(query => query
            .where('category_id', request.input('category_id'))
            .byEncodedKeyword(request.input('keyword'))
            .whereIn('tags', request.input('tags'))
        )
        .where('active', true)
        .fetch()

    return response.json({ posts: posts.toJSON() })
} 
```

Enter fullscreen mode Exit fullscreen mode

通过将可选查询包装在高阶函数`optional`中，我们能够消除整个控制器中的所有条件。高阶函数将查询对象捕获在 ES6 代理中，该代理检查传递的参数是否真实。只有这样，它才会将约束添加到查询中。

* * *

## [当](https://github.com/MZanggl/adonis-lucid-when)

知识库:[https://github.com/MZanggl/adonis-lucid-when](https://github.com/MZanggl/adonis-lucid-when)

我写的第二个特征实现了 Laravel 的`when`方法作为特征。`Optional`的缺点是您只能检查真值，有时您可能还想在应用约束之前检查输入是否是某个值。有了`when`，我们可以把搜索方法变成

```
async index({ response, request }) {    
    const posts = await Post.query()
        .when(request.input('category_id'), (q, value) => q.where('category_id', value))
        .when(request.input('keyword'), (q, value) => q.byEncodedKeyword(value))
        .when(request.input('sort') === 1, q => q.orderBy('id', 'DESC'))
        .where('active', true)
        .fetch()

        return response.json({ posts: posts.toJSON() })
    } 
```

Enter fullscreen mode Exit fullscreen mode

`When`的工作方式类似于`Optional`,因为它只在第一个参数为真时应用回调。如果第一个参数不正确，您甚至可以添加第三个参数来应用默认值。

* * *

当然我们也可以结合这两种特质

```
async index({ response, request }) {    
    const posts = await Post.query()
        .optional(query => query
            .where('category_id', request.input('category_id'))
            .byEncodedKeyword(request.input('keyword'))
            .whereIn('tags', request.input('tags'))
        )
        .when(request.input('sort') === 1, q => q.orderBy('id', 'DESC'))
        .where('active', true)
        .fetch()

    return response.json({ posts: posts.toJSON() })
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

更好的方法是使用过滤器。检查[这个模块](https://www.npmjs.com/package/adonis-lucid-filter)。

我们可以把我们的控制器变成

```
const Post = use('App/Models/Post')

class PostsController {
    async index({ response, request }) {
        const posts = await Post.query()
            .filter(request.all())
            .fetch()

        return response.json({ posts: posts.toJSON() })
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这样做的好处是，它消除了控制器的所有约束，但缺点是，如果不仔细查看您创建的所有过滤器，就不能 100%清楚发生了什么。

# 结论

总有不止一种方法可以解决问题，我们也可以将查询和条件提取到一个单独的类中，专门用于搜索这个表(类似于存储库模式，但用于搜索)。

我希望这篇文章能给你一些如何清理搜索查询的想法。

* * *

如果这篇文章对你有所帮助，我有更多关于简化编写软件的技巧[在这里](https://michaelzanggl.gumroad.com/l/intent-driven-development)。
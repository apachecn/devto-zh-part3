# 避免 AdonisJs 中的胖控制器

> 原文：<https://dev.to/michi/avoiding-fat-controllers-in-adonisjs-47oc>

> 最初发布于[michaelzanggl.com](https://michaelzanggl.com/articles/avoiding-fat-controllers-in-adonisjs)。订阅[我的简讯](https://michaelzanggl.com/)永远不要错过新内容。

今天我们将看看控制器，它们是如何变成大量代码垃圾的，以及我们如何避免这种情况。

例如，我们有一个创建文章的 API 端点。

这是路线:

```
// routes.js

Route.group(() => {
  Route.post("store", "ArticleController.store").middleware("auth");
}).prefix("article"); 
```

Enter fullscreen mode Exit fullscreen mode

起初，我们的控制器看起来或多或少不错。

```
'use strict'

const Article = use('App/Models/Article')

class ArticleController {
    async store({ params, auth, request }) {
        const article = await Article.create({
            title: request.input('title'),
            description: request.input('description'),
            user_id: auth.user.id,
        })

        return response.json({
            article: article.toJSON()
        })
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

但是现在我们有了额外的要求。文章有标签，我们必须将它们保存在`ArticleTag`表中。我们很快实现了它，但随后意识到我们还必须确保标题和描述确实被填充了。所以我们实现了验证。既然不多，我们干脆把所有代码都加到控制器里。几天后，我们收到要求，我们应该发送一封邮件给所有的追随者，也需要一个密码验证。还是那句话，不多，我们就把它加到控制器里吧！

这已经很多了。考虑到所有东西都在这一个控制器方法中，这一点尤其重要。它看起来会像这样:

```
'use strict'

/** @type {import('@adonisjs/framework/src/Hash')} */
const Hash = use('Hash')
const Article = use('App/Models/Article')
const ArticleTag = use('App/Models/ArticleTag')

class ArticleController {
    async store({ params, auth, request }) {
        // validation rules
        const rules = {
            title: 'required',
            description: 'required',
        }
        const validation = await validate(request.all(), rules)

        if (validation.fails()) {
            return response.status(400).json({
                message: validation.messages()[0].messsage,
            })
        }

        // verify password
        if (!(await Hash.verify(request.input('password'), auth.user.password))) {
            return response.status(400).json({
                message: 'The entered password is not correct',
            })
        }

        // actual work
        const article = await Article.create({
            title: request.input('title'),
            description: request.input('description'),
            user_id: auth.user.id,
        })

        const tags = JSON.parse(request.input('tags'))
        const articleTags = tags.map(tagId => {
            article_id: article.id,
            tag_id: tagId
        })

        await ArticleTag.createMany(articleTags)

        // some afterwork
        await this.sendMailToFollowers(article)

        return response.json({
            article: article.toJSON()
        })
    }

    sendMailToFollowers(article) {
        // some big private method
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

代码太多了！控制器目前只有一种方法。让我们看看阿多尼斯是如何帮助我们收拾残局的。

## 验证

我们想看的第一段代码是验证部分。

```
// validation rules
const rules = {
  title: "required",
  description: "required"
};
const validation = await validate(request.all(), rules);

if (validation.fails()) {
  return response.status(400).json({
    message: validation.messages()[0].messsage
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

这可以通过将验证放入它自己的[验证器](https://adonisjs.com/docs/4.1/validator)中来 100%提取。Adonis 提供了以下命令来创建这样的验证器。

```
adonis make:validator StoreArticle 
```

Enter fullscreen mode Exit fullscreen mode

这是实现:

```
"use strict";

class StoreArticle {
  get rules() {
    return {
      title: "required",
      description: "required"
    };
  }

  async fails(errorMessages) {
    return this.ctx.response.status(400).json({
      message: errorMessages[0].message
    });
  }
}

module.exports = StoreArticle; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们只需要将验证器添加到现有的路由中。

```
// routes.js

Route.group(() => {
  Route.post("store", "ArticleController.store")
    .middleware("auth")
    .validator("StoreArticle");
}).prefix("article"); 
```

Enter fullscreen mode Exit fullscreen mode

由于`fails`部分总是相同的，一旦我们有了不止一个验证器，我们就可以创建一个`BaseValidator`类，并且总是从它开始扩展。

好的，已经有一个了，但是还有很大的改进空间。

接下来，我们来看看密码验证。很可能我们不止在一个地方需要它。把这个分开也很好，如果规格改变的话可以很容易的移除。把它放在控制器里感觉不合适。放置它的一个好地方是在中间件内部。

## 中间件

我们想去掉的部分是这个。

```
if (!(await Hash.verify(request.input("password"), auth.user.password))) {
  return response.status(400).json({
    message: "The entered password is not correct"
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

所以让我们为它创建一个中间件。

```
adonis make:middleware VerifyPassword 
```

Enter fullscreen mode Exit fullscreen mode

下面是实现。

```
"use strict";
/** @type {import('@adonisjs/framework/src/Hash')} */
const Hash = use("Hash");

class VerifyPassword {
  async handle({ request, auth, response }, next, properties) {
    if (!(await Hash.verify(request.input("password"), auth.user.password))) {
      return response.status(400).json({
        message: "The entered password is not correct"
      });
    }

    await next();
  }
}

module.exports = VerifyPassword; 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们将它添加到`start/kernel.js`中命名的中间件中。

```
const namedMiddleware = {
  // ...
  verifyPassword: "App/Middleware/VerifyPassword"
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在剩下的就是将中间件添加到路由中。

```
// routes.js

Route.group(() => {
  Route.post("store", "ArticleController.store")
    .middleware(["auth", "verifyPassword"])
    .validator("StoreArticle");
}).prefix("article"); 
```

Enter fullscreen mode Exit fullscreen mode

这也可以通过[扩展验证器](https://adonisjs.com/docs/4.1/validator#_extending_validator)并向`StoreArticle.js`验证器添加另一个验证规则来解决。

## 事件

如果操作不需要立即执行，我们可以使用[事件](https://adonisjs.com/docs/4.1/events%5E)异步执行。这非常适合发送邮件之类的事情。

这一行代码正是这种情况。

```
await this.sendMailToFollowers(article) 
```

Enter fullscreen mode Exit fullscreen mode

首先让我们创建一个事件监听器:

```
adonis make:listener Article 
```

Enter fullscreen mode Exit fullscreen mode

这将创建`App/Listeners/Article.js`，下面是它的实现:

```
"use strict";

const Article = (exports = module.exports = {});
const Mail = use("Mail");

Article.registered = async article => {
  console.log('mail implementation')
}; 
```

Enter fullscreen mode Exit fullscreen mode

回到`ArticleController.js`让我们将这一行添加到顶部:

```
const Event = use("Event"); 
```

Enter fullscreen mode Exit fullscreen mode

现在剩下的就是关掉

```
await this.sendMailToFollowers(article) 
```

Enter fullscreen mode Exit fullscreen mode

用这一行:

```
Event.fire("new::article", article) 
```

Enter fullscreen mode Exit fullscreen mode

* * *

我们的控制器归结为这一点。

```
'use strict'

const Event = use("Event");
const Article = use('App/Models/Article')
const ArticleTag = use('App/Models/ArticleTag')

class ArticleController {
    async store({ params, auth, request }) {
        const article = await Article.create({
            title: "request.input('title'),"
            description: "request.input('description'),"
            user_id: auth.user.id,
        })

        const tags = JSON.parse(request.input('tags'))
        const articleTags = tags.map(tagId => {
            article_id: article.id,
            tag_id: tagId
        })

        await ArticleTag.createMany(articleTags)

        Event.fire("new::article", article)

        return response.json({
            article: article.toJSON()
        })
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

但是我们可以做得更好。现在，我们只能在通过这个控制器时创建一个文章。如果我们需要能够在其他地方创建文章，例如命令，或者只是想让我们的代码更易测试，我们可以将业务逻辑转移到服务中。

## 服务

让我们检查一下实现，没有创建服务的命令。

```
// app/Services/ArticleService.js

'use strict'

const Article = use('App/Models/Article')
const ArticleTag = use('App/Models/ArticleTag')

class ArticleService {
    async store({ title, description, tags }, user) {
        const article = await Article.create({
            title,
            description,
            user_id: user.id,
        })

        const articleTags = tags.map(tagId => {
            article_id: article.id,
            tag_id: tagId
        })

        await ArticleTag.createMany(articleTags)

        return article
    }
}

module.exports = ArticleService 
```

Enter fullscreen mode Exit fullscreen mode

我们现在的控制器就是

```
'use strict'

const Event = use('Event')
const ArticleService = use('App/Services/ArticleService')

class ArticleController {
    constructor() {
        this.articleService = new ArticleService
    }

    async store({ params, auth, request }) {
        const article = await this.articleService.store(request.all(), auth.user)

        Event.fire("new::article", article);

        return response.json({
            article: article.toJSON()
        })
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 没有自定义动作

到目前为止，我们只看了重构控制器中的一个方法。你仍然可以得到相当大的控制器。如果你的控制器有太多的方法，你可以开始把方法分成更多的控制器。怎么会？通过保持控制器`cruddy`。你可以用下面的命令在 Adonis 中创建一个足智多谋的控制器:

```
adonis make:controller YourController --resource 
```

Enter fullscreen mode Exit fullscreen mode

这样，控制器就有了七个默认的 crud 动作。如果你需要一个自定义动作，把它做成`cruddy`并放到一个新的控制器中。我这么说到底是什么意思，你如何才能做到这一点？
嗯，实际上有一个完整的关于这个的演讲，你可以在这里找到。

## 结论

太好了！每个部分现在都在适当的位置，易于测试和重用。控制器简单地调用每个部分。我们甚至能够将`context`(请求、验证和响应)从业务逻辑中分离出来，从而降低代码与框架的耦合度。

请注意，这些重构都不是绝对必要的。一开始控制器有点混乱是正常的，因为你可能对你试图解决的整个问题没有一个清晰的了解。

但是我们还没有被保护起来！看一下下面的控制器，看看我们将在以后的文章中重构什么！

```
const Post = use('App/Models/Post')

class PostsController {
    async search({ response, request }) {    
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

        const posts = await query.fetch()

        return response.json({ posts: posts.toJSON() })
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

如果这篇文章对你有所帮助，我有更多关于简化编写软件的技巧[在这里](https://michaelzanggl.gumroad.com/l/intent-driven-development)。
# 如何在 Nuxt.js 中设置动态路由

> 原文：<https://dev.to/vstothard/how-to-setup-dynamic-routes-in-nuxt-js-bcp>

当在 Nuxt 中使用[动态页面](https://nuxtjs.org/guide/routing/)时，例如当我们有一个博客文章的 CMS 时，我们很快发现我们需要一种方法来为每个文章生成路由，例如`www.veritystothard.com/blog/a-post/`。

我在使用 Contentful 建立自己的网站时遇到了这个问题。我的动态页面在第一次加载时运行良好；在`<nuxt-link />`中传递的 post slug 允许我使用 asyncData 方法预先呈现所需的 post，对 Contentful 的调用返回了一个类型为`blogPost`的条目，其 slug 与 URL 中的匹配。然而，当我刷新页面时，它却不见了。

出现这个问题是因为我没有在配置文件中定义我需要 Nuxt 为我的动态页面生成的路由，所以当`nuxt generate`运行时，没有创建路由。

这个问题的快速解决方法是简单地将每个帖子的路线添加到`nuxt.config.js`文件
的`routes`数组中

```
 export default {
    ...
      generate: {
          routes: [
              '/blog/a-post',
              '/blog/another-one'
          ]
      }
    ...
    } 
```

但是，当将来增加新职位时，这种方法很快就会变得乏味和低效。

更好的方法是根据您的输入生成一组路由。对于这个例子，我将使用从 Contentful 中提取的类型为`blogPost`的条目，但是这个概念可以应用于各种 CMS /数据源。

首先，我设置我的客户机并调用 Contentful 返回所有类型为`blogPost`的条目。然后，我将每个 slug 格式化以适应我的网站的文件夹结构，并将其推送到`routes`数组中(例如，slug `a-post`变成了`/blog/a-post`)。

```
 require('dotenv').config()

    const contentful = require('contentful')
    const config = {
      space: process.env.CONTENTFUL_SPACE_ID,
      accessToken: process.env.CONTENTFUL_ACCESS_TOKEN
    }
    const client = contentful.createClient(config)

    export default {
        generate: {
          routes: async function () {
            const entries = await client.getEntries({ content_type: "blogPost" });
            const routes = []
            entries.items.forEach(item => {
              routes.push(`blog/${item.fields.slug}`)
            })
            return routes
          }
       }
    } 
```

为了测试我的解决方案，我运行了`yarn generate`，发现控制台中记录了我的每个页面*和*帖子的路径。然后，我将我的更改部署到我的 Netlify 项目中，发现在页面刷新时，我的内容仍然存在🙌

*本帖最初发表于[veritystothard.com](http://www.veritystothard.com/blog/nuxt-dynamic-routes)T3】*
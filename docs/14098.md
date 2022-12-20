# 将 bettercms 集成到您的 vue 应用程序中

> 原文：<https://dev.to/easyaspython/integrating-buttercms-in-your-vue-application-2ihb>

这个帖子最初出现在 [dane.engineering](https://dane.engineering/post/buttercms-in-vue/) 上。

* * *

虽然像 Medium 这样的网站提供了受众范围和一致的体验，但我对它不再满意的原因越来越多。我最近的大部分写作都是在 dev.to 上完成的，这是一个令人耳目一新的变化！

我决定在 headless CMS 中进行我的处女航，目前我选择了 ButterCMS。它小巧而专注的 API 足以让我的博客正常运行！最棒的是，非商业使用是免费的。

在你注册了 Butter 之后，你会得到一个 API 密匙和一系列令人印象深刻的关于如何设置的框架特定指令的链接。我发现 Vue 的说明非常容易理解，从注册到检索测试博文只需 15 分钟左右！我在我的应用程序中使用了单文件组件(sfc ),所以我从抓取一个博客帖子列表并一起创建模板开始。Butter 返回一个包含帖子列表的`data`属性，每个帖子都有标题、slug、body 等等。我们可以在创建`BlogPostList`组件时获取这些数据，这样数据在它挂载时就可用了。我还为 API 响应花费大量时间的罕见情况设置了加载状态:

```
<template>
  <main>
    <h1>Posts</h1>
    <span v-if="loading">Loading...</span>
    <ul v-else-if="posts.data.length">
      <li v-for="post in posts.data" :key="post.slug">
        <router-link :to="{ name: 'post', params: { slug: post.slug } }">
          {{ post.title }}
        </router-link>
      </li>
    </ul>
  </main>
</template>

<script>
import Butter from 'buttercms'

const butter = Butter('YOUR API KEY')

export default {
  name: 'BlogPostList',
  data () {
    return {
      loading: true,
      posts: {},
    }
  },
  methods: {
    fetchPosts () {
      butter.post.list({ page: 1, pageSize: 10 })
        .then((response) => {
          this.loading = false
          this.posts = response.data
        }).catch((response) => {
          console.log(response)
        })
    },
  },
  created () {
    this.fetchPosts()
  },
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

显示一篇文章的代码基本相同——`data`属性包含文章的信息，我们可以在创建组件时获取文章:

```
<template>
  <main>
    <article v-if="post.data">
      <h1>{{ post.data.title }}</h1>
      <span class="metadata">{{ publishedDate }}</span>
      <div v-html="post.data.body" />
    </article>
  </main>
</template>

<script>
import Butter from 'buttercms'

const butter = Butter('YOUR API KEY')

export default {
  name: 'BlogPost',
  data () {
    return {
      loading: true,
      post: {},
    }
  },
  methods: {
    fetchPost () {
      butter.post.retrieve(this.$route.params.slug)
        .then((response) => {
          this.loading = false
          this.post = response.data
        }).catch((response) => {
          console.log(response)
        })
    },
  },
  created () {
    this.fetchPost()
  },
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

这就是开始获取帖子列表并呈现每个帖子所需的全部内容！剩下的工作就是把所有的搜索引擎优化放到适当的位置(我用 [`vue-meta`](https://github.com/nuxt/vue-meta) 来做这个)并按照你的意愿设计内容。为了让黄油在我的 Vue 应用中更容易使用，我做了一件额外的事情，就是把它添加到 Vue 原型中:

```
import Butter from 'buttercms'

const butter = Butter('YOUR API KEY')
Vue.prototype.$butter = butter 
```

Enter fullscreen mode Exit fullscreen mode

然后我可以在组件的方法中输入`this.$butter`,而不是每次都初始化 API 客户端。

* * *

到目前为止，我喜欢黄油的特点是:

*   易于使用的 API 和相应的 JavaScript 客户端
*   开始使用的少量时间
*   他们提供的图像 API(通过文件堆栈)

可能需要一些工作的功能有:

*   在你的文章中添加代码块:这比 Medium 好得多，但是没有其他更基本的东西那样好
*   术语:这不是黄油独有的；CMS 可能很复杂，因此很难区分您可以配置、创建和定制的所有概念
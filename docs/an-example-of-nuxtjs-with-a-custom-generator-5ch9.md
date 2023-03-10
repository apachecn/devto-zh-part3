# 带有定制生成器的 Nuxt.js 示例

> 原文：<https://dev.to/raymondcamden/an-example-of-nuxtjs-with-a-custom-generator-5ch9>

上周，我有幸在 [DevNexus](https://devnexus.com/) 上就多个 Vue.js 主题发表了演讲，其中之一就是 [Nuxt.js 框架](https://nuxtjs.org/)。几个月前，我曾经看过 Nuxt，并认为我不喜欢它。我遇到的主要问题是文档问题——老实说——只是一种直觉。当有人问我是否可以做一个 Nuxt 演示来代替一个不得不取消的演讲者时，我很高兴有机会再看一眼 Nuxt。最后，我带着完全不同的观点离开了。我仍然认为文档需要一点调整(我将在这篇文章中提到一个问题)，但总的来说，我印象相当深刻。Nuxt 增加了多个快捷方式，让你按照惯例跳过许多样板代码。这真的是一个很好的框架，我希望今年能更多地使用它，并在上面写博客。

好了，既然介绍已经结束了，让我进入我想讲的内容。Nuxt 作为一个通用(服务器端渲染)应用程序工作得最好，但是它也支持静态输出。当创建静态输出时，它既可以创建一组“传统的”HTML 文件等，也可以创建一个 SPA(单页应用程序)。到目前为止一切顺利。

我非常喜欢的 Nuxt 的一个特性是在你的 Vue 应用程序中创建路线的简单方法。想要`/cat`工作？只需添加`cat.vue`就可以了。Nuxt 也很容易支持动态路由。假设`cat.vue`返回了一个猫的列表，您可以通过添加`cats/_id.vue`来创建一个动态路由，其中下划线代表一个动态值。(你可以在这里阅读更多关于这个功能[。再说一次，到目前为止一切顺利。](https://nuxtjs.org/guide/routing#dynamic-routes)

但是，当您生成 Nuxt 应用程序的静态版本时，会发生什么呢？涵盖[静态生成](https://nuxtjs.org/guide/commands)的文档是这样说的:

> 如果您有一个包含动态路由的项目，那么查看一下 generate 配置，告诉 Nuxt.js 如何生成这些动态路由。

这将引导您进入[生成配置](https://nuxtjs.org/api/configuration-generate)文档，然后它会说:

> generate 命令会忽略动态路由。

真扫兴。但是，Nuxt 支持在生成配置中使用函数返回路径的能力。这允许您添加任何您想要的逻辑。让我们考虑一个这样的例子。我构建了一个简单的 Nuxt 应用程序，有两条路线。主页从星球大战 API 检索电影列表:

```
<template>
  <section class="container">
    <div>

        <h1>Films</h1>

        <b-list-group>
            <b-list-group-item v-for="film in films" :key="film.episode_id" 
            :to="'film/'+film.properId">{{film.title}}</b-list-group-item>
        </b-list-group>

    </div>
  </section>
</template>

<script>

export default {
    data() {
        return {
        }
    },
    methods: {
    },
    async asyncData(context) {
        let { data } = await context.$axios.get('https://swapi.co/api/films/');
        // add a proper id
        data.results.forEach(d => {
            d.properId = d.url.substring(0,d.url.length-1).split('/').pop();
        });
        return { films: data.results }
    }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

在很大程度上，我认为这是相当不言自明的(但像往常一样，*请*问如果不是！)，唯一的例外是`forEach`,在这里我抓取了用于获取电影特定信息的 URL 值的末尾。(《星球大战》API“list”命令实际上返回完整的数据，所以这不是最好的例子，但我们现在忽略它。)

然后我创建了`film\_id.vue`来处理细节的显示:

```
<template>
    <div>

        <h1>{{film.title}}</h1>

        <p>
            <pre>
{{film.opening_crawl}}
            </pre>
        </p>

        <p>
        <b-button to="/" variant="success">Home</b-button>
        </p>

    </div>

</template>

<script>
export default {

    async asyncData(context) {
        let { data } = await context.$axios.get('https://swapi.co/api/films/'+context.route.params.id);
        return { film: data }
    }

}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

同样，我的假设是这已经足够简单，不需要任何额外的上下文，如果不需要，请告诉我。好的，理论上，如果我们生成这个 Nuxt 应用程序的静态版本，它将简单地忽略动态路由，只呈现第一页。对吗？

不对。

事实证明，Nuxt 似乎选择了动态路线，并在生成静态输出时使用“SPA”模式。还记得我说过 Nuxt 可以以两种形式输出静态内容，一种是更“传统”的每条路线的页面，另一种是 SPA。这里的文档有点误导(我已经提交了一份错误报告)，因为它看起来工作得很好。你可以在这里看到这个直播:[https://deserted-squirrel.surge.sh/](https://deserted-squirrel.surge.sh/)

好吧，但是如果我想要“非”SPA 版本，并且想测试自定义生成支持呢？让我们看看它是什么样子的！这是我加在`nuxt.config.js` :
上的

```
generate: {
    routes:function() {
        console.log('doing my generate like a pro');
        return axios.get('https://swapi.co/api/films/')
        .then((res) => {
            return res.data.results.map((film) => {
                let properId = film.url.substring(0,film.url.length-1).split('/').pop();
                return '/film/'+properId;
            })
        });

    }
} 
```

Enter fullscreen mode Exit fullscreen mode

是的，就是这样。请注意，这些`console.log`消息*会执行*操作，并显示在您的终端上，这对调试非常有帮助。这创建了一个名为`film`的目录(它很聪明，在它不存在的时候创建了它)，然后为每个 id 创建了一个文件夹，文件夹下有一个`index.html`文件。

很简单！有了这种支持，您实际上可以变得更加复杂，我鼓励您查看[文档](https://nuxtjs.org/api/configuration-generate)以获取更多信息。最后，你可以在这里查看这个版本:[http://typical-jump . surge . sh](http://typical-jump.surge.sh)

有什么问题吗？下面给我留个评论吧！
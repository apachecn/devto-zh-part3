# 用 Go 和 TailwindCSS 构建我的个人网站

> 原文：<https://dev.to/restoreddev/building-my-personal-site-with-go-and-tailwindcss-5bhn>

最近发现域名 [andrewdavis.me](https://andrewdavis.me) 可以购买。我想过去买，但是已经有人买了。我很快就买下了这个域名，但是我必须决定在上面放什么。我下定决心建立一个个人网站，展示我的工作和写作。然而，我想把它作为一个学习的机会，用一些非传统的方式建立网站。构建个人网站的两个最流行的解决方案是 WordPress 或静态网站生成器。我想在中间的东西，一个有帖子写作后端和一个很少依赖的自定义前端的网站。我用 [Go](https://golang.org/) 和几个前端包建立了这个网站，包括 [TailwindCSS](https://tailwindcss.com/) 。

## 后端在跟杜松子酒一起走

我决定用 Go 做后端。在过去几年中，Go 在服务器应用程序中变得非常流行，尤其是微服务。我选择了 [Gin](https://github.com/gin-gonic/gin) 作为 web 框架来帮助路由和提供模板。Gin 有一个类似 Sinatra 的语法，允许您将路由表示为 HTTP 方法和为路由调用的函数。

```
func main() {
    router := gin.Default()

    router.GET("/", func (c *gin.Context) {
      c.HTML(http.StatusOK, "index.html")
    })
} 
```

我还引入了用于数据库访问的 Gorm。Gorm 允许您将您的表编写为 Go 结构，它将从结构字段构建列。然后，您可以基于您的结构轻松地查询记录。

```
type Post struct {
    gorm.Model
    Name string
    Content string `gorm:"type:text;"`
}

var post Post
db.First(&post)

fmt.Println(post.Name) 
```

总而言之，我成功地使用 Go 构建了后端。我喜欢数据类型和编译检查带来的安全性。Go 的美妙之处在于，如果你能让代码编译，那么你就更有信心这个应用会成功。也很快。然而，大多数请求时间都花在了查询数据库上，所以实际的速度提升与 PHP 或 Ruby 并没有太大的区别。我不得不拼凑几个包来获得我需要的所有特性，但是还有其他的 Go 框架，比如 [Buffalo](https://gobuffalo.io) 提供了更像 Rails 的体验。

## 前端带尾翼 CSS、SimpleMDE、激励和棱镜

我使用 [TailwindCSS](https://tailwindcss.com/) 作为我的造型框架。对于我所有的个人项目，我都成了顺风的超级粉丝。适应一个实用框架需要一点时间，但是我喜欢它的灵活性和特性。它允许我建立一个独特的网站，没有太多的自定义 CSS。另外，它有一些很棒的默认配色方案和字体堆栈。

我在后端使用了 [SimpleMDE](https://simplemde.com/) 来给我一个 Markdown 编辑器。这是非常容易设置和工作没有任何问题。

同样在后端，我使用了[刺激](https://stimulusjs.org/)来帮助我建立更多的交互性。Stimulus 是一个优秀的服务器渲染 HTML 框架，当我需要一些 JavaScript [sprinkles](https://twitter.com/dhh/status/958442395317583872?lang=en) 时，它已经成为 jQuery 的替代品。与 jQuery 相比，Stimulus 易于学习，也更易于组织。

最后，我在前端使用了 [Prism](https://prismjs.com/) 来突出代码块。它也很容易设置和运行。它允许你选择你需要的语言来突出显示，并给你选择一个主题的选项(明天晚上的胜利！).

## 结论

andrewdavis.me 现已上线运行，请查看并告诉我您的想法！虽然我的主要博客平台是 Dev.to，但我喜欢拥有自己的网站来展示我的技能和写作。我计划增加几页来展示我目前的项目和过去的工作。我认为个人网站是试验新模式和体验新语言的好方法。你的个人网站使用了什么语言和库？
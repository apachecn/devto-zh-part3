# 如何在 React Native 中检索 WordPress 帖子

> 原文：<https://dev.to/mrcflorian/how-to-retrieve-wordpress-posts-in-react-native-3fmd>

当我们完成功能齐全的 React Native WordPress 应用程序模板的工作时，我意识到许多 React Native 开发者将面临将 WordPress REST API 与 React Native 集成的任务，所以我决定发布我们的学习成果，以便帮助那些使用 WordPress 作为后端开发 React Native 应用程序的开发者。

正如你已经知道的，WordPress 自带了一个 REST API。如果你不相信我，就看看这个链接[https://www.instamobile.io/wp-json/wp/v2/posts](https://www.instamobile.io/wp-json/wp/v2/posts)。它返回一个分页的 JSON 响应，包含我博客中的所有文章。我发誓我只安装了 WordPress，没有做任何其他事情。尝试完全相同的网址，但你的博客，你会看到自己的文章。

现在，为了显示这些格式优美的帖子，React 原生新闻阅读器应用程序需要做的就是通过 HTTP 请求获取它们。幸运的是，在 React Native 中这是一个简单的任务，即使初学者也应该至少做过一次。在 React Native 中从 WordPress API 获取数据非常简单:

```
getPosts() {
    return fetch(
      "https://instamobile.io/wp-json/wp/v2/posts?page=" + this.state.page
    )
    .then(response => response.json())
    .then(responseJson => {
      this.setState(prevState => ({
        posts: [...prevState.posts, ...responseJson],
        isLoading: false
      }));
    })
    .catch(error => {
      console.error(error);
    });
 } 
```

Enter fullscreen mode Exit fullscreen mode

“fetch”方法向给定的 URL 发出网络请求(GET)。在我们的例子中，我们提供了我们的 WordPress 博客的 REST API URL(注意第三行的 instamobile.io URL)。一旦响应返回，我们将其解析为 JSON，然后在现有(已经显示的)文章的末尾添加帖子。实际上，这基本上是更新 React 组件的状态。

注意 WordPress REST API 是分页的，所以为了获取特定的页面，您需要在 GET 请求中提供一个额外的参数，名为“page”。我们将当前页面存储在 state 对象中，这样当用户向下滚动博客文章列表时，我们总是会获取一个新页面。

此外，在状态对象中，我们还存储了一个“isLoading”布尔变量，以通知呈现的组件是否有正在处理的获取请求。在我们的 React Native WordPress 应用程序中，我们在屏幕底部显示了一个微调器，让用户知道正在发生一些事情，他们应该等待新获取的文章。

事不宜迟，下面是如何在 React Native 中显示检索到的 WordPress 文章:

```
return (
  <FlatList
    data={this.state.posts}
    renderItem={({ item }) => (
      <News
        navigation={this.props.navigation}
        title={item.title.rendered}
        image={item.acf.author_photo}
        day={item.date}
        data={item}
      />
    )}
    keyExtractor={item => item.id.toString()}
    onEndReached={this.handleLoadMore}
    onEndReachedThreshold={1}
  />
); 
```

Enter fullscreen mode Exit fullscreen mode

希望这篇教程有用。祝你在用 WordPress REST API 后端制作自己的 React 原生应用时好运。原始文章最初发表在 [Instamobile](https://www.instamobile.io/react-native-tutorials/wordpress-rest-api-react-native-acf/) 博客上。
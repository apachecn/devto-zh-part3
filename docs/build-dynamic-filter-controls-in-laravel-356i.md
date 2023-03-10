# 在 Laravel 中构建动态过滤控件

> 原文：<https://dev.to/adi/build-dynamic-filter-controls-in-laravel-356i>

大家好，这是[阿迪](https://twitter.com/skadimoolam)。
我想和你分享我在 Laravel 中搜索、过滤&排序数据的简单解决方案。

[![The final widget](img/0bc9d2d4f68bf49e1368f4c8d39edc27.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--slnwA3vy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/ihlzgZD.png) 这是我们将要构建的图像。第一个输入是搜索，第二个输入是选择排序的列，第三个是选择如何排序，最后我们有一个下拉菜单来选择我们希望在一个页面中有多少项。

> 这是我为我的副业-[LaravelCollections.com](https://laravelcollections.com/?utm_campaign=laravel-filter-bar-article)制作的一个小部件

你可以在 Github Gist 找到本教程的所有代码。

### TLDR；

我的解决方案非常简单，包括 UI 部分和控制逻辑。UI 部分是一个水平表单，用`GET`方法指向同一个页面，这样当点击过滤器按钮时，表单被重定向到当前页面，每个输入字段作为查询参数。

控制器中的逻辑也很简单。我们检查这些查询参数，并使用它们相应地从模型中过滤数据。

这是我的解决方案的要点。我将在下面的代码示例中进行更详细的介绍。

### 控制器

先说控制器。这里的逻辑非常简单。
[![Controller Logic](img/0f4d2c833607fb514c5af4aa35be28fa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--e5938v63--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/Urx4iY2.png) 
如您所知，我们用默认数据初始化了 4 个变量。然后我们检查请求是否有任何参数，如果有，我们将参数设置为变量的数据。

一旦我们完成了参数的检查和设置，我们就搜索、排序和分页用户模型(在这种情况下，它可以是任何模型)。然后我们将过滤后的数据和其他变量一起发送到`View`。

我将在下一节介绍用户模型的搜索方法。

### 模型

我们的搜索逻辑也很简单。搜索方法在用户模型类内部。
[![Model's search method](img/2d647eaba4edd98f4c0280bae8dbd91d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YycFiYSL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/g2Y8XiI.png) 
点击这里可以了解更多关于 Laravel `Model`示波器[。](https://laravel.com/docs/5.8/eloquent#query-scopes)

### 查看

像其他部分一样，使所有这些工作的视图也非常简单。
[![View markup](img/db781fa0510be5a4dfe15642094a60e8.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--HgqEYRQQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/YwxTQFb.png)

我们有一个包含 5 个元素的表单，第一个输入获取搜索查询，第二个选择要排序的列，第三个选择排序的顺序，第四个选择每页的记录，最后一个是启动表单请求的过滤器按钮。

### 结论

我希望你明白这个不太长的教程。这是我为一个简单的需求在几分钟内想出的简单解决方案。希望你在你的项目中使用它。如果你有这个代码的改进，请让我知道，我很乐意学习它们。

既然你是 Laravel 的开发者，我有东西给你看。
[【LaravelCollections.com】](https://laravelcollections.com/?utm_campaign=laravel-filter-bar-article)是一个我为 Laravel 开发者收集大量资源的网站，如果你感兴趣，可以去看看。
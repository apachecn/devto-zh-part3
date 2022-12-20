# 铁轨！我来了。(第一天)

> 原文：<https://dev.to/saral/rails-here-i-come-day-1-52l2>

终于，经过几个月的辛苦工作，我即将在 [edx](https://edx.org) 上完成我的第一部 MOOC - [CS50 的《计算机科学导论](https://www.edx.org/course/cs50s-introduction-computer-science-harvardx-cs50x)。这是一次很棒的学习经历，我经历过挫折，也有过在完成某项任务或问题集后无比喜悦的时刻。最后，作为这门课程的一部分，我正在写我的最后一段代码，老实说，我等不及要获得认证了。

**这让我想到了 Rails** 。我打算使用 rails 编写和部署我的应用程序。我听说过很多关于 rails 的事情，以及它是如何提高生产率的，以及 web 应用程序部署起来有多容易。另外，阅读由[本](https://dev.to/ben)撰写的[帖子](https://dev.to/ben/im-ben-and-i-am-a-rails-developer-1j67)帮助我对 Rails 有了更好的了解。虽然我并没有想象自己现在就开始制作一个又一个 web 应用，但我确实希望在不久的将来能够这样做。请注意，我也没有实践 ruby 的经验，但是我希望我使用 to C 的经验能够帮助我学习 ruby。

事不宜迟，让我进入我今天要做的事情:

1.  安装和设置导轨

    首先，我着手在我的系统上设置 Rails。为此，我按照 rails 网站上的[文档](https://guides.rubyonrails.org/getting_started.html)进行操作，很快我的系统上就有了 rails。

2.  创建我的第一个应用程序

    接下来，我尝试建立一个静态页面。从我早期的项目中，我对模型、视图、控制器(MVC)方法有了一个基本的工作思路，并且知道 Rails 有一个类似的开发方法是令人欣慰的。

    1.  创建第一批文件:

        我只需要在 Rails 的终端上写些东西，就可以在一个名为 blog 的文件夹中快速生成我需要的所有文件。新文件的文件名可以是任何要求，对我来说，在这种情况下，它是一个博客。

    2.  运行服务器:

        最后，我通过端口 3000 上的`rails server`运行本地服务器，这是 Rails 的默认端口。当我浏览到 localhost:3000 时，瞧，我已经让服务器启动并运行了。

        [![Rails server running](img/62dcc1d76a6772d34b53fd6cc8b4f824.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WPqrOoZm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://guides.rubyonrails.oimg/getting_started/rails_welcome.png)

3.  设置静态页面

    接下来，要建立一个静态页面，我至少需要一个控制器和一个视图。这里控制器的工作是接收应用程序的特定请求。视图向人类显示信息的目的。控制器是收集信息的地方，视图只是显示信息。此外还有路由，它决定哪个控制器接收哪个请求，而且通常每个控制器有多条路由。我在这里仅仅是转述一下文档(就目前而言)，但是因为有太多的基础知识需要解开，所以我想把它们写下来，以便我能更好地理解它们。

    要生成控制器及其相关视图，我们可以运行“bin/rails 生成控制器主索引”。

    1.  Html 文件

        在这种情况下，rails 生成一个控制器主目录，并在 views 文件夹中生成一个主文件夹。该命令还会生成许多其他文件，但是现在我们只关注这两个文件。生成的 html 文件和你正常的 html 文件有点不同，意味着文件的扩展名包含 erb。这意味着你可以将 ruby 命令嵌入到你的 html 文件中，给你更多的控制权。

        然后，我可以对位于我的视图>个人文件夹中的 index.html.erb 文件进行更改。

    2.  路线变更

        然后，我转到 config 文件夹中的 route.rb。在这里，我修改了代码以获取 home/index，并将根目录设置为 home#index。

        ```
        Rails.application.routes.draw do

            get 'home/index'

            root 'home#index'

        end 
        ```

本质上，我在这里所做的是请求我的路由从 home 获取索引，并将我的根目录设置为 homepage，在本例中是 index.html.erb。

现在，如果我刷新我的页面，我的主页就变成了我的索引页面。

对于我来说，有很多东西需要从 rails 中解开，但这是美好的一天。由于各种错误，我有时不得不排除故障，但我实现的大多数解决方案是通过快速搜索，很多时候我不知道自己在做什么。设置 rails 并运行我的第一个静态页面非常好。

小小的胜利！！！
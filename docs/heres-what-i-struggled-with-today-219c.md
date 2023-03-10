# 以下是我今天纠结的事情

> 原文：<https://dev.to/saral/heres-what-i-struggled-with-today-219c>

在我今天晚上跑步的时候，我碰巧听了一个关于 [codeNewbie](https://www.codenewbie.org/podcast/how-to-teach-yourself-computer-science) 的播客(我最近一直在做这个)。这一集的嘉宾是神奇的算法女士-

[![vaidehijoshi image](img/daa0c6794423f7b9f23ca0f6a2c5a2b3.png)](/vaidehijoshi)

## [瓦伊代希·乔希](/vaidehijoshi)

[Writing words, writing code. Sometimes doing both at once. Señiorita engineer at DEV.](/vaidehijoshi)

[![twitter logo](img/ecef78ee24c258a213354fc0e60fd71a.png)瓦伊德希](https://twitter.com/vaidehijoshi) [ ![github logo](img/7e90f0f60c25b501324445b96acd3de8.png)瓦伊德希](https://github.com/vaidehijoshi)[![external link icon](img/7ad9ad23055d49c106b927d92662ca16.png)http://www.vaidehi.com](http://www.vaidehi.com)

I thought this was a great episode, and given what Vaidehi has achieved. Something stood out for me during this podcast though. Towards the end of the podcast, Vaidehi talks about how everyone invariably is going to get stuck at some point and how important it is to find a way to get unstuck and being ok with it. This statement resonated with me even more so today, because today was one such day for this beginner. I tried my best to get stuck, however I was only partially succesful in accomplishing the task that had me stuck.

作为学习 Rails 项目的一部分，我一直在开发一个博客应用，在过去的六天里，进展相当顺利(除了第四天)。然而，今天是这个项目中最大的测试之一(至少目前是这样)。

### 那么任务是什么呢？

不知道能不能分解的足够贴切让大家理解。现在我正在写这篇文章，我脑子里的想法是“我是否明白这个任务是什么”，如果我明白了，我应该能够向自己解释清楚。所以，在这里，我将尽可能详细地解释给自己听。

这项任务由两部分组成

首先，我必须传递我的`session[: username]`，它是当前登录到我的新博客文章页面的用户的用户名。

# # # #我为什么要这么做？

我计划将这个用户名作为帖子的作者保存在帖子模型中。帖子模型是我的数据库，它将存储帖子的标题、帖子的全部文本以及提交帖子的人的用户名。这样，我可以跟踪和访问删除和编辑的职位，只有作者的职位。目前，因为我没有这个功能，任何登录的用户都可以查看、编辑和删除帖子。这并不理想。

我花了一段时间才明白，但最终，我做到了。第一部分完成了。

#### 我是怎么做到的？

```
<%= f.hidden_field :username, :value => @user.username %> 
```

我在表单中为用户名创建了一个隐藏字段，允许用户发表新帖子。在本例中是`_form.html.erb`，我传入了一个默认值。`Posts_Controller.rb`中的创建帖子方法是这样的

```
def create
        post = Post.new(post_params)
        if post.save
            redirect_to post_path(post)           
        else
            redirect_to new_post_path          
        end
    end

private 
        def post_params
            params.require(:post).permit(:username, :title , :body)
        end 
```

经过几个小时的调试后，这个任务被标记为完成，我准备处理另一个任务。我觉得精神焕发。

二。当我开始第二项任务时，我认为这是一个简单明了的方法，但是我大错特错了。当我写这篇文章的时候，我仍然没有找到解决的方法。我希望我写下这个问题会给我另一个角度来看待如何完成这个任务。

#### 任务是什么

该任务要求我显示三个链接，显示、编辑和删除。然而，如上所述，只有当当前登录的用户也是博客文章的作者时，编辑和删除链接才会出现。下面是我第一次走近它

```
<%= if @post.username == @user.username %>
            <td><%= link_to 'Edit', edit_post_path(post) %></td>
             <td><%= link_to 'Delete', destroy_post_path(post) %></td>

<% end %> 
```

我在这里的想法是，我将同时获得 [@post](https://dev.to/post) 。username 和@user.username 添加到位于 posts_controller 和 sessions_controller 的 views/posts 文件夹中的`index.html.erb`,就像我之前做的那样。然而，这种直截了当的方法行不通。我做了一个快速调试，看看我是否真的得到了我需要的值。于是，在索引页面，我做了一个改动:

```
<%= @user.username %> 
```

我这样做是为了看看我是否会得到任何回报，我得到的唯一回报是一个错误，它写着“nil:NilClass 的未定义方法`用户名'”。至少我现在知道了，我的方法没有返回任何东西，但是最大的问题是为什么？我的意思是，在我的表单中，当我想要获取会话用户名时，这一行代码似乎可以工作。我快速检查了一下索引文件和表单文件是否在同一个目录中。我推断，如果它们都在同一个目录中，就一定能正常工作，对吗？不对。它们在同一个目录中，但是它们不能工作。

所以我看着，但是当我看着的时候，我突然想到一件事，“我在找什么？”我这次搜索的关键词是什么？

我的方法之一是这样的

 ``def index
@post = Post.all
puts ">>>>#{Post.where(username:"saral")}"
end`  `在我的 post 控制器中，我试图定位用户名，并在我的控制台中打印出结果。然而，我得到的只是内存位置(我是猜测)。无论如何，今天，我卡住了。虽然卡住不是最大的感觉，但这绝对是一个学习的机会。也是写这篇文章的一个机会。``

 ``明天，我们再去。感谢任何帮助或指导，当然前提是我已经能够恰当地描述我的任务。

当我结束这篇文章时，我觉得我可能会再一次挖掘这个问题，但就在我面前挂着一句名言，上面写着“努力工作，保持耐心”``
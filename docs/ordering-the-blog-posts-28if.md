# 订购博客文章

> 原文：<https://dev.to/saral/ordering-the-blog-posts-28if>

现在我不得不说，我用 Rails 构建博客应用程序的第一个项目已经接近尾声。虽然有一些功能可以添加到网站上，但我对这个项目的热情正在消退。因此，我打算暂时搁置这个项目，从明天开始继续我的下一个项目构想。

然而，在进入下一个项目之前，我还想实现一件事。该功能通过在顶部显示新创建的文章来对博客文章进行排序。在破解了解决方案之后，这个解决方案看起来非常简单，一行修改后的代码就完成了任务。

```
 def index        
        @post = Post.all.order('created_at DESC')
   end 
```

在 post controllers 函数索引中，我需要做的就是添加`order`方法。order 方法将查看 created_at 列，对文章进行降序排序。

完成这些后，我还实现了一个特性，允许用户在创建新的或编辑博客文章时，在文本框内键入时按 tab 键。我用普通的 Javascript 破解了这个问题。

```
var myInput = document.getElementById("textbox");
    if(myInput.addEventListener ) {
        myInput.addEventListener('keydown',this.keyHandler,false);
    } else if(myInput.attachEvent ) {
        myInput.attachEvent('onkeydown',this.keyHandler); 
    }
    function keyHandler(e) {
        var TABKEY = 9;
        if(e.keyCode == TABKEY) {
            this.value += "    ";
            if(e.preventDefault) {
                e.preventDefault();
            }
            return false;
        }
    } 
```

在将 javascript 包含到 Rails 中时，我确实遇到了一段时间的困难，但最终还是成功了。

我在`app > javascripts`文件夹中包含了我创建的 js 文件`main.js`，然后在 app 文件夹中的`application.js`上，我添加了下面几行`//= require main`。我以为这样会奏效，但事实并非如此。我的浏览器控制台出现以下错误。

```
Uncaught TypeError: Cannot read property 'addEventListener' of null 
```

我不得不寻找一个解决方案，老实说，我没有做过很多 javascript，所以我不能真正跟上。我确实发现，javascript `addEventListener`甚至在我博客的正文文本框中出现任何文本之前就已经在运行了。

对于目前对我有效的解决方案，我复制粘贴了下面的代码

```
<%= javascript_include_tag 'application', 'data-turbolinks-track': 'reload' %> 
```

在`application.html.erb`文档的最后。

这样，我就可以让选项卡在我的文本框上工作了。我还不明白我对 javascript 的问题是什么？或者我是否将 js 文件正确地包含到了我的 rails 应用程序中？

总而言之，这对我来说是一次很好的学习经历。然而，我必须离开这个应用程序，重新开始。
# 分页完成

> 原文：<https://dev.to/saral/pagination-done-309l>

在几天急需的休息之后，是时候回去给我的博客项目添加功能了。今天的任务是给博客文章添加分页。为了让这个特性工作，我使用了 [will_paginate](https://rubygems.org/gems/will_paginate) gem。在 gem 文件上，我添加了`gem 'will_paginate', '~> 3.1', '>= 3.1.6'`，并运行了一个快速的`bundle install`来安装所需的 gem。

然后，就全是跟着创业板的[文档](https://github.com/mislav/will_paginate/wiki)走了。

首先，我为我的索引方法更新了 posts 控制器。

```
 def index
  @post = Post.all.order('created_at DESC').paginate(:page => params[:page], :per_page => 3)
   end 
```

正如你所看到的,`.paginate`已经被添加，并且我已经遵循了文档。另外，我设置了每页显示三篇文章。

完成后，页面会自动更新，在一页上只显示三篇文章。现在，我需要在视图本身中添加分页，以便用户可以在网站上导航。为此，我使用了

```
<%= will_paginate @post %> 
```

完成后，我就可以用导航菜单进行分页了。然而，我仍然需要给菜单添加一点风格，使它们看起来更好。为此，我可以使用 bootstrap，我已经在我的应用程序中使用了它。

为此，我通过`gem 'bootstrap-will_paginate', '~> 1.0'`使用了 gem[bootstrap _ will _ paginate](https://rubygems.org/gems/bootstrap-will_paginate)。我再次运行了 bundle install，最后在视图上，我按照 gem 的[文档](https://github.com/yrgoldteeth/bootstrap-will_paginate)添加了样式。

```
 <%= will_paginate(@post, :renderer =>
WillPaginate::ActionView::Bootstrap4LinkRenderer) %> 
```

分页现在可以使用了。
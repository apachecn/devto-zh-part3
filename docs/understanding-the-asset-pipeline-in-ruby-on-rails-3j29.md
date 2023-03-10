# 理解 Ruby on Rails 中的资产管道

> 原文：<https://dev.to/justalever/understanding-the-asset-pipeline-in-ruby-on-rails-3j29>

在 Ruby on Rails 社区中，资产管道已经改变了很多年的游戏规则。这篇文章和视频概述了什么是资产管道，如何使用它，以及这种工具的好处。

[https://www.youtube.com/embed/kgEcdBGpr54](https://www.youtube.com/embed/kgEcdBGpr54)

### 什么是资产管道？

Ruby on Rails 文档很好地解释了“什么”。

> 资产管道提供了一个框架来连接、缩小或压缩 JavaScript 和 CSS 资产。它还增加了用其他语言和预处理程序(如 CoffeeScript、Sass 和 ERB)编写这些资产的能力。它允许您的应用程序中的资产自动与来自其他 gem 的资产相结合。

一个全新的 rails 应用程序配备了`sprockets-rails` gem。这个宝石结合了其他三个宝石(`sass-rails`、`coffee-rails`和`uglifier`)的力量，使你的应用程序中的资产变得轻而易举。只要掌握一点知识，您就可以使用资产管道在给定的 Ruby on Rails 应用程序中添加`CSS`、添加`JavaScript`、参考图像等等。

#### Rails 6 即将到来怎么办？

诚然，Rails 6 已经非常接近发布了。它将以一种不同的方式将 JavaScript 添加到你的应用程序中，这种方式更加 node.js 驱动。这利用了`webpacker` gem 的力量，允许在您的 ruby on rails 应用程序中包含现代 JavaScript 和更多内容。这完全发生在资产管道之外。目前，建议继续使用来自新的 webpacker `app/javascript`目录的图像和 CSS/SCSS 以及服务器 JavaScript 的资产管道。

### 主要特征

使用资产管道的一个普遍好处是性能和速度。在这些广义收益中，我们得到了以下结果:

*   **文件串联**–提供更少的文件意味着浏览器需要发出更少的请求。
*   **SHA256 指纹**–用指纹自动缓存文件。仅更新需要的文件。
*   **缩小/压缩**–缩小意味着文件更小，每个请求需要的“字节”更少。

注意:大约在 20:08 左右，我尝试要求“log.js”。由于有了“require_tree”，你甚至不需要请求它。声明在同一个文件中。我还把“要求”拼错了。 [![🤦🏼‍♂️](img/47761291ebca6a34ef4659fa00fc08cb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CSeAO5ZK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s.w.oimg/core/emoji/11.2.0/72x72/1f926-1f3fc-200d-2642-fe0f.png) 我只是想搞清楚这一点！

### 用法

在一个全新的 Rails 应用程序中，您应该找到几个地方来存放您的资产。绑你 app 的主要是`app/assets`。在该文件夹中有一个`images`、`javascripts`和`stylesheets`目录。

#### 图像

图像相对来说是不言自明的，但是在视图或 CSS 文件中呈现它们时，需要记住一些约定。

在视图中包含图像是通过 rails 助手实现的

```
<%= image_tag "cat.jpg", alt: "Black Cat" %> 
```

Enter fullscreen mode Exit fullscreen mode

这里我们包括一个图像。`image_tag`助手希望图像位于应用程序中的`app/assets/images`。你可以在`images`中添加子文件夹，也可以这样引用图片。

```
<%= image_tag "marketing/animals/cat.jpg", alt: "black cat" %> 
```

Enter fullscreen mode Exit fullscreen mode

在你的`CSS`文件中添加背景图片也伴随着 gotchya。

```
body {
  background-image: url('/asseimg/cat.jpg');
} 
```

Enter fullscreen mode Exit fullscreen mode

您可能认为上面的代码可以工作，但不幸的是，它不能。相反，我们利用另一个助手来找到图像。

```
body {
  background-image: image-url('cat.jpg');
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意`image-url`助手以及我不需要声明任何相对路径的事实。相当酷！

#### 样式表

在您的`app/assets/stylesheets`目录中，您会看到一个`application.css`文件。这是你的应用 CSS 的真实来源，它不在`vendor`或`lib`目录中(你可以存放资产的另外两个地方)。任何时候你都可以将`application.css`改为`application.scss`，并获得用`SCSS`代替`CSS`的好处。我之前提到的宝石在这里发挥作用。

默认的`application.css`文件可能如下所示:

```
/* app/assets/stylesheets/application.css */

/*
 * This is a manifest file that'll be compiled into application.css, which will include all the files
 * listed below.
 *
 * Any CSS and SCSS file within this directory, lib/assets/stylesheets, or any plugin's
 * vendor/assets/stylesheets directory can be referenced here using a relative path.
 *
 * You're free to add application-wide styles to this file and they'll appear at the bottom of the
 * compiled file so the styles you add here take precedence over styles defined in any other CSS/SCSS
 * files in this directory. Styles in this file should be added after the last require_* statement.
 * It is generally better to create a new file per style scope.
 *
 *= require_tree .
 *= require_self
 */ 
```

Enter fullscreen mode Exit fullscreen mode

`*= require_tree .`语法可能看起来像注释，但它实际上负责呈现`app/assets/stylesheets`中的所有文件，并自动将它们包含在这里。这样，您就不必手动包含它们。`*= require_self`注释需要文件本身，这就是我们的主布局模板的最终内容。您可以添加任何您喜欢的 CSS 文件，如果它不在`app/assets/stylesheets`目录中，您可以在这里要求它。你可以从`vendor`目录中这样做，或者如果你偶然安装了一个 gem，它有一些你需要利用的相关风格。

链接到这些资产看起来像这样。

```
<!-- app/views/layouts/application.html.erb -->

<!DOCTYPE html>
<html>
  <head>
    Asset Pipeline
    <%= csrf_meta_tags %>
    <%= csp_meta_tag %>

    <%= stylesheet_link_tag 'application', media: 'all', 'data-turbolinks-track': 'reload' %>
    <%= javascript_include_tag 'application', 'data-turbolinks-track': 'reload' %>
  </head>

  <body>
    <div class="container">
      <%= yield %>
    </div>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

上面你会看到一些方便的 Rails 助手帮助我们，包括我们的`application.css`样式表和在`app/assets/javascripts`找到的`application.js` JavaScript 文件

注意，我们只渲染了`application.css`和`application.js`文件，其他什么都没有渲染。如果我在`app/assets/stylesheets`中创建新的`global.css`文件，它会自动包含在页面中。

#### JavaScript

像 CSS/SCSS 一样，同样的原理也适用于 JavaScript，但是我们使用了稍微不同的语法。

```
// app/assets/javascripts/application.js

// This is a manifest file that'll be compiled into application.js, which will include all the files
// listed below.
//
// Any JavaScript/Coffee file within this directory, lib/assets/javascripts, or any plugin's
// vendor/assets/javascripts directory can be referenced here using a relative path.
//
// It's not advisable to add code directly here, but if you do, it'll appear at the bottom of the
// compiled file. JavaScript code in this file should be added after the last require_* statement.
//
// Read Sprockets README (https://github.com/rails/sprockets#sprockets-directives) for details
// about supported directives.
//
//= require rails-ujs
//= require activestorage
//= require turbolinks
//= requre log
//= require_tree . 
```

Enter fullscreen mode Exit fullscreen mode

我作为例子补充的`//= require log`。我创建了一个名为`log.js`的新文件，并将其添加到`app/assets/javascripts`中。然后我在`application.js`中需要它，这样它会自动注入到我们的布局中。这在本地的好处不像在生产中那么大，在生产中所有的文件都被编译成一个文件。这个过程节省了文件大小、请求大小和速度，这是最重要的(加上它有多容易)。

### 配置和添加新的 JavaScript 和 CSS

假设您想在组合中添加新文件？在我的视频中，我为咧嘴笑添加了自举。这意味着将一个`bootstrap.css`文件和一个`bootstrap.js`文件添加到我们的`vendor`文件夹中，因为这是我们将利用但不会修改的代码。您将希望您的供应商文件夹有一个`assets`目录，并且在该目录中有一个`stylesheets`目录和`javascripts`目录。

该结构最终看起来像这样:

```
vendor
└── assets
    ├── javascripts
    │   └── bootstrap.js
    └── stylesheets
        └── bootstrap.css 
```

Enter fullscreen mode Exit fullscreen mode

仅仅将这些文件添加到`vendor`是不够的。我们需要将它们告诉我们的应用程序。

`sprockets-rails`宝石在`app/assets/config/`里有一个方便的`manifest.js`文件。该文件也有自己的方式来包含资产。我的文件最终如下:

```
// app/assets/config/manifest.js

//= link_tree ../images
//= link_directory ../javascripts .js
//= link_directory ../stylesheets .css

//= link bootstrap.js
//= link bootstrap.css 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，前三行是。我添加的底部两个将引用`vendor`中新添加的文件。请注意`link_tree`声明。这意味着文件夹及其子文件夹中的所有文件。`link_directory`声明仅指指定目录中的文件。

我们还没有 100%完成。应用程序不太了解这两个新增加的内容。让我们解决这个问题。

```
# config/initializers/assets.rb

# Precompile additional assets.
# application.js, application.css, and all non-JS/CSS in the app/assets
# folder are already added.
Rails.application.config.assets.precompile = ["manifest.js"] 
```

Enter fullscreen mode Exit fullscreen mode

上面我对我们的`config`目录中的一个`assets.rb`初始化文件进行了修改。最后一行设置了一个配置，在`app/assets/`中查找`manifest.js`文件，以查找要预编译哪些新资产用于应用程序中。保存后，如果 rails 服务器已经在运行，您将需要重启它。

最后一个难题是在我们的视图中包含这些新文件。我不希望这些文件被编译成`application.js`文件，尽管如果我们愿意的话，它们可以。相反，我把它们渲染成不同的文件，如下:

```
<!DOCTYPE html>
<html>
  <head>
    Pipeline
    <%= csrf_meta_tags %>
    <%= csp_meta_tag %>

    <%= javascript_include_tag 'https://code.jquery.com/jquery-3.3.1.slim.min.js' %>
    <%= javascript_include_tag 'https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.7/umd/popper.min.js' %>

    <%= stylesheet_link_tag 'bootstrap', media: 'all', 'data-turbolinks-track': 'reload' %>
    <%= javascript_include_tag 'bootstrap', 'data-turbolinks-track': 'reload' %>

    <%= javascript_include_tag 'bootstrap', media: 'all', 'data-turbolinks-track': 'reload' %>

    <%= stylesheet_link_tag 'application', media: 'all', 'data-turbolinks-track': 'reload' %>
    <%= javascript_include_tag 'application', 'data-turbolinks-track': 'reload' %>
  </head>

  <body>
    <div class="container">
      <%= yield %>
    </div>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

Bootstrap 附带了`jQuery`和`popper.js`作为依赖项，所以我也需要添加它们。我没有像 Bootstrap 那样包含它们，而是简单地链接到它们的 CDN 文件。不过，我不建议在生产中信任 cdn。

最后一次刷新应该会得到您的引导！

### 资产管道还相关吗？

我绝对这么认为。现在，随着 Rails 6 的出现，我们将开始看到 JavaScript 存在于其他地方，但图像、CSS、字体等可以很好地存在于资产管道中。它附带了一些需要学习的约定，但是在持续使用一段时间后，您将学会欣赏 Ruby on Rails 中资产管道背后的魔力。

### 不要脸的塞！

如果你喜欢这篇文章，我在 [YouTube](https://youtube.com/c/webcrunch) 和我的[博客](https://web-crunch.com)上有更多视频。希望收件箱中有更多这样的内容吗？[订阅我的简讯](https://web-crunch.com/subcribe)自动获取。

#### 查看我的课程

 [![https://i2.wp.com/i.imgur.com/KIaaJEB.jpg?ssl=1](img/083250001f26c8017cdaafe107a730f9.png)

T6】](https://hellorails.io)

[![☝](img/9bbc19a8f8b6c50ce408d0d17fdbbb56.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2LBBRZlr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s.w.oimg/core/emoji/11.2.0/72x72/261d.png) 想从头开始学习 Ruby on Rails？查看我即将推出的课程 [Hello Rails](https://hellorails.io) 。

理解 Ruby on Rails 中的资产管道的帖子最先出现在 T2 的 Web-Crunch 上。
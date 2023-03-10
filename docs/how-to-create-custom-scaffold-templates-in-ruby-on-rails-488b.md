# 如何在 Ruby on Rails 中创建定制的脚手架模板

> 原文：<https://dev.to/justalever/how-to-create-custom-scaffold-templates-in-ruby-on-rails-488b>

Ruby on Rails 就是这样一个强大的框架。它有自己的 CLI，其中利用了所谓的“生成器”。这些生成器只需几个按键，就可以快速增加应用程序的资源。在这篇文章中，学习如何在 Ruby on Rails 中创建定制的脚手架模板

[https://www.youtube.com/embed/BTW3MSkX6O0](https://www.youtube.com/embed/BTW3MSkX6O0)

### 约定让你走得更远

在我开发了相当多全新的 Ruby on Rails 应用程序之后，我总是发现搭建资源的默认外观有点暗淡。默认情况下，Rails 会生成大量与您在命令行上传递的给定模型相关联的文件和样式表。

示例:

```
$ rails generate scaffold Post title:string body:text 
```

Enter fullscreen mode Exit fullscreen mode

上面的命令将在给定的 rails 应用程序中围绕`Post`模型创建一个完整的类似 CRUD 的概念。为此，它生成了许多文件，包括从样式表到测试专用文件的所有内容。它还创建一个活动记录迁移文件，该文件稍后将被迁移到数据库中。这最终意味着一个名为`posts`的新表诞生了。

这个概念是一个巨大的时间节省。如果你只是在建立一个想法的原型或者测试一个理论，这一点尤其正确。

### 扩展约定

在典型的 scaffold 中，您会得到一个完整的视图文件夹，其中包含创建的 restful 路由的相关视图。

```
app/views/posts/_form.html.erb
app/views/posts/edit.html.erb
app/views/posts/index.html.erb
app/views/posts/new.html.erb
app/views/posts/show.html.erb 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们得到了让应用程序按预期工作所需的基本视图和逻辑。在经历了这么多的搭建之后，我开始意识到我讨厌默认的外观和感觉。这能改变吗？答案是肯定的！

### 自定义脚手架视图模板

在你的应用程序中，你会找到一个`lib`目录。默认情况下，它可能会有`assets`和`tasks`文件夹，但我们需要创建一个新的`templates`目录。

该结构的结尾如下:

```
lib/
-- templates
  -- erb
    -- scaffold
      --| _form.html.erb.tt
      --| edit.html.erb.tt
      --| index.html.erb.tt
      --| new.html.erb.tt
      --| show.html.erb.tt 
```

Enter fullscreen mode Exit fullscreen mode

扩展名为`.tt`的新文件可能看起来很新。那是因为他们是！这些是一种新类型视图，被归类为模板。当一个新的框架运行时，框架会找到这些，并使用它们来构建生成的新视图。

### 查看查看索引:

```
<!-- lib/templates/erb/scaffold/index.html.erb.tt -->

<div class="container mx-auto my-8 px-4">
  <div class="flex justify-between items-center mb-4">
    <h1 class="h2"><%= plural_table_name.titleize %></h1>

    <%% if @<%= plural_table_name %>.exists? %>
      <%%= link_to 'New <%= singular_table_name.titleize %>', new_<%= singular_route_name %>_path, class: "btn btn-default" %>
    <%% end %>
  </div>

  <%% if @<%= plural_table_name %>.exists? %>
    <div class="bg-white rounded shadow">
      <table class="w-full">
        <thead>
          <tr>
<% attributes.reject(&:password_digest?).each do |attribute| -%>
            <th class="p-3 uppercase text-left text-xs text-gray-700"><%= attribute.human_name %></th>
<% end %>
            <th class="p-3 uppercase text-left text-xs text-gray-700">Actions</th>
          </tr>
        </thead>

        <tbody>
        <%% @<%= plural_table_name %>.each do |<%= singular_table_name %>| %>
          <tr class="group border-t border-gray-400 hover:bg-gray-100">
            <% attributes.reject(&:password_digest?).each do |attribute| -%>
              <td class="p-3"><%%= <%= singular_table_name %>.<%= attribute.column_name %> %></td>
            <% end %>
            <td>
              <%%= link_to "View", <%= singular_table_name %>, class: "btn btn-default" %>
              <%%= link_to "Edit", edit_<%= singular_table_name %>_path(<%= singular_table_name %>), class: "btn btn-default" %>
            </td>
          </tr>
        <%% end %>
        </tbody>
      </table>
    </div>

  <%% else %>
    <div class="bg-white rounded shadow flex items-center justify-between p-8">
      <div class="flex-1 text-center">
        <p class="text-xl font-semibold mb-4">Create your first <%= singular_table_name.titleize %></p>
      <%%= link_to 'New <%= singular_table_name.titleize %>', new_<%= singular_route_name %>_path, class: "btn btn-default" %>
      </div>
    </div>
  <%% end %>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

一些需要注意的事情:

*   这里假设你使用我的[启动顺风](https://github.com/justalever/kickoff_tailwind)模板创建了一个新的 rails 应用。您看到的类属于 Tailwind CSS，以及一些在您使用模板创建新的 rails 应用程序时默认出现的自定义组件。我在这里谈论更多关于模板[的最新变化。](https://dev.to/justalever/latest-news-rails-6-jumpstart-pro-and-kickoff-updates-28g0)

*   `<%%`符号是新的，当一个脚手架在`erb`文件的上下文中运行时，它实质上充当一个模板预格式化程序。

*   在这些模板中，我们可以访问像`singular_table_name`或`singular_route_name`这样的动态局部变量和方法。这些将被你生成的任何资源所取代。在我们的例子中，应该是`post`。

### 编辑视图模板

```
<!-- lib/templates/erb/scaffold/edit.html.erb.tt -->

<div class="container mx-auto my-8 px-4">
  <div class="max-w-xl mx-auto">
    <h1 class="text-2xl font-bold mb-6">Editing <%= singular_table_name.titleize %></h1>

    <%%= render 'form', <%= singular_table_name %>: @<%= singular_table_name %> %>
  </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

### 新建视图模板

```
<!-- lib/templates/erb/scaffold/new.html.erb.tt -->

<div class="container mx-auto my-8 px-4">
  <div class="max-w-xl mx-auto">
    <h1 class="text-2xl font-bold mb-6">New <%= singular_table_name.titleize %></h1>

    <%%= render 'form', <%= singular_table_name %>: @<%= singular_table_name %> %>

    <%%= link_to 'Back', <%= index_helper %>_path, class: "link" %>
  </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

### 显示视图模板

```
<!-- lib/templates/erb/scaffold/show.html.erb.tt -->

<div class="container mx-auto my-8 px-4">
  <% attributes.reject(&:password_digest?).each do |attribute| -%>
  <p class="text-lg font-semibold"><%= attribute.human_name %>:</p>
  <p class="leading-normal"><%%= @<%= singular_table_name %>.<%= attribute.name %> %></p>

  <% end -%>
  <div class="flex items-center justify-start mt-6">
    <%%= link_to 'Edit', edit_<%= singular_table_name %>_path(@<%= singular_table_name %>), class: "btn btn-default mr-4" %>
    <%%= link_to 'Back', <%= index_helper %>_path, class: "btn btn-default" %>
  </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

### _ 表单视图模板

```
<!-- lib/templates/erb/scaffold/_form.html.erb.tt -->

<%%= form_with(model: <%= model_resource_name %>) do |form| %>
  <%%= render "error_messages", resource: form.object %>

<% attributes.each do |attribute| -%>
  <div class="mb-6">
<% if attribute.password_digest? -%>
    <%%= form.label :password, class: "label" %>
    <%%= form.password_field :password, class: "input" %>
  </div>

  <div class="mb-6">
    <%%= form.label :password_confirmation, class: "label" %>
    <%%= form.password_field :password_confirmation, class: "input" %>
<% else %>
    <%%= form.label :<%= attribute.column_name %>, class: "label" %>
    <%%= form.<%= attribute.field_type %> :<%= attribute.column_name %>, class: "input" %>
<% end %>
  </div>

<% end -%>
  <div class="mb-6 flex justify-between items-center">
    <%%= form.button class: "btn btn-default" %>

    <%% if form.object.persisted? %>
      <%%= link_to 'Delete', form.object, class: "btn btn-default", method: :delete, data: { remote: true, confirm: "Are you sure?" } %>
    <%% end %>
  </div>
<%% end %> 
```

Enter fullscreen mode Exit fullscreen mode

### 让魔法开始吧

有了这些模板集，您现在可以创建新的框架来利用它们，而不是 Rails 自带的默认模板。这里最棒的是，您可以根据自己的需要，使用模板变得疯狂或简单。在我自己的例子中，我可能会扩展我的 rails 应用程序模板 Kickstart，以便在将来的截屏/教程中使用它们。它节省了大量的时间和脑力，这是框架的要点。

我希望您发现这种技术很有用。我发现能够简单地添加一些模板并让应用程序/框架从那里接手真的很有力量。几乎没有配置，这是 Ruby on Rails 的一大卖点，也是我继续喜欢每天使用这个框架的原因。

### 不要脸的塞！

[![](img/f572d5df905b6366d1201f6c51d1b8c2.png)](http://hellorails.io)

我有一门新课程叫做 [Hello Rails](https://hellorails.io) 。Hello Rails 是一门现代课程，旨在帮助您快速开始使用和理解 Ruby on Rails。如果你是 Ruby 或 Ruby on Rails 的新手，我邀请你去看看这个网站。该课程将与这些构建非常相似，但是是一个超级深入的版本，具有更现实的目标和可交付成果。[报名今天得到通知](https://hellorails.io/)！

在推特上关注 [@hello_rails](https://twitter.com/hello_rails) 和[本人@justalever](https://twitter.com/justalever) 。

帖子[如何在 Ruby on Rails 中创建定制的脚手架模板](https://web-crunch.com/how-to-create-custom-scaffold-templates-in-ruby-on-rails/)最先出现在[网站](https://web-crunch.com)上。
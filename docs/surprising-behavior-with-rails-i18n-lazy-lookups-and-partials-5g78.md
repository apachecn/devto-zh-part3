# Rails I18n 延迟查找和部分查询的惊人行为

> 原文：<https://dev.to/zspencer/surprising-behavior-with-rails-i18n-lazy-lookups-and-partials-5g78>

Rails i18n 支持[惰性查找](https://guides.rubyonrails.org/i18n.html#lazy-lookup)，因此如果您有一个类似于
的本地文件

```
en:
 newsletter_cta:
   title: "Stay  Up  To  Date  With  The  Latest  Happenings" 
```

在`app/views/_newsletter_cta.html.erb`时，您可以通过`t('.title')`引用`newsletter_cta.title`中的值

在许多 Rails 应用程序中，我提取部分代码来封装组件，因此任何面向 HTML/CSS 的团队成员在编写组件时都不必学习如何编写助手方法；因此，我们不会在升级或转移底层 UI 框架时，以复制粘贴代码的方式结束，这将带来巨大的痛苦。

下面是一个引导部分的例子:

```
# app/views/application/_section.html.erb
<section class="section-wrap">
  <div class="container">
    <%= yield %>
  </div>
</section> 
```

然后我可以在其他视图中使用它，就像这样:

```
# app/views/_newsletter_cta.html.erb
<%= render "section" do %>
  <h2><%= t('newsletter_cta.title') %></h2>
  <%= render "cta-button", text: t('newsletter_cta.title') %> 
<% end %> 
```

这很好，只要不依赖于懒惰查找，并且我为翻译硬编码了完整的查找字符串。

然而，我一使用懒惰查找，翻译就找不到。

```
# app/views/_newsletter_cta.html.erb 
<%= render "section" do %>
  <h2><%= t('.title') %></h2>
  <%= render "cta-button", text: t('.title') %> 
<% end %> 
```

追溯到[action view::Helpers::translation helper](https://github.com/rails/rails/blob/b9ca94caea2ca6a6cc09abaffaad67b447134079/actionview/lib/action_view/helpers/translation_helper.rb#L87)中的 translate 方法，我们看到它依赖于一个名为 [`scope_key_by_partial`](https://github.com/rails/rails/blob/b9ca94caea2ca6a6cc09abaffaad67b447134079/actionview/lib/action_view/helpers/translation_helper.rb#L126) 的方法，而这个方法又依赖于一个实例变量`@virtual_path`。

你们中有谁知道在使用块的分部时保持虚路径的方法吗？某个地方有没有改变这种行为的宝石？我有点惊讶没有其他人遇到这种情况。
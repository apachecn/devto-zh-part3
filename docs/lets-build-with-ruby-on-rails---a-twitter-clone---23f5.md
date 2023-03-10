# 让我们构建:用 Ruby on Rails——Twitter 的克隆

> 原文：<https://dev.to/justalever/lets-build-with-ruby-on-rails---a-twitter-clone---23f5>

欢迎来到我们的下一期《让我们一起建造》!这个构建引入了一个著名的社交媒体巨头 Twitter 作为项目的灵感来源。我们将创建一个叫做**Twitter**的山寨版。

这个应用程序将最显著地演示一个用户角色系统，让您的 web 应用程序和本机应用程序在浏览器中感受一切。我将比之前的[版本](https://web-crunch.com/lets-build-with-ruby-on-rails-blog-with-comments/)快一点，因为它更像是一个基础练习，我解释了围绕 Ruby on Rails 生态系统的常见概念。我们将利用 Rails 生成器来帮助搭建我已经介绍过的一些东西。如果你还没有看过[首先让我们建造](https://web-crunch.com/lets-build-with-ruby-on-rails-blog-with-comments/)，我邀请你这样做，以便了解我们在做什么。

#### 下载源代码

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)[just alever](https://github.com/justalever)/[Twitter](https://github.com/justalever/twittter)

### 这是一个关于如何使用 Ruby on Rails 克隆 Twitter 的一些特性的教程

<article class="markdown-body entry-content container-lg" itemprop="text">

# 让我们构建:用 Ruby on Rails——Twitter 的克隆

[![feature-image](img/674246559bfb9510de49d01ea90f3135.png)](https://camo.githubusercontent.com/dba7645fb07722f9853d55814743a837ddd9403c7b5c544b9f5aea61ed172839/68747470733a2f2f692e696d6775722e636f6d2f3651486a594a462e6a7067)

欢迎来到我们的下一期《让我们一起建造》!这个构建引入了一个著名的社交媒体巨头 Twitter 作为项目的灵感来源。我们将创建一个叫做**Twitter**的山寨版。

这个应用程序将最显著地演示一个用户角色系统，让你的 web 应用程序和主动应用程序感觉都在浏览器中。我将比之前的[版本](https://web-crunch.com/lets-build-with-ruby-on-rails-blog-with-comments/)快一点，因为它更像是一个基础练习，我解释了围绕 Ruby on Rails 生态系统的常见概念。我们将利用 Rails 生成器来帮助搭建我已经介绍过的一些东西。如果你还没有看过[首先让我们建造](https://web-crunch.com/lets-build-with-ruby-on-rails-blog-with-comments/)，我邀请你这样做，以便了解我们在做什么。

#### 下载源代码

[下载源代码](https://github.com/justalever/twittter)

### 我们正在建造的东西

应用程序本身…

</article>

[View on GitHub](https://github.com/justalever/twittter)

### 我们在建造什么

该应用程序本身将具有一个基本的 CRUD 原则，我们可以创建、读取、更新和销毁*tweets*。本质上，这与我之前为博客创建帖子的构建是一样的。在*tweets*之上，我引入了一个名为[device](https://github.com/plataformatec/devise)的新 gem，它使得创建完整的用户角色和认证系统变得容易。结合这个 gem，我们可以验证想要创作 Tweeets 的用户。然后，用户的*tweets*也被绑定到他们的账户。最终结果是一个面向公众的网站，有来自不同用户的推文流。拥有帐户的用户可以登录创建他们自己的*tweets*来添加到公共流。

### 我们不会报道的内容

Twitter 是一个非常复杂的应用程序。我不会报道这个著名的社交媒体巨头的回复、转发、喜欢和其他一些基本功能。相反，我邀请您尝试如何添加这些功能。类似于我做的带有评论构建的的[博客，看看你是否能弄清楚如何自己给应用程序添加回复。(提示:和我在之前的构建中给博文添加评论是一样的；) ).我可能会重新访问这个应用程序，以添加更多的功能到另一个练习中，但我真的没有时间了！如果你想看到我进一步扩展，请告诉我。](https://web-crunch.com/lets-build-with-ruby-on-rails-blog-with-comments/)

在野外也有一些“喜欢”帖子的宝石。[这是我的最爱](https://github.com/schneems/Likeable)

**注意:**我在退出这个版本后注意到的一个大错误是，任何登录的用户都可以编辑**任何**其他用户的 tweeets。这是一个很大的安全缺陷，因为用户的能力应该只存在于他们自己的账户中。看看你能否找到一种方法，只允许当前登录的用户编辑他们自己的推文，而不允许其他用户编辑。我可能会重温这个系列来扩展和解决这些问题。

### 手表第一部分

[https://www.youtube.com/embed/5gUysPm64a4](https://www.youtube.com/embed/5gUysPm64a4)

### 手表第二部分

[https://www.youtube.com/embed/ZxkbFOe3lRY](https://www.youtube.com/embed/ZxkbFOe3lRY)

### 手表第三部分

[https://www.youtube.com/embed/V4h7-hR_WME](https://www.youtube.com/embed/V4h7-hR_WME)

### 此建造中使用的红宝石

*   [更好的误差](https://rubygems.org/gems/better_errors) -为了更好的误差

*   [布尔玛](https://github.com/joshuajansen/bulma-rails)——为了简单的 CSS。随意推出你自己的风格和/或使用不同的框架。

*   [守卫](https://github.com/guard/guard) -用于实时重装我们的`scss`、`js`、`css`和`erb`文件，尽管它能做更多！

*护卫者需要护卫者的肝脏来装载宝石才能工作*

*   [护肝负载](https://github.com/guard/guard-livereload)

*   [简单表单](https://github.com/plataformatec/simple_form) -针对简单表单！

*   [设计](https://github.com/plataformatec/devise) -轻松的用户角色和认证

*   [Gravatar_image_tag](https://github.com/mdeering/gravatar_image_tag) -吐出 Gravatar 的集成图像标签。

### 我如何扩展定计

正如在视频中所承诺的，我谈到了扩展 Devise gem 来绕过默认注册控制器并应用我们自己的注册控制器。我参考了一篇博文，这篇博文帮助我理清了事情的真相。我真的邀请你去看看，让你自己更好地理解它。要向您使用的 Devise 模型添加任何附加字段，这一步是 100%必需的。

在我的例子中，我在注册过程中添加了`name`和`username`字段。我需要创建一个注册控制器，它包含以下内容:

```
 # app/controllers/registrations_controller.rb

class RegistrationsController < Devise::RegistrationsController 

    private

    def sign_up_params 
        params.require(:user).permit(:name, :username, :email, :password, :password_confirmation)
    end

    def acount_update_params 
        params.require(:user).permit(:name, :username, :email, :password, :password_confirmation, :current_password)
    end

end 
```

控制器从默认的设备注册控制器扩展而来。我们将`name`和`username`字段添加到`user`模型/表中，并利用 Rails 强参数将它们联系在一起。

#### 向数据库添加字段

我们仍然需要在数据库中的`users`表中添加几个新列来实现这一点。要添加这些，您需要运行迁移。运行以下命令:

```
$ rails g migration AddFieldsToUsers
```

然后前往`db/migrate/XXXXXXXXXXXXXX_add_fields_to_users.rb`寻找新的迁移。该说的都说了，该做的都做了，我补充了以下内容:

```
 class AddFieldsToUsers < ActiveRecord::Migration[5.1]
  def change
    add_column :users, :name, :string
    add_column :users, :username, :string
    add_index :users, :username, unique: true
  end
end 
```

我们正在向我们的`users`表中添加两列，这两列由我们想要添加到注册表单中的`name`和`username`字段组成。我更进一步，确保`username`字段是唯一的，这基本上意味着没有两个用户可以使用同一个用户名。

有了这些字段和迁移，您就可以继续运行`rails db:migrate`来将它们绑定在一起。

#### 修改设计视图

在`app/views/devise/registrations/new.html.erb`中，您可以添加以下更新的代码来反映我们最近的变化。

```
 <!-- app/views/devise/registrations/new.html.erb -->
<div class="section">
    <div class="container">
    <div class="columns is-centered">

        <div class="column is-4">

        <h2 class="title is-2">Sign Up</h2>

        <%= simple_form_for(resource, as: resource_name, url: registration_path(resource_name)) do |f| %>
      <%= f.error_notification %>

      <div class="field">
        <div class="control">
        <%= f.input :name, required: true, autofocus: true, input_html: { class:"input" }, wrapper: false, label_html: { class:"label" } %>
        </div>
        </div>

        <div class="field">
        <div class="control">
        <%= f.input :username, required: true, input_html: { class:"input" }, wrapper: false, label_html: { class:"label" } %>
        </div>
        </div>

        <div class="field">
        <div class="control">
        <%= f.input :email, required: true, input_html: { class:"input" }, wrapper: false, label_html: { class:"label" } %>
        </div>
        </div>

        <div class="field">
            <div class="control">
                <%= f.input :password, required: true, input_html: { class:"input" }, wrapper: false, label_html: { class:"label" }, hint: ("#{@minimum_password_length} characters minimum" if @minimum_password_length) %>
            </div>
        </div>

        <div class="field">
            <div class="control">
                <%= f.input :password_confirmation, required: true, input_html: { class: "input" }, wrapper: false, label_html: { class: "label" } %>     
            </div>
        </div>

        <div class="field">
            <div class="control">
                <%= f.button :submit, "Sign up", class:"button is-info is-medium" %>
            </div>
        </div>

        <% end %>
            <br />
            <%= render "devise/shared/links" %>
        </div>
        </div>
    </div>
</div>
```

这里我使用了布尔玛，并将我们的`name`和`username`字段添加到表单中。您将希望在同一个目录中的`edit.html.erb`文件上重复这一更改。

```
 <!-- app/views/devise/registrations/edit.html.erb -->

<section class="section">
  <div class="container">
    <div class="columns is-centered">
      <div class="column is-4">

      <h2 class="title is-2">Edit <%= resource_name.to_s.humanize %></h2>
      <%= simple_form_for(resource, as: resource_name, url: registration_path(resource_name), html: { method: :put }) do |f| %>
        <%= f.error_notification %>

          <div class="field">
            <div class="control">
              <%= f.input :name, required: true, autofocus: true, input_html: { class: "input"}, wrapper: false, label_html: { class: "label" } %>
            </div>
          </div>

          <div class="field">
            <div class="control">
              <%= f.input :username, required: true,  input_html: { class: "input"}, wrapper: false, label_html: { class: "label" } %>
            </div>
          </div>

          <div class="field">
            <div class="control">
              <%= f.input :email, required: true, input_html: { class: "input"}, wrapper: false, label_html: { class: "label" } %>
            </div>
          </div>

          <div class="field">
          <% if devise_mapping.confirmable? && resource.pending_reconfirmation? %>
            <p>Currently waiting confirmation for: <%= resource.unconfirmed_email %></p>
          <% end %>
          </div>

          <div class="field">
            <div class="control">
            <%= f.input :password, autocomplete: "off", hint: "leave it blank if you don't want to change it", required: false, input_html: { class: "input"}, wrapper: false, label_html: { class: "label" } %>
            </div>
          </div>

          <div class="field">
            <div class="control">
            <%= f.input :password_confirmation, required: false, input_html: { class: "input"}, wrapper: false, label_html: { class: "label" } %>
            </div>
          </div>

          <div class="field">
            <div class="control">
              <%= f.input :current_password, hint: "we need your current password to confirm your changes", required: true, input_html: { class: "input"}, wrapper: false, label_html: { class: "label" } %>
            </div>
        </div>

        <%= f.button :submit, "Update", class:"button is-info" %>

      <% end %>

        <hr />
        <h3 class="title is-5">Cancel my account</h3>
        <p>Unhappy? <%= link_to "Cancel my account", registration_path(resource_name), data: { confirm: "Are you sure?" }, method: :delete %></p>

      </div>
    </div>
  </div>
</section>
```

你想从用户那里“要求”什么取决于你自己。后来我注意到,`edit.html.erb`表单不一定需要这么多必填字段。您可以随意扩展/修改它。

### 包装完毕

这次构建的主要目的是重新引入在[之前的构建](https://web-crunch.com/lets-build-with-ruby-on-rails-blog-with-comments/)中使用的技术和术语。在前面构建的基础上，我更进一步，引入了身份验证和用户角色。我们没有建立一个功能完整的 Twitter 克隆，但我们确实采取了一些零碎的东西来创建类似的东西。

欢迎您进一步扩展这个项目。随着时间的推移，我自己甚至可能会这样做，只是为了解决我在这个过程中发现的一些错误，以及将新的技术和功能集成到组合中。我希望你和我一样喜欢这个建筑。随着我们向前推进，我计划引入更多的概念和想法来构建你可以自己开发的应用程序。下一期再见！

##### 学分

第二部分音乐学分-[https://soundcloud.com/argofox](https://soundcloud.com/argofox)

#### 到目前为止的级数

*   [01 -简介](https://web-crunch.com/lets-build-with-ruby-on-rails-introduction/)
*   [02 -安装](https://web-crunch.com/lets-build-with-ruby-on-rails-installation/)
*   [03 -创建一个带有评论的博客](https://web-crunch.com/lets-build-with-ruby-on-rails-blog-with-comments/)
*   创建一个推特克隆体 -你在这里

## 不要脸的插头

如果你喜欢这篇文章，我在 [YouTube](https://youtube.com/c/webcrunch) 和我的[博客](https://web-crunch.com)上有更多内容。我计划在这里开始创作更多的作品。希望收件箱中有更多这样的内容吗？[订阅我的简讯](https://web-crunch.com/subcribe)自动获取。

 [![](img/c009079376aa2fccf2abd73f5ab135d8.png)](https://hellorails.io) 
☝想从头开始学习 Ruby on Rails？查看我即将推出的课程 [Hello Rails](https://hellorails.io) 。
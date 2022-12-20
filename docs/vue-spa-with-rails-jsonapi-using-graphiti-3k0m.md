# 使用 Rails JSON 的 Vue SPA:使用 Graphiti 的 API

> 原文：<https://dev.to/mikeeus/vue-spa-with-rails-jsonapi-using-graphiti-3k0m>

标准是好的，它们让我们的生活更容易。 [Graphiti](https://graphiti-api.github.io/graphiti/guides/) 和它的客户端库 [Spraypaint](https://graphiti-api.github.io/graphiti/js/) 使得构建 [JSON:API](https://jsonapi.org/) 兼容的 API 变得容易，这些 API 与前端框架如 Vue 无缝集成。

我在一个生产应用程序中使用 graphiti 为嵌入在我们的 Rails 视图中的 Vue 组件提供 JSON 请求。它可靠、灵活，使用起来也很愉快。

在本教程中，我们将使用 graphiti 通过 JSON:API 兼容 Rails 5 API 将 Vue 设置为 SPA。您可以克隆演示应用程序来查看成品。

```
# follow along
git clone git@github.com:mikeeus/demos-rails-webpack.git
cd demos-rails-webpack
git checkout ma-vue-graphiti 
```

## 用 Webpacker 设置 Rails API

用 webpacker 和 Vue 创建 Rails 应用程序。我使用 postgresql，但是你可以使用任何你喜欢的数据库。

```
mkdir rails-vue
rails new . --webpack=vue —database=postgresql
rails db:create db:migrate 
```

就这样…完成了！很简单，对吧？现在，我们可以根据 JSON API 规范设置 graphiti 来处理记录的解析和序列化。

## 设置图形

安装 graphiti，你可以在文档中找到[的完整说明。我们需要添加以下宝石。](https://graphiti-api.github.io/graphiti/guides/getting-started/installation) 

```
# The only strictly-required gem
gem 'graphiti'

# For automatic ActiveRecord pagination
gem 'kaminari'

# Test-specific gems
group :development, :test do
  gem 'rspec-rails'
  gem 'factory_bot_rails'
  gem 'faker'
  gem 'graphiti_spec_helpers'
end

group :test do
  gem 'database_cleaner'
end 
```

我们需要将`Graphiti::Rails`添加到我们的应用程序控制器中，这样 graphiti 就可以处理解析和序列化我们的请求。我们将注册`Graphiti::Errors::RecordNotFound`异常，这样我们就可以返回 404。

```
# app/application_controller.rb
class ApplicationController < ActionController::API
    include Graphiti::Rails

    # When #show action does not find record, return 404
    register_exception Graphiti::Errors::RecordNotFound, status: 404

    rescue_from Exception do |e|
        handle_exception(e)
    end
end 
```

现在让我们创建一个 Post 模型。

```
rails g model Post title:string content:string
rails db:migrate 
```

我们还需要为 graphiti 创建一个 PostResource 和一个控制器来处理请求。Graphiti 有一个[生成器](https://graphiti-api.github.io/graphiti/guides/concepts/resources#generators)，可以很容易地设置它。

```
rails g graphiti:resource Post -a index 
```

我们将声明我们的属性并添加`ActionView::Helpers::TextHelper`来使用`simple_format`格式化我们的帖子内容，这样我们就可以在我们的客户端上很好地呈现它。

```
class PostResource < Graphiti::Resource
    include ActionView::Helpers::TextHelper

    self.adapter = Graphiti::Adapters::ActiveRecord
    primary_endpoint '/api/v1/posts'

    attribute :title, :string
    attribute :content, :string do
        simple_format(@object.content)
    end
end 
```

生成器还会在`app/controllers/posts_controller.rb`创建规格和控制器。我们将把它移动到一个命名空间文件夹`app/api/v1`，这将允许我们在将来管理 API 版本。

```
# app/controllers/api/v1/posts_controller.rb
module Api
    module V1
        class PostsController < ApplicationController
            def index
                posts = PostResource.all(params)
                render jsonapi: posts
            end
        end
    end
end 
```

我们使用`render jsonapi: posts`根据 JSON:API 规范渲染帖子，这样我们就可以使用 graphiti 的 js 客户端 [spraypaint](https://graphiti-api.github.io/graphiti/js/) 在我们的客户端上解析它。

现在让我们添加路线。

```
# config/routes.rb
Rails.application.routes.draw do
    namespace :api do
        namespace :v1 do
            resources :posts, only: :index
        end
    end
end 
```

好吧，看起来不错！我们现在只需要一个客户端来使用我们的 API。

## 设置视图

Webpacker 附带了一个 vue 生成器，你可以在文档中看到。这使得向我们的应用程序添加 Vue 或任何其他前端框架(如 React 或 Angular)变得非常容易。

```
bundle exec rails webpacker:install:vue 
```

运行以上程序将在`app/javascript`生成文件

我们将编辑`app/javascript/packs/application.js`,以便可以呈现我们的 App.vue 组件。

```
// app/javascript/packs/application.js

import Vue from 'vue/dist/vue.esm'
import App from '../app.vue'

document.addEventListener('DOMContentLoaded', () => {
    const app = new Vue({
        el: '#app',
        components: { App }
    })
}) 
```

现在我们可以忽略 Vue 组件，我们将在设置好资源和端点后填充它。

## 设置 Rails 来提供静态内容

我们不能使用我们的 ApplicationController 来服务我们的`index.html`页面，因为它是从`ActionController::Api`继承的，并且我们希望保持这种方式，因为我们的其他控制器将从它继承。

为了服务于 SPA 的索引页面，我们将使用继承自`ActionController::Base`的`PagesController`,这样它就可以无问题地服务于 html 文件。

```
# app/pages_controller.rb
class PagesController < ActionController::Base
  def index
  end
end 
```

接下来，我们将为我们的主页添加一个路由，并将所有 404 请求重定向到它，以便我们的 SPA 可以处理业务。

```
# config/routes.rb
Rails.application.routes.draw do
    root 'pages#index'
    get '404', to: 'pages#index'

    namespace :api do
        # ...
    end
end 
```

看起来不错，朋友们！现在让我们添加我们的 index.html 页面，它将呈现我们的 Vue 组件。

```
# app/views/pages/index.html
<%= javascript_pack_tag 'application' %>
<%= stylesheet_pack_tag 'application' %>

<div id="app">
  <app></app>
</div> 
```

这非常简单:它只是拉入由 webpacker 编译的 javascript 和样式表。然后我们添加一个带有 id="app "的 div 和一个嵌套的`<app></app>`，这样我们的 Vue 包装器组件就可以拾取它并呈现主组件。

这是我们的应用程序运行所需的唯一 Rails 视图。

## 在客户端创建模型

通常当我构建一个 SPA 时，我会编写使用 axios 之类的库向后端发出 Ajax 请求的服务。Graphiti 附带了一个名为 [spraypaint](https://graphiti-api.github.io/graphiti/js/) 的客户端库，用于处理 JSON:API 有效负载的解析和序列化。它支持包括关联、高级过滤、排序、统计等等。

我们开始吧！

```
yarn add spraypaint isomorphic-fetch 
```

接下来让我们创建一个`ApplicationRecord`类来存储我们的喷漆配置。

```
// app/javascript/models/application_record.js

import { SpraypaintBase } from 'spraypaint';

export const ApplicationRecord = SpraypaintBase.extend({
  static: {
    baseUrl: '',
    apiNamespace: '/api/v1',
  }
}) 
```

我们将`baseUrl`和`apiNamespace`分别设置为“”和“/api/v1”，这样 spraypaint 使用相对路径并避免 CORS 请求。它还命名了我们的请求，因此我们可以轻松地管理 API 版本。

现在的岗位型号

```
// app/javascript/models/post.model.js

import { ApplicationRecord } from './application_record';

export const Post = ApplicationRecord.extend({
    static: {
        jsonapiType: 'posts'
    },

    attrs: {
        id: attr(),
        title: attr(),
        content: attr()
    },

    methods: {
        preview() {
            return this.content.slice(0, 50).trim() + '...'
        }
    }
}) 
```

我们声明了`id`、`title`和`content`属性。我们还添加了一个方法来返回内容的截断预览，以显示我们如何声明方法。

需要使用`jsonapiType`属性来生成端点并解析和序列化 JSON 负载。

现在我们已经准备好将客户端连接到 API。

## 勾搭 SPA

为了连接一切，我们将创建一个 Vue 组件，它使用我们的 spraypaint 模型与我们的端点进行通信。

```
// app/javascript/app.vue

<template>
<div>
    <h1>Posts</h1>
    <div v-if="error" class="error">{{error}}</div>
    <div class="loading" v-if="loading">Loading...</div>
    <ul>
        <li v-for="post of posts" :key="post.id">
            <h3>{{post.title}}</h3>
            <p v-html="post.preview()"></p>
        </li>
    </ul>
</div>
</template>

<script>
import {Post} from './models/post.model'

export default {
    data: function () {
        return {
            posts: [],
            error: null,
            loading: true
        }
    },

    created() {
        Post.all()
            .then(res => {
                this.posts = res.data
                this.loading = false
            })
            .catch(err => {
                this.error = err
                this.loading = false
            })
        }
}
</script>

<style scoped>
h1 {
    text-align: center;
}
ul {
    list-style: none;
}
</style> 
```

太棒了。如果我们在控制台中添加一些帖子并运行应用程序，我们将看到帖子加载并呈现在页面中。

注意，我们导入了 Post 模型，并在我们的`created()`调用中使用它，就像它是一个 Rails 模型一样。调用`Post.all()`返回一个承诺，我们可以链接设置我们的`posts`和`loading`数据属性。喷漆模型可以链接更多有用的方法，如`where`和`page`。

```
Post.where({ search: 'excerpt' })
    .stats({ total: 'count' })
    .page(1)
    .per(10)
    .order({ created_at: 'desc' })
    .all()
    .then(res => ...) 
```

Spraypaint 是一个非常强大的库，支持分页、排序、统计、复杂过滤等等。您可以查看[喷漆文档](https://graphiti-api.github.io/graphiti/js/)了解详细指南。

## 结论

标准很好。Vue 很牛逼，Rails 也是。Graphiti 和 Spraypaint 等 Gems 和库使得构建符合上述标准并与 Vue 等框架无缝集成的可伸缩 REST APIs 变得非常容易。

我希望你喜欢这篇文章，如果你喜欢，别忘了喜欢。我很想听听你对其他文章的想法或建议。就在下面留言吧:)
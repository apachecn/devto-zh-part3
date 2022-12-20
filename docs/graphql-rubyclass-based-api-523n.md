# GraphQL Ruby(基于类的 API)

> 原文：<https://dev.to/k_penguin_sato/graphql-rubyclass-based-api-523n>

# 简介

我将通过使用 [GraphQL Ruby](http://graphql-ruby.org/) 向您展示如何在您的 rails 应用程序中使用 GraphQL。
我们将创建一个非常简单的 api，用户可以获得，创建，更新，删除帖子。
你可以在这里查看源代码[。](https://github.com/K-Sato1995/GraphqlApi)

# 基本概念

让我们来看看`GraphQL`的一些非常基本的概念

## 图式

模式定义了服务器的 API。它提供了服务器和客户端之间的联系点。您必须为查询创建一个，为突变创建另一个。

## 类型

构成模式的所有事物的形状。
每种类型都有一组`fields`来定义该类型应该返回的数据。

## 解析器

解析器是 GraphQL 服务器用来执行获取(`queries`)或变异(`mutations`)数据的`functions`。
graph QL 类型的每个字段都需要一个相应的解析函数。

有关 GraphQL 基本概念的更多信息，请查看[官方指南](https://graphql.org/learn/queries/)。

## 安装

# GraphQL 与 Ruby on Rails

简单地说，把`graphql`和`graphql-rail`加到你的`Gemfile`上。

```
# Gemfile
gem 'graphql'
gem 'graphiql-rails' 
```

如果您想将 graphiql-rails 用于 api only rails 应用程序，请将下面一行添加到您的`config/application.rb`中。

```
# config/application.rb
require "sprockets/railtie" 
```

添加完宝石和线条后，运行下面的命令。第二个命令将创建`app/graphql`文件夹。
所有与 graphql 相关的文件都将保存在该目录下。

```
$ bundle install
$ rails generate graphql:install 
```

## 查询

在`GraphQL`中，`Queries`用于获取数据，`Mutations`用于创建、更新和删除数据。
在这一节，我们将重点介绍`queries`。

创建一个模型`post`并运行`rake db:migrate`。

```
$ rails g model Post title:string description:text
$ rake db:migrate 
```

在`rails console`上创建一些数据。

```
$ rails c
$ Post.create(title: "What is Ruby?", description:"Ruby is a programming language")
$ Post.create(title: "How to learn Ruby", description:"Read some books brah") 
```

为`app/graphql/types/post_type.rb`中的`Post`定义`GraphQL Type`。
如果你运行下面的命令，它会自动为你创建`Post Type`。

```
$ rails g graphql:object Post id:ID! title:String! description:String! 
```

上面的命令将生成这个。

```
# app/graphql/types/post_type.rb
module Types
  class PostType < Types::BaseObject
    field :id, ID, null: false
    field :title, String, null: false
    field :description, String, null: false
  end
end 
```

如果你想了解关于`Class-based`系统的细节，请阅读[官方 graphql-ruby 文档。](http://graphql-ruby.org/schema/class_based_api.html#classes)。

## 查询解析器

类型现在已经定义了，但是服务器仍然不知道如何处理它。我们使用`resolvers`来执行`queries`。

所有 GraphQL 查询都从一个名为`Query`的根类型开始。当您之前运行`rails g graphql:install`时，它会在`app/graphql/types/query_type.rb`中为您创建根查询类型。

使用下面的代码，您可以检索所有的文章和每个使用其唯一 id 的特定文章。

```
# app/graphql/types/query_type.rb
module Types
  class QueryType < Types::BaseObject
    field :posts, [Types::PostType], null: false
    def posts
      Post.all
    end

    field :post, Types::PostType, null: false do
      argument :id, Int, required: false
    end
    def post(id:)
      Post.find(id)
    end
  end
end 
```

### 用图形进行测试

将以下代码添加到您的`routes.rb`中。

```
Rails.application.routes.draw do
  post '/graphql', to: 'graphql#execute'

  if Rails.env.development?
    # add the url of your end-point to graphql_path.
    mount GraphiQL::Rails::Engine, at: "/graphiql", graphql_path: "/graphql" 
  end
end 
```

在使用`rails s`启动 rails 服务器之后，您可以使用`http://localhost:3000/graphiql`来测试您的进度。

如果你想得到所有的帖子，发送下面的查询。

```
# Query for posts
{
 posts {
   id
   title
   description
 }
}

################ Result #####################
{
  "data": {
    "posts": [
      {
        "id": 1,
        "title": "title1",
        "description": "description1"
      },
      {
        "id": 2,
        "title": "title2",
        "description": "description2"
      },
      {
        "id": 3,
        "title": "title3",
        "description": "description3"
      },
     ]
   }
} 
```

如果你想得到一个特定的职位，发送以下查询。

```
# Query for a post
{
 post(id:1) {
   id
   title
   description
 }
}

################ Result #####################
{
  "data": {
    "post": {
      "id": 1,
      "title": "title1",
      "description": "description1"
    }
  }
} 
```

## 突变

所有的 GraphQL 突变都是从一个叫做`Mutation`的根类型开始的。
该类型在`app/graphql/types/mutation_type.rb`中自动生成。

```
module Types
  class MutationType < Types::BaseObject
    # TODO: remove me
    field :test_field, String, null: false,
      description: "An example field added by the generator"
    def test_field
      "Hello World"
    end
  end
end 
```

### 突变创造

运行下面的命令，为创建帖子生成一个变异。

```
$ rails g graphql:mutation CreatePost 
```

上面的命令将完成以下两件事。

*   (1)创建`graphql/mutations/create_post.rb`。
*   (2)将`field :createPost, mutation: Mutations::CreatePost`加到`graphql/types/mutations_type.rb`上。

```
# (1) Create graphql/mutations/create_post.rb.
# graphql/mutations/create_post.rb
module Mutations
  class CreatePost < GraphQL::Schema::RelayClassicMutation
    # TODO: define return fields
    # field :post, Types::PostType, null: false

    # TODO: define arguments
    # argument :name, String, required: true

    # TODO: define resolve method
    # def resolve(name:)
    #   { post: ... }
    # end
  end
end 
```

```
# (2) Add  field :createPost, mutation: Mutations::CreatePost to graphql/types/mutations_type.rb.
# app/graphql/types/mutation_type.rb
module Types
  class MutationType < Types::BaseObject
    field :createPost, mutation: Mutations::CreatePost
  end
end 
```

**这部分是可选的！！！**

你可以创建`graphql/mutations/base_mutation.rb`，让`graphql/mutations/create_post.rb`继承它。

```
# graphql/mutations/base_mutation.rb
class Mutations::BaseMutation < GraphQL::Schema::RelayClassicMutation
end 
```

```
# graphql/mutations/create_post.rb
module Mutations
  class CreatePost < Mutations::BaseMutation
    # TODO: define return fields
    # field :post, Types::PostType, null: false

    # TODO: define arguments
    # argument :name, String, required: true

    # TODO: define resolve method
    # def resolve(name:)
    #   { post: ... }
    # end
  end
end 
```

让`create_post.rb`继承`Mutations::BaseMutation`之后，让我们按照`create_post.rb`中的`TODOs`修改文件，如下面的代码所示。
(如果您没有遵循可选部分，`create_post.rb`仍然继承您文件中的`GraphQL::Schema::RelayClassicMutation`。)

```
module Mutations
  class CreatePost < Mutations::BaseMutation
    graphql_name 'CreatePost'

    field :post, Types::PostType, null: true
    field :result, Boolean, null: true

    argument :title, String, required: false
    argument :description, String, required: false

    def resolve(**args)
      post = Post.create(title: args[:title], description: args[:description])
      {
        post: post,
        result: post.errors.blank?
      }
    end
  end
end 
```

就是这样！现在，您已经准备好创建帖子了！。

### 用图形进行测试

在启动 rails 服务器之后，您可以使用 [GraphQL](http://localhost:3000/graphiql) 来测试您的进度。
像下面的代码一样发出请求并检查结果。

```
mutation {
  createPost(
    input:{
      title: "title1"
      description: "description1"
    }
  ){
    post {
      id
      title 
      description
    }
  }
}

################ Result #####################
{
  "data": {
    "createPost": {
      "post": {
        "id": 15,
        "title": "title1",
        "description": "description1"
      }
    }
  }
} 
```

### 突变更新

运行下面的代码来生成一个更新帖子的变异。

```
$ rails g graphql:mutation UpdatePost 
```

像下面的代码一样修改`/graphql/mutations/update_post.rb`。和`/graphql/mutations/create_post.rb`里的代码很像。唯一显著的不同是，它需要`id`作为参数来更新特定的帖子。

```
module Mutations
  class UpdatePost < Mutations::BaseMutation
    graphql_name 'UpdatePost'

    field :post, Types::PostType, null: true
    field :result, Boolean, null: true

    argument :id, ID, required: true
    argument :title, String, required: false
    argument :description, String, required: false

    def resolve(**args)
      post = Post.find(args[:id])
      post.update(title: args[:title], description: args[:description])
      {
        post: post,
        result: post.errors.blank?
      }
    end
  end
end 
```

### 用图形进行测试

发出类似下面代码的请求并检查结果。

```
mutation {
  updatePost(
    input:{
      id: 1
      title: "Updated"
      description: "UPdated"
    }
  ){
    post {
      id
      title 
      description
    }
  }
}

################ Result #####################
{
  "data": {
    "updatePost": {
      "post": {
        "id": 1,
        "title": "Updated",
        "description": "UPdated"
      }
    }
  }
} 
```

### 突变删除

做与创建`create`和`update`突变几乎相同的事情。
运行下面的命令并修改生成的文件。

```
$ rails g graphql:mutation DeletePost 
```

这一次，你只需要一个参数`id`就可以删除一篇文章！

```
# graphql/mutations/delete_post.rb
module Mutations
  class DeletePost < Mutations::BaseMutation
    graphql_name 'DeletePost'

    field :post, Types::PostType, null: true
    field :result, Boolean, null: true

    argument :id, ID, required: true

    def resolve(**args)
      post = Post.find(args[:id])
      post.destroy
      {
        post: post,
        result: post.errors.blank?
      }
    end
  end
end 
```

### 用图形进行测试

发出类似下面代码的请求并检查结果。

```
mutation {
  deletePost(
    input:{
      id: 1
    }
  ){
    post {
      id
      title 
      description
    }
  }
}

################ Result #####################
{
  "data": {
    "posts": [
      {
        "id": 2,
        "title": "How to learn Ruby"
      },
      {
        "id": 3,
        "title": "title1"
      }]
  }
} 
```

## 连接字段(1)

一旦你理解了`types`、`queries`和`mutations`是如何工作的，就很容易用联想来工作。
让它能够检索每个帖子上的评论和相应的帖子。
首先，创建评论模型，建立与文章模型的`has_many`关联，并创建一些数据，以便稍后检查查询。

```
$ rails g model Comment content:string post:references 
```

```
# app/models/comment.rb
class Comment < ApplicationRecord
  belongs_to :post
end

# app/models/post.rb
class Post < ApplicationRecord
  has_many :comments, dependent: :destroy
end 
```

```
# db/seeds.rb
Post.new.tap do |post|
  post.title = 'title'
  post.description = 'description'
  post.comments.build(content: 'comment1')
  post.save!
end 
```

```
$ rake db:seed 
```

其次，通过运行下面的命令创建注释类型。

```
$ rails g graphql:object Comment id:ID! content:String! 
```

该命令将创建一个类似下面代码的文件。

```
# app/graphql/types/comment_type.rb
module Types
  class CommentType < Types::BaseObject
    description 'Comment'

    field :id, ID, null: false
    field :content, String, null: false
  end
end 
```

最后，将评论字段添加到帖子类型中。

```
# app/graphql/types/post_type.rb
module Types
  class PostType < Types::BaseObject
    description 'Post'

    field :id, Int, null: false
    field :title, String, null: false
    field :description, String, null: false
    field :comments, [Types::CommentType], null: false 
  end
end 
```

### 用图形进行测试

发出类似下面代码的请求并检查结果。

```
{
  posts {
    id
    title
    comments {
      id
      content
    }
  }
}

################ Result #####################
{
  "data": {
    "posts": [
      {
        "id": "1",
        "title": "title",
        "comments": [
          {
            "id": "1",
            "content": "comment1"
          }
        ]
      }
    ]
  }
} 
```

## 连接字段(2)

让我们让它能够创建一个新的评论。
首先，运行下面的命令创建`CreateComment`突变，并将其添加到`/graphql/types/mutation_type.rb`。

```
$ rails g graphql:mutation CreateComment 
```

接下来，将`field :post, Types::PostType, null: false`加到`app/graphql/types/comment_type.rb`上。

```
# app/graphql/types/comment_type.rb
module Types
  class CommentType < Types::BaseObject
    description 'Comment'
    field :id, ID, null: false
    field :content, String, null: false
    field :post, Types::PostType, null: false
  end
end 
```

最后，像下面的代码一样修改`app/graphql/mutations/create_comment.rb`。

```
# app/graphql/mutations/create_comment.rb
module Mutations
  class CreateComment < Mutations::BaseMutation
    graphql_name 'CreateComment'

    field :comment, Types::CommentType, null: true
    field :result, Boolean, null: true

    argument :post_id, ID, required: true
    argument :content, String, required: true

    def resolve(**args)
      post = Post.find(args[:post_id])
      comment = post.comments.build(content: args[:content])
      comment.save
      {
        comment: comment,
        result: post.errors.blank?
      }
    end
  end
end 
```

### 用图形进行测试

发出类似下面代码的请求并检查结果。

```
mutation {
  createComment(
    input:{
      postId: 1
      content: "NEW COMMENT"
    }
  ){
    comment {
      id
      content
      post {
        id
        title
        comments {
          id
          content
        }
      }
    }
  }
}

################ Result #####################
{
  "data": {
    "createComment": {
      "comment": {
        "id": "2",
        "content": "NEW COMMENT",
        "post": {
          "id": "1",
          "title": "title",
          "comments": [
            {
              "id": "1",
              "content": "comment1"
            },
            {
              "id": "2",
              "content": "NEW COMMENT"
            }
          ]
        }
      }
    }
  }
} 
```

# 利用解析器目录

首先创建`app/graphql/resolvers/`，创建`app/graphql/resolvers/base_resolver.rb`。

```
module Resolvers
  class BaseResolver < GraphQL::Schema::Resolver
  end
end 
```

其次，创建`app/graphql/resolvers/query_type`，同时在目录下创建`posts_resolver.rb`和`post_resolver.rb`。

```
-- graphql
   -- resolvers
      -- query_type
       - posts_resolver.rb
       - post_resolver.rb 
```

分别修改`posts_resolver.rb`和`post_resolver.rb`Li，如下面的代码。

```
# app/graphql/resolvers/query_type/posts_resolver.rb

module Resolvers
  module QueryType
    class PostsResolver < Resolvers::BaseResolver
      type [Types::PostType], null: false

      def resolve(**_args)
        Post.all
      end
    end
  end
end 
```

```
# app/graphql/resolvers/query_type/post_resolver.rb

module Resolvers
  module QueryType
    class PostResolver < Resolvers::BaseResolver
      type Types::PostType, null: false
      argument :id, ID, required: false

      def resolve(**args)
        Post.find(args[:id])
      end
    end
  end
end 
```

最后，用`query_type.rb`连接它们。
您现在可以使用相同的请求来检索帖子和每个帖子！

```
# app/graphql/types/query_type.rb

module Types
  class QueryType < Types::BaseObject
    field :posts, resolver: Resolvers::QueryType::PostsResolver
    field :post, resolver: Resolvers::QueryType::PostResolver
  end
end 
```

## 参考文献

*   [用 Ruby 后端构建 GraphQL 服务器教程](https://www.howtographql.com/graphql-ruby/0-introduction/)
*   [freeCodeCamp.org 简明英语 GraphQL 指南](https://medium.freecodecamp.org/a-beginners-guide-to-graphql-60e43b0a41f5)
*   [GraphQL Ruby](http://graphql-ruby.org/)
*   [如何在 Rails 中实现 graph QL API-via @ code ship | via @ code ship](https://blog.codeship.com/how-to-implement-a-graphql-api-in-rails/)
*   [当您将 graphiql-rails 用于 api only rails 应用程序时](https://github.com/rmosolgo/graphiql-rails/issues/13)
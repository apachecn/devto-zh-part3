# 在 Rails 中组织路线

> 原文：<https://dev.to/buddyreno/organizing-routes-in-rails-1hd3>

我喜欢 Ruby on Rails，但是有一件事让我很困扰，那就是`routes.rb`文件变得难以处理的速度有多快。当我开始创建我正在制作的 API 的版本 2 时，这个问题出现了。谢天谢地，只要稍微了解一下 ruby 如何引用扩展对象，就有一个简单的方法来处理这个问题。

考虑以下示例路由文件:

```
Rails.application.routes.draw do
  root to: redirect("https://www.example.com/show")

  get "/ready", to: proc { [200, {}, [""]] }

  namespace :api do
    namespace :v1 do
      # about 31 routes
    end
    namespace :v2 do
      # about 36 routes
    end
    # v3, v4, v5 and so on
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这可能会变得非常漫长，尤其是如果您不能在很长一段时间内放弃旧版本的 API。通过利用 ruby 的`extend`类的一些事件和方法，我们可以很容易地打破这一点，并将这些版本化的路线分成它们自己的文件。

## 文件夹和文件结构

`routes.rb`文件位于 Rails 应用程序的`config`文件夹中。在同一个地方创建一个新文件夹:`config/routes`。我们将在此存储新的路径扩展。

在该文件夹中，我希望将文件命名为与文件中的名称空间相同的名称。我要虹吸的第一个路线集合是`:api :v1`。将文件命名为`api_v1_routes.rb`,以匹配我们的模块和名称空间结构。

打开新文件并创建一个名为`ApiV1Routes`的模块。

```
module ApiV1Routes
end 
```

Enter fullscreen mode Exit fullscreen mode

## 扩展

当你扩展一个对象时，ruby 有一个叫做 [extended](https://ruby-doc.org/core-2.6.2/Module.html#method-i-extended) 的有用事件，允许你在对象被...扩展。

```
module ApiV1Routes
  def self.extended(router)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

在上面的方法中，当我们创建一个扩展这个特定模块的类时，extended 事件将通过引用被扩展的对象来触发。在我们的例子中，扩展方法将接收`router`作为参数。

## 在上下文中执行

现在我们有了一个模块，它可以在扩展 rails 路由器时获取对它的引用，我们如何从这里向路由器添加路由呢？

ruby 中的所有对象都有一个名为 [instance_exec](https://ruby-doc.org/core-2.6.3/BasicObject.html#method-i-instance_exec) 的方法。这将获取一个块，并在调用方的上下文中执行该块。在我们的`ApiV1Routes`模块中，如下所示。

```
module ApiV1Routes
  def self.extended(router)
    router.instance_exec do

      # add routes
      namespace :api do
        namespace :v1 do
          # 31 v1 routes
        end
      end

    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

根据`instance_exec`的文档，`self`被设置为调用对象，因此我们可以访问对象的所有实例变量和方法。

> 为了设置上下文，在代码执行时，将变量 self 设置为 obj，使代码能够访问 obj 的实例变量。

添加路线就好像我们在`routes.rb`文件中一样！

## 把一切绑在一起。

现在我们已经创建了模块，我们需要 rails 来加载这些 route 模块，否则我们将无法在代码中引用它们。

打开`application.rb`文件，该文件也应该位于`config`文件夹中。添加下面一行代码:

```
# Load split router files
config.autoload_paths += %W(#{config.root}/config/routes) 
```

Enter fullscreen mode Exit fullscreen mode

这告诉 rails 加载 routes 文件夹中的文件，因为`config/routes`并不默认 rails 会自动加载。

回到`routes.rb`文件，在顶部的`do`块中的所有内容之前添加`extend ApiV1Routes`。

```
Rails.application.routes.draw do
  extend ApiV1Routes
  extend ApiV2Routes

  # all our other routes
end 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！您以这种方式添加的任何路线现在都应该可以使用了！对您想要分离的任何明确定义的路径结构重复此操作，然后将`extend ModuleName`添加到`routes.rb`文件中。
# 在 Ruby 中使用“Hash#fetch”来获得更好的 nil 处理

> 原文：<https://dev.to/raquelxmoss/using-hashfetch-in-ruby-for-better-nil-handling-2l8h>

# 在 Ruby 中使用`Hash#fetch`实现更好的零处理

在 Ruby 中，用`[]`方法从散列中提取值很简单，但是当您要查找的值不在那里时，就会出现问题。这可能导致繁琐的零检查，或者我们最喜欢的错误`Undefined method for nil:NilClass`。

让我们看一个分类广告网站的例子，它在向用户显示列表时对列表进行排序，以及我们可以使用`Hash#fetch`在这些 nil 发生之前主动处理它们的一些方法。

只是对代码做一个简单的说明——这些例子是适度设计的，不一定是你在生产中如何解决这些问题，但是，嘿，至少它们阐明了我的观点！

## 使用 Hash#fetch 设置默认值

```
def index
  valid_sort_orders = {
    lowest_price: :asc,
    highest_price: :desc
  }

  sort_order = valid_sort_orders[listings_params[:sort]]

  @listings = Listing.order(price: sort_order)
end 
```

这里我们有一个问题——如果用户没有提供`sort`参数，或者他们提供了一些无效的东西，该怎么办？有许多方法可以处理这个问题，例如，我们可以用`||`操作符设置默认值。这是一种非常常见的模式，也是处理这种情况的完美方式。

```
def index
  valid_sort_orders = {
    lowest_price: :asc,
    highest_price: :desc
  }

  sort_order = valid_sort_orders[listings_params[:sort]] || :asc

  @listings = Listing.order(price: sort_order)
end 
```

就我个人而言，我更喜欢使用`Hash#fetch`方法来获得稍微更优雅的解决方案。使用`Hash#fetch`，如果在 hash 中没有找到一个键，我们可以提供一个缺省的键来查找，这是一个很好的处理 nil 情况的方法。

```
def index
  valid_sort_orders = {
    lowest_price: :asc,
    highest_price: :desc
  }

  sort_order = valid_sort_orders.fetch(listings_params[:sort], :asc)

  @listings = Listing.order(price: sort_order)
end 
```

尽管语义本身并不是使用这种模式的一个很好的理由，所以让我们看一些更有趣的例子，在这些例子中`Hash#fetch`可以用来主动处理 nils。

## 使用`Hash#fetch`设置一个假值

当您希望接受来自调用者的假值，但默认为真值时，这种模式很有用。让我们看一个例子。

这里我们希望能够用参数控制是否在响应中包含卖家的详细信息，默认值为`true`。很容易不小心做了这样的事情:

```
def index
  @include_seller_details = listings_params[:include_metadata] || true

  valid_sort_orders = {
    lowest_price: :asc,
    highest_price: :desc
  }

  sort_order = valid_sort_orders.fetch(listings_params[:sort], :asc)

  @listings = Listing.order(price: sort_order)
end 
```

这将无法正常工作！因为如果`listing_params[:include_metadata]`是一个 falsey 值，那么无论如何它都会计算为`true`。这可能不是灾难性的，但这意味着我们将在响应中发送比我们想要的更多的信息，这往好里说是不礼貌的，往坏里说可能是安全问题。

解决这个问题的一种方法是使用`Hash#fetch`

```
def index
  @include_seller_details = listing_params.fetch(:include_metadata, true)

  valid_sort_orders = {
    lowest_price: :asc,
    highest_price: :desc
  }

  sort_order = valid_sort_orders.fetch(listings_params[:sort], :asc)

  @listings = Listing.order(price: sort_order)
end 
```

在这个例子中，如果在`:include_metadata`有任何值，包括一个 falsey 值，它将被设置，这正是我们想要的。

## 如果没有找到想要的东西，运行一段代码

如我们所见，如果您在 hash 中没有找到您想要的东西，那么返回一个默认值是很好的。但是，有时简单的值不行，您可能需要运行一段代码作为后备。接受一个块来帮助你实现这一点，这是相当漂亮的！

在这个例子中，如果用户向 API 发送一个无效的排序顺序，我们将在分析服务中记录下来。这将帮助我们决定下一步是否要为我们的用户构建这个特性。如果很多用户请求在`popular`或`new`前订购列表，我们知道这一点会很方便。因此，我们将报告该值，然后返回默认值。

```
def index
  @include_seller_details = listing_params.fetch(:include_metadata, true)

  valid_sort_orders = {
    lowest_price: :asc,
    highest_price: :desc
  }

  sort_order = valid_sort_orders.fetch(listings_params[:sort]) do |value|
    Analytics.report("user requested to sort listings by #{value}")
    :asc
  end

  @listings = Listing.order(price: sort_order)
end 
```

## 用`Hash#fetch`更安全的处理环境变量

对环境变量处理不当会导致一些灾难性的结果(…问我怎么知道的)。被咬了不止一次，检索环境变量的时候喜欢用`Hash#fetch`。

如果您使用环境变量来突出标志发布，或者添加/删除环境变量是与您的正常代码部署过程分开处理的事情，这一点尤其重要。

代码期望一个环境变量+一个无声的失败，如果它不在那里+不能正确地设置一个环境变量=潜在的非常昂贵的错误

🙃

```
def index
  seasonal_discount = ENV["SEASONAL_DISCOUNT"]

  valid_sort_orders = {
    lowest_price: :asc,
    highest_price: :desc
  }

  sort_order = valid_sort_orders.fetch(listings_params[:sort], :asc)

  @listings = Listing.order(price: sort_order)

  if seasonal_discount
    @listings = @listings.map(&:apply_seasonal_discount)
  end
end 
```

在这种情况下，如果有人未能设置`SEASONAL_DISCOUNT`环境变量，我们可能不会注意到，因为这里没有任何东西会抛出错误。我们的顾客会错过一笔好交易！

我们可以使用`Hash#fetch`,这样当找不到 env 变量时，这个操作就会失败。

```
def index
  seasonal_discount = ENV.fetch("SEASONAL_DISCOUNT")

  valid_sort_orders = {
    lowest_price: :asc,
    highest_price: :desc
  }

  sort_order = valid_sort_orders.fetch(listings_params[:sort], :asc)

  @listings = Listing.order(price: sort_order)

  if seasonal_discount
    @listings = @listings.map(&:apply_seasonal_discount)
  end
end 
```

如果没有找到环境变量，这将会很麻烦地失败，您的代码甚至可能无法正确部署。如果这样做更有意义的话，您也可以选择设置默认值，但是在很多情况下，有噪声的失败更好。

### 这些是我最喜欢的`Hash#fetch`用法。对于任何 ruby 爱好者来说，这都是一个非常有用的工具，我认为记住`[]`不是从 Hash 中获取值的唯一方法是有好处的。
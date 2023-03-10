# 使用 Seed-Fu 简化 Rails 种子:一步一步的指南

> 原文：<https://dev.to/risafj/rails-seeds-made-simple-with-seed-fu-a-step-by-step-guide-ff2>

我在一个 Rails 应用程序中植入了一些默认数据，但是发现在不产生重复数据或者没有删除一些不必要的记录的情况下维护这些数据变得越来越困难。
然后我了解了 [`seed-fu`](https://github.com/mbleigh/seed-fu) gem，它帮助我避免了这样的陷阱，让语法看起来很干净。

在这篇文章中，我将描述我最初使用`seeds`时遇到的问题，并指导你如何使用`seed-fu`。如果你在种子中使用了大量的`find_or_intialize_by` / `find_or_create_by`，或者发现很难保持数据的完整性，这可能适合你。

# 问题

让我告诉你我在做什么的大致想法。
我想为`product_types`和`products`播种数据。每个`product`属于一个`product_type`。我的`seeds.rb`看起来是这样的(只是长一点，不那么傻):

```
# db/seeds.rb
# Seed product types
[
  {name: 'fruit', description: 'Juicy'}
  {name: 'vegetable', description: 'Get your vitamins'}
  {name: 'snack', description: 'Cheat day!'}
].each do |attributes|
  ProductType.find_or_initialize_by(name: attributes[:name]).update!(attributes)
end

# Seed products
[
  {product_type: ProductType.find_by(name: 'fruit'), name: 'apple'},
  {product_type: ProductType.find_by(name: 'fruit'), name: 'ooorange'},
  {product_type: ProductType.find_by(name: 'vegetable'), name: 'tomato'},
  {product_type: ProductType.find_by(name: 'snack'), name: 'chocolate'}
].each do |attributes|
  Product.find_or_create_by(attributes)
end 
```

Enter fullscreen mode Exit fullscreen mode

如果我只是更新一个`description`，我用`find_or_initialize_by`代替`product_types`来覆盖记录。例如，如果我将`fruit`的`description`从`'Juicy'`更改为`'Scrumptious'`并运行`rails db:seed`，Rails 将为现有的`fruit`记录更新`description`，而不是创建一个新记录。到目前为止，一切顺利。

但是后来，我注意到了`products`的一个问题。一个`product_type`可以有多个`products`，这意味着如果存在一个具有相同`product_type`的记录，我不能告诉 Rails 覆盖这个名称。注意到`orange`是怎么拼错的吗？如果您更正拼写并重新加载种子，名为`ooorange`的产品将保留，而另一个名为`orange`的产品将被创建。为了清理这个，你必须手动进入控制台，删除不必要的`product`，或者`reset`你的数据库——两者都不理想。

注意:我后来意识到，你可以为每条记录指定`id`，并用`id`作为键来更新你的记录，根本不用这个宝石。但是在这篇文章中，我将集中讨论使用`seed-fu`的解决方案。

# 用`seed-fu`管理你的种子

## 1。装置

将宝石添加到您的`Gemfile`并运行`bundle`。

```
gem 'seed-fu' 
```

Enter fullscreen mode Exit fullscreen mode

## 2。创建种子文件夹

从下面的两个选项中选择放置文件的位置；`seed-fu`从他们身上就知道读书。或者你可以设置一个自定义路径(查看[文档](https://github.com/mbleigh/seed-fu#where-to-put-seed-files))。

*   `#{Rails.root}/db/fixtures`
*   `#{Rails.root}/db/fixtures/#{Rails.env}`

## 3。创建种子数据文件

现在是实际数据——这是我们的种子重写后的样子。

```
# db/fixtures/product_types.rb
ProductType.seed(:name,
  {name: 'fruit', description: 'Juicy'}
  {name: 'vegetable', description: 'Get your vitamins'}
  {name: 'snack', description: 'Cheat day!'}) 
```

Enter fullscreen mode Exit fullscreen mode

```
# db/fixtures/product_types.rb
Product.seed(:id,
  {id: 1, product_type: ProductType.find_by(name: 'fruit'), name: 'apple'},
  {id: 2, product_type: ProductType.find_by(name: 'fruit'), name: 'ooorange'},
  {id: 3, product_type: ProductType.find_by(name: 'vegetable'), name: 'tomato'},
  {id: 4, product_type: ProductType.find_by(name: 'snack'), name: 'chocolate'}) 
```

Enter fullscreen mode Exit fullscreen mode

让我简单解释一下发生了什么。

对于`product_types`，我们不希望创建多个同名的记录，所以我们使用`:name`作为键(或“约束”)。如果带有那个`name`的`product_type`已经存在，那么`description`将被更新，而不是创建一个全新的`product_type`。

至于`products`，我们给每一个都指定了`id`。我们使用这个`:id`作为约束，并相应地创建/更新。因此，如果我们将拼写从`ooorange`更正为`orange`并重新加载种子，那么`product`的`name`将被更新，而`id`是`2`。这样，我们可以避免留下不正确的记录或意外创建记录的陷阱。

[文档](https://github.com/mbleigh/seed-fu#constraints)有更多关于使用这些约束的内容。我个人认为它比原版`seeds`更方便读者。

## 4。自动加载您的`seed-fu`数据

如果您想从命令行手动加载您的`seed-fu`数据，您可以使用`rails db:seed_fu`来完成。但是如果数据能够在你通常期望你的种子被加载的情况下自动加载，那就更方便了(比如`rails db:reset`)。

这再简单不过了。只要把这一行加到你的`seeds.rb` :

```
# db/seeds.rb
SeedFu.seed 
```

Enter fullscreen mode Exit fullscreen mode

还可以给它传递两个参数( [docs](https://github.com/mbleigh/seed-fu#rake-task) ):

*   夹具路径:以防您没有使用默认路径
*   过滤器:如果你只想加载某些文件

希望这有所帮助。感谢阅读！
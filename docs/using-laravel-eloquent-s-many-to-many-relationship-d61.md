# 使用 Laravel 口才的多对多关系

> 原文：<https://dev.to/chrisrhymes/using-laravel-eloquent-s-many-to-many-relationship-d61>

你花在阅读 Laravel 文档上的时间越多，你就会发现越多可以节省你时间和精力的东西。我已经使用 Laravel 很多年了，但是这个星期我发现我一直在以一种次优的方式使用多对多关系，我的意思是我不惜一切代价避免它。

这一定是由于我在大学学到的东西，但我记得有人教过我数据库关系，你应该避免表中的多对多关系，而应该在它们之间有一个链接或透视表。

所以每当我设计我的数据库时，我会避免直接在表之间多对多，并建立另一个表将它们链接在一起。当使用 Laravel 时，我将分别定义每个关系，因此从第一个表到链接表是一对多，然后从第二个表到链接表是一对多，从链接表到其他表是相反的。

就在本周，我发现有一种更简单的方法来定义和使用多对多关系，即正确地定义和使用多对多关系。为了更容易理解，我将使用一个例子。

## 多对多例子

因为我现在感觉很饿，所以让我们考虑一下面包和三明治馅料的种类，如果这也让你感到饥饿，我很抱歉(希望它能让你有学习的欲望……)。面包有很多种，三明治馅料也有很多种。一种面包，比如全麦面包，可以有很多三明治填充物，比如奶酪、火腿、生菜和马麦酱。但反过来，marmite 可以用在白面包，全麦面包，百吉饼等。因为马麦酱总是适合搭配各种面包。所以他们之间有多对多的关系。

## 创建模型、迁移和控制器

在 Laravel 中，我们需要为面包的类型创建一个模型，让我们称它为面包，因为 Laravel 对模型使用单数名称，并且为三明治填充物创建一个模型，称为 SandwichFiller。

首先，我们需要为这两者创建模型、迁移和控制器。我最近发现了另一个有用的快捷方式，您可以使用-mcr 一次性创建模型、迁移和资源控制器。

```
php artisan make:model Bread -mcr
php artisan make:model SandwichFiller -mcr 
```

Enter fullscreen mode Exit fullscreen mode

为了简单起见，我们只给每个表一个`name`字段，以及迁移中的 id 和时间戳。

接下来，我们还需要对链接表进行迁移，以将它们连接在一起。我可能是错的，但是我认为没有必要为链接表创建一个模型，因为我们不会直接访问它。

```
php artisan make:migration create_bread_sandwich_filler_table --create=bread_sandwich_filler 
```

Enter fullscreen mode Exit fullscreen mode

在这个迁移中，我们需要为`bread_id`和`sandwich_filler_id`添加一列，以及 id 和时间戳。注意名字是`bread_sandwich_filler`而不是`sandwich_filler_bread`。这是由于 Laravel 的命名惯例，它按字母顺序排列模型。

然后，我们可以运行 php artisan migrate 来创建这些新表。

## 定义关系

现在我们准备开始定义关系。如果我们打开面包模型，我们可以定义与三明治填充物的多对多关系。

```
class Bread extends Model
{

    public function sandwichFillers()
    {
        return $this->belongsToMany(SandwichFiller::class);
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以在三明治填充模型
中定义关系

```
class SandwichFiller extends Model
{

    public function breads()
    {
        return $this->belongsToMany(Bread::class);
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

即使我们已经创建了`bread_sandwich_filler`表，我们也不需要模型或以任何方式定义与它的关系。它只是工作。

## 检索关系

现在我们有了迁移和模型，我们可以检索关系。对于面包，我们可以做一些事情，如

```
$bread = Bread::find($id);

foreach($bread->sandwichFillers as $filler) {
    echo $filler->name;
} 
```

Enter fullscreen mode Exit fullscreen mode

和三明治灌装机

```
$sandwichFiller = SandwichFiller::find($id);

foreach($sandwichFiller->breads as $bread) {
    echo $bread->name;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 同步关系

这是真正节省我时间的一点，如果你愿意，三明治里的肉，也是我想与你分享这个的原因。您可以通过向表中传递一个 id 数组来使用 sync，而不是手动添加和删除表中的条目。

如果我们有一个三明治填充物的列表，我们需要将它与一种面包联系起来，例如奶酪、火腿、泡菜和生菜，添加到硬皮面包中，以制作一个农夫三明治，我们可以使用 sync 来完成这项工作。

如果我们知道奶酪的 id 是 1，火腿的 id 是 2，泡菜的 id 是 3，生菜的 id 是 4，我们可以做如下的事情。

```
$bread = Bread::whereName(‘Crusty Bread’)->first();

$bread->sandwichFillers()->sync([1, 2, 3, 4]); 
```

Enter fullscreen mode Exit fullscreen mode

如果我们决定用 marmite(id 为 5)代替 pickle，我们可以简单地做以下事情:

```
$bread->sandwichFillers()->sync([1, 2, 3, 5]); 
```

Enter fullscreen mode Exit fullscreen mode

这比试图手动确定哪些项目需要从`bread_sandiwch_filler`表中添加或删除要容易得多。它会替你处理所有的逻辑。当我发现这一点时，我可以从我的控制器中删除大约 10 行代码，并用一行 sync 命令替换它。

我希望你觉得这很有趣，并能在未来为你节省一些时间，但如果它让你想要一个三明治，我很抱歉。
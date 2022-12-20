# 一次创建多个模型对象

> 原文：<https://dev.to/thedavefulton/creating-multiple-model-objects-at-once-3d97>

免责声明——我对 Laravel 还相当陌生，所以我很乐意承认可能有其他/更好的方法来做这件事，或者这已经是众所周知的了。但是稍微谷歌一下似乎就表明了另外一种情况

所以今天我遇到了一点挑战。我有一个父对象，我创造性地称之为“应用程序\父对象”，它有一堆子对象，我称之为“应用程序\子对象”？

是啊，这很管用。

所以这些子元素基本上只是一堆文件名，POST 请求已经将它们放入了一个数组中，所以我想用某种类型的`Model::Create()`方法来保存这个数组，但是`Create()`似乎更喜欢一组属性，非常感谢。

一些四处寻找的人发现了使用`Insert()`方法的解决方案。这将允许我插入尽可能多的记录，但显然不会设置时间戳。我不想手动操作。或者说，我不想浪费服务器的时间手动完成。

就在那时，我偶然发现了解决方案。如果你有一个 HasMany 关系，你可以使用*上的`CreateMany()`方法，即*。我给你举个例子。假设我有几个与 HasMany/BelongsTo 相关的基本模型。

```
class Parent extends Model
{
    public function children()
    {
        return $this->hasMany('App\Child');
    }
}

class Child extends Model
{
    public function parent()
    {
        return $this->belongsTo('App\Parent');
    }
} 
```

我可以利用这种关系一次性创造出我想要的所有孩子。假设我收到一个带有`'parentName'`和一个数组`'childNames'`的请求。我可以这样处理:

```
class ParentController extends Controller
{
    public function store(Request $request)
    {
        $parentName = $request->get('parentName');
        $childNames = $request->get('childNames');
        // Remember, $childNames is already an array

        $parent = Parent::create(['parent_name' => $parentName]);
        $parent->children()->createMany($childNames);
    }
} 
```

就是这样。还有一个小小的额外特性是，您甚至不必将`'parent_id'`作为属性传入；它是自动处理的。正如我之前所说，这可能都是常识，但如果不是，我希望这至少可以更容易为下一个人找到！

黑客快乐！
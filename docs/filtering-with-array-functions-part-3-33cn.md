# 用数组函数过滤[第 3 部分]

> 原文：<https://dev.to/anastasionico/filtering-with-array-functions-part-3-33cn>

在本教程的示例中，您将学习如何使用数组函数过滤元素

这是本系列的第三章，在这一章中，我们将分解关于数组函数的整个其他部分。

这部分是关于从数组中过滤元素的。

在很多情况下，我们的脚本只需要数组中的某些元素，而其他元素，除了对我们的目的无用之外，还会使我们的应用程序更慢，代码更混乱。

PHP 提供了几个函数，**允许你只过滤我们想要在**上工作的元素，使得我们的代码更容易阅读和工作。
T3】

## 关于级数

这篇属于“ **PHP 数组曝光**”系列的博文。

如果你还没有读过其他的文章

看看它们:

[PHP 数组暴露(上)](http://anastasionico.uk/blog/php-array-functions-exposed)

[数组的创建和操纵](http://anastasionico.uk/blog/array-function-in-php)

目录
[array _ filter()](http://anastasionico.uk/blog/filtering-with-array-functions#filter)
[array _ reduce()](http://anastasionico.uk/blog/filtering-with-array-functions#reduce)
[array _ intersec()](http://anastasionico.uk/blog/filtering-with-array-functions#intersec)
[array _ intersect _ assoc()](http://anastasionico.uk/blog/filtering-with-array-functions#interassoc)
[array _ diff _ assoc()](http://anastasionico.uk/blog/filtering-with-array-functions#diffassoc)
[max()](http://anastasionico.uk/blog/filtering-with-array-functions#max)
[min()](http://anastasionico.uk/blog/filtering-with-array-functions#min)
[count()&size of()](http://anastasionico.uk/blog/filtering-with-array-functions#count)
[结论](http://anastasionico.uk/blog/filtering-with-array-functions#conclusion)

## 数组 _ 过滤器()

如前所述，这是本系列中关于数组中元素过滤的部分，所以让我们从一个经典的例子开始:

*array_filter()*

这个函数需要一个强制参数，即我们要应用过滤器的数组，以及另外两个不必要的参数。

第一个参数最好是插入它，它由一个回调函数组成，第二个非强制参数由决定键或键值对使用的标志组成。

我们进入正题， *array_filter()* 是如何工作的？

这个函数循环数组中的所有元素作为第一个参数，并把它们一个接一个地传递给作为回调函数的数组函数。

如果在对元素求值之后，函数返回 true，那么如果没有被过滤掉，则该值将返回到数组内部。

请注意，数组键被保留。

如果你已经看过这个系列的几集，你就已经知道我是用简单的引用来理解的。

在这种情况下，我会无耻地从 PHP.net 手册中偷一个例子，

原因很简单，我认为所提出的例子对于初学者来说是相当困难的，我借此机会用简单的话来解释它。

下面是提出的例子:

```
function odd($var) 
{ 
    // returns whether the input integer is odd 
    return($var & 1); 
} 
function even($var) 
{ 
    // returns whether the input integer is even 
    return(!($var & 1)); 
} 

$array1 = array("a"=>1, "b"=>2, "c"=>3, "d"=>4, "e"=>5);
$array2 = array(6, 7, 8, 9, 10, 11, 12); 

echo "Odd :\n"; 
print_r(array_filter($array1, "odd")); 
echo "Even:\n"; 
print_r(array_filter($array2, "even")); 
Odd : 
Array ( [a] => 1 [c] => 3 [e] => 5 ) 
Even: 
Array ( [0] => 6 [2] => 8 [4] => 10 [6] => 12 ) 
```

在上面的例子中，函数 *array_filter()* **将一个数组作为参数，并调用函数**(第二个参数)，该函数反过来计算作为属性传递的元素，并返回一个布尔值。

这是什么？

回报(var & 1 美元)；

这是按位的 AND 运算。

所有偶数的最低有效位设置为 0，而所有奇数的最低有效位设置为 1。

所以它只是简单地将两个数“与”在一起。

例如，它在二进制中表示为 11。11 & 01 = 01 = >真，所以是奇数。

2，而是用二进制表示为 10。10 & 01 = 00 = >假，所以是偶数。

我已经在一篇关于 PHP 基础的文章中描述了位操作符。它肯定会增加你的技能。

[点击此处了解关于按位的更多信息](http://anastasionico.uk/blog/php-operators#bitwise)

## 

## 

## array_reduce()

关于这个数组函数，网上有很多混乱和错误的信息。

这使得它成为我职业生涯中使用最少的功能之一。

***【array _ reduce()*需要一个回调函数，这使得它比一般情况下更复杂，更困难的部分是回调需要两个参数才能工作。**

让我们来看看，

这个函数所做的是使用回调函数迭代地**将数组缩减为单个值。**

这是什么意思？

简单来说，**这个函数接受一个数组，在根据回调处理元素之后，从数组中返回一个变量。**

可以想象，这个函数至少有两个参数，第一个是要处理的数组，第二个是完成任务的函数回调。

第三个参数涉及到我们希望在回调中开始的初始值，但是，并不强制使用它。

```
function addComma($e1,$e2) 
{ 
    return $e1 . ", " . $e2; 
} 

$dwarfs = array('Doc', 'Grumpy', 'Happy', 'Sleepy', 'Dopey', 'Bashful', 'Sneezy');

print_r(array_reduce($dwarfs,"addComma")); 
// Doc, Grumpy, Happy, Sleepy, Dopey, Bashful, Sneezy 
print_r(array_reduce($dwarfs,"addComma", "Dwarfs")); 
// Dwarfs, Doc, Grumpy, Happy, Sleepy, Dopey, Bashful, Sneezy 
```

如果你想了解更多关于这个函数的知识，这里有一篇 Gordon Forsythe 的文章:[深入探讨了 PHP 中使用 array_reduce](https://medium.com/@baohx2000/reaching-deep-into-arrays-using-array-reduce-in-php-9ff9e39a9ca8) 的数组

## 

## array_intersec()

我们已经从一个相当复杂的函数发展到一个非常简单的函数。

array_intersec() 事实上非常简单，是 PHP 4.0.1 中首次发布的第一批可用函数之一。

**它的任务是过滤一个数组的元素，表示为第一个强制参数，通常称为主数组，对照一个或多个选择的数组作为后续参数。**

如果一个元素存在于主数组中，同时也存在于其他数组中，那么它将被返回到结果数组中。

```
$dwarfs1 = array('Doc', 'Grumpy', 'Happy', 'Sleepy', 'Dopey', 'Bashful', 'Sneezy'); 
$dwarfs2 = array('Doc', 'Dopey');
$dwarfs3 = array('Doc', 'Grumpy', 'Dopey', 'Bashful', 'Sneezy'); 
$result=array_intersect($dwarfs1, $dwarfs2, $dwarfs3);
print_r($result); 
Array ( [0] => 'Doc', [1] => 'Dopey' ) 
```

请注意，要被视为相等的元素，它们之间的比较值===必须为 true。

## 数组 _ 交集 _ 关联()

这个函数是在前一个函数之后不久出现的，和前一个函数一样，它继承了非常简单的语法来理解。

*array_intersect_assoc()* 取最少 2 个数组作为参数，**用其他的**过滤第一个数组，称为 master。

如果所有提供的数组中的键和值都相同，那么它们将被添加到函数返回的数组中。

```
$dwarfs = array( 
    'first' => 'Grumpy', 
    'second' => 'Happy', 
    'third' => 'Sleepy', 
    'fourth' => 'Dopey', 
    'fifth' => 'Bashful', 
    'sixth' => 'Sneezy', 
    'seventh' => 'Doc' 
); 
$dwarfs2 = array( 
    'first' => 'Grumpy', 
    0 => 'Happy', 
    1 => 'Dopey', 
    2 => 'Bashful', 
    'sixth' => 'Sneezy', 
); 
$dwarfs3 = array( 
    'first' => 'Grumpy', 
    0 => 'Dopey', 
    'sixth' => 'Sneezy', 
); 
$result= array_intersect_assoc($dwarfs1, $dwarfs2, $dwarfs3);
print_r($result); 
Array ( ['first'] => 'Grumpy', ['sixth'] => 'Sneezy') 
```

如您所见，过滤只返回了数组中所有元素的 2 个。

在所有三个数组中都有一对*[‘第一’]=>，【暴躁的】和[‘第六’]=>‘喷嚏精’。*

您还可以注意到，在所有数组中都有一个值“Dopey ”,但是它有不同的键，所以它不会被计算。

## array_diff_assoc()

与上面解释的函数相关的另一个函数是“数组过滤器”类别中的另一个函数。

*array _ ui intersect()*的工作方式与 *array_intersect()* 非常相似，但不同之处在于**有一个回调函数，表示为最后一个参数，它比较**提供的数组元素。

```
function comparison($a,$b) 
{ 
    return $a ⇔ $b; 
} 

$dwarfs1 = array('Doc', 'Grumpy', 'Sneezy'); 
$dwarfs2 = array('Doc', 'Sleepy', 'Dopey', 'Bashful', 'Sneezy'); 

print_r(array_uintersect($dwarfs1, $dwarfs2, "comparison"));
Array ( [0] => 'Doc', [1] => 'Sneezy') 
```

注意，从 PHP 5 开始，该语言提供了函数 *array_intersect_assoc()* ，它的工作方式类似于 *array_intersect()* ，但是它**比较键和值**。

array_intersect 和 array_diff 家族非常庞大，总共有 10 个元素，它们在工作方法和语法上都非常相似，所以如果您决定深入研究它们，这里有一个列表和一些小提示:

*   数组 _ 交集，
*   数组 _ 交集 _ 关联，
*   数组 _ 交集 _ 关键字，
*   数组 _ 交集 _ uassoc，
*   数组 _ 交集 _ ukey，
*   数组 _ 差异
*   数组 _ 差异 _ 关联，
*   数组 _ 差异 _ 关键字，
*   array _ diff _ uassoc，
*   array_ diff_ ukey

图例:

**交集**:计算交集；

**Diff** :计算差值；

**Assoc** :评估键和值；

**Uassoc** :回调函数用于指标比较；

**Ukey** :对按键求值；

## 

## max()

在您的职业生涯中，肯定会发生这样的情况，您将不得不至少一次**在一系列变量**之间或者一个数组中的元素之间寻找最大值，

PHP 4 为您提供了自己的程序员特性，使这种搜索变得更加容易。

*max()* 函数将一个数组作为一个参数，或者两个或更多不同类型的变量，**对它们进行比较，返回最大值**。

您还可以评估不同类型的变量，它们将使用标准的比较规则进行评估。

```
$maxNumber = max(2, 3, 1, 6, 7); 
// 7 
$maxArrayElement = max([2, 4, 5]);
// 5 

$maxDwarfs = ['Doc', 'Grumpy', 'Happy', 'Sleepy', 'Dopey', 'Bashful', 'Sneezy']; 
// 'Doc' 

// The string 'hello' when compared to an int is treated as 0 
$maxIntString = max(1, 'Doc'); 
// 1 

// Multiple arrays compares the elements from left to right 3 < 4 
$maxArray = max([1, 2, 3], [1, 2, 4]); 
// [1, 2, 4] 

// The value 0 is evaluated as false whereas TRUE is evaluates as 1 
$maxIntBool = max(0, TRUE); 
// TRUE 
```

## min()

*min()* 函数查找一组值中的最小值。

基本上，**如果你把写在关于 *max()* 函数部分的所有内容反过来，你会得到 *min()*** 的结果。

## 

count() & sizeof()

如果你正在处理一个数组元素或者一个实现了 PHP 可数函数的内置接口**的对象，你可以使用数组 *count()* 函数。**

*count()* 取 2 个值作为参数，第一个是要计数的变量，第二个参数不是强制的，由一个定义计数方法的标志组成。

该标志可以有两个值:COUNT_NORMAL 和 COUNT_RECURSIVE，第二个值在

关联数组。

```
$dwarfs = ['Doc', 'Grumpy', 'Happy', 'Sleepy', 'Dopey', 'Bashful', 'Sneezy']; 
var_dump(count($dwarfs)); 
// int(7) 
$disney = [
    "dwarfs" => ['Doc', 'Grumpy', 'Happy', 'Sleepy', 'Dopey', 'Bashful', 'Sneezy'], 
    "originals" => ["Mickey Mouse","Pete", "Goofy", "Minnie Mouse", "Pluto"] 
]; 

// Normal count 
var_dump(count($disney)); 
// int(2) 
// Recursive count 
var_dump(count($disney)); 
// int(14) 
```

[官方手册上关于计数功能的更多信息](https://www.php.net/manual/en/function.count.php)

## 

## 结论

如您所见， **PHP 已经并将继续提供给我们的数组函数组合非常广泛**，

在本系列的这一部分中，我们已经看到了极其简单的函数，例如，一对 *min()* 和 *max()* ，以及其他更深入细节的函数，如 *array_ intersect_ uassoc()* 。

好消息是，没有任何工作和空缺职位需要你记住所有这些功能。

另一个好消息是，现在你已经理解了它们，**你可以在你的浏览器上标记这篇文章，并随时返回**。

在讨论完过滤器之后，在本系列的下一章，您将看到构成 PHP 函数基础的另一部分。

如何在里面订购物品？

如果你今天学到了一些新东西，而你还没有这样做，那么**订阅时事通讯，以便在下一章发表时得到通知**。
T3】

***

如果你喜欢这篇文章和下一组关于数组函数的例子，请继续关注并订阅时事通讯，我们会通知你的。

如果你想学习更多关于 PHP 数组函数的知识，可以点击下面的链接，看看本系列已经发布的教程。

[PHP 数组暴露(上)](http://anastasionico.uk/blog/php-array-functions-exposed)

[数组的创建和操纵](http://anastasionico.uk/blog/array-function-in-php)

***

[![get the weekly book's review](img/b5f72271601563e8dc6e9a77ad8c785f.png)](https://anastasionico.us12.list-manage.com/subscribe?u=58fde30bf74e04ab41c7afea8&id=13c59ad6f4)

***

[![](img/785ca08ebbf3af6092f309af6d7e071f.png)](http://treehouse.7eer.net/c/1374240/294479/3944)

### 学习编码，获得一项新技能，获得一份新工作

#### 无论你的目标是什么——树屋都能帮你实现

他们目前提供 4 个月的免费服务(价值 100 美元)。

[看一看吧！。](http://treehouse.7eer.net/c/1374240/294479/3944)

([附属链接](http://treehouse.7eer.net/c/1374240/294479/3944)
# PHP 中的数组函数

> 原文：<https://dev.to/anastasionico/array-function-in-php-1fo3>

在本系列的第一部分中，您看到了 PHP 语言提供的一些最常用的数组函数，

幸运的是，经过年复一年的发展和语言新版本的发布，这个数组函数的列表仍然很长，似乎永远不会结束。

让我们看看其他的**数组函数**，下面列表中的那些不太常见，但是在这里你会成为 PHP 语言的专家。

## 关于级数

这篇属于“ **PHP 数组曝光**”系列的博文。

如果你还没有读过其他的文章

看看它们:

[PHP 数组暴露(上)](http://anastasionico.uk/blog/php-array-functions-exposed)

[数组的创建和操纵](http://anastasionico.uk/blog/array-function-in-php)

## 数组 _ 反转()

array_reverse()函数是一个有趣的函数，同时也非常有用，而且非常容易使用。

实际上，array_reverse()将一个数组作为第一个也是唯一一个强制参数，**反转给定数组中元素的顺序**。

该函数还有另一个参数，在这种情况下是可选的，由 bool 类型的变量组成，**如果第二个参数为真，数字键将被保留**如果参数为假，或者参数未被提供，则顺序将根据数组中存在的元素进行排列。

注意，如果键不是数字的，它们总是被保存，第二个参数对它们没有影响。

```
$dwarfs = array('Doc', 'Grumpy', 'Happy', 'Sleepy', 'Dopey', 'Bashful', 'Sneezy');
$sfrawd = array_reverse($dwarfs); 
print_r($sfrawd); 
Array ( 
    [0] => Sneezy 
    [1] => Bashful 
    [2] => Dopey 
    [3] => Sleepy 
    [4] => Happy 
    [5] => Grumpy 
    [6] => Doc ) 
```

## 

## 数组 _ 翻转()

array_flip 函数将一个数组作为参数，**返回一个数组，在这个数组中它将密钥交换为值**，这可以被看作是一种原始的方法来获得一个数组的密钥或者用于许多不同的目标。

这个函数也有一些限制，例如，**键必须遵循精确的类型**，因为 PHP 命令键只能是整数或字符串，否则你会收到一个警告，这个函数将无法工作。

你需要注意的另一个特性是相同密钥的数量，事实上，只有其中的最后一个会被使用，其他的都会丢失。

```
$dwarfs = array('Doc', 'Grumpy', 'Happy', 'Sleepy', 'Dopey', 'Bashful', 'Sneezy');
$flipped = array_flip($dwarfs); 
Array ( 
    [Doc] => 0 
    [Grumpy] => 1 
    [Happy] => 2 
    [Sleepy] => 3 
    [Dopey] => 4 
    [Bashful] => 5 
    [Sneezy] => 6 ) 
```

[以下是关于 array_flip 的其他例子](https://blog.kartones.net/post/php-is-array-and-array-flip/)

## array_rand()

诚然，函数 array_rand()有点奇怪，如果你决定使用它**，你必须注意你想要获得的结果是以选择的格式**。

这个函数确实可以返回一个数组、一个整数甚至一个字符串。

array_rand()的任务是从数组中的元素获取并**返回一个或多个键。**

这个函数是在 PHP 4 中插入的，已经很老了，但是从那以后已经修改过几次了。

例如，从 PHP 7.1 开始，array_rand()使用一种叫做 Mersenne Twister 的新算法来生成它的数字。

尽管这种算法随机取数，但它不适用于加密目的。

```
$dwarfs = array( 
    'zero' => 'Doc', 
    'first' => 'Grumpy', 
    'second' => 'Happy', 
    'third' => 'Sleepy', 
    'fourth' => 'Dopey', 
    'fifth' => 'Bashful', 
    'sixth' => 'Sneezy' 
);

print_r(array_rand($dwarfs, 1)); 
'Second' print_r(array_rand($dwarfs, 2)); 
Array ( 
    [0] => 'second' 
    [1] => 'fifth' 
) 
```

## 

## 洗牌()

尽管相似，shuffle()函数在结果上不同于前一个函数。

**shuffle()对数组**中出现的元素进行随机化，这些元素通过引用传递，而不是通过值传递，同时根据 shuffle 是否成功返回一个布尔值(真或假)。

该函数使用 Marsenne Twister 算法，这使得它不安全，并且肯定不适合用于加密目的。

```
$dwarfs = array('Doc', 'Grumpy', 'Happy', 'Sleepy', 'Dopey', 'Bashful', 'Sneezy');
print_r(shuffle($dwarfs)); 
Array ( 
    [0] => Happy 
    [1] => Doc 
    [2] => Sleepy 
    [3] => Grumpy 
    [4] => Bashful 
    [5] => Sneezy 
    [6] => Dopey 
) 
```

## 

## 数组 _ 替换()

**array_replace()替换数组**中的元素。

它有两种不同类型的参数，我们称之为输入参数和替代参数，

输入参数是一个数组类型参数，是我们想要处理的数组，替换参数由一个或多个数组组成，这些数组包含我们想要用来替换第一个参数的元素。

如果第二个数组中的键与第一个数组中的键相同，那么第二个数组的值将替换第一个数组的值。

如果有第三个数组，并且它的键也存在于第二个和第一个数组中，则第三个数组的值将替换前两个值。

看看下面的例子。

```
$dwarfs = array('Doc', 'Grumpy', 'Happy', 'Sleepy', 'Dopey', 'Bashful', 'Sneezy');
$originals = array(0 => "Mickey Mouse", 4 => "Pete"); 
$originals2 = array(0 => "Goofy"); 
$disney= array_replace($dwarfs, $originals, $originals2); 
print_r($disney); 
Array ( 
    [0] => Goofy 
    [1] => Grumpy 
    [2] => Happy 
    [3] => Sleepy 
    [4] => Pete 
    [5] => Bashful 
    [6] => Sneezy 
) 
```

**array_replace()不是递归的**，这意味着这个函数不会递归到数组中，也不会对内部值应用相同的过程。如果需要这个功能，可以使用 array_replace_recursive()并为每个数组指定一个键

[这里有一个简短的视频，解释了 array_replace 的核心](http://mauricemutetingundi.blogspot.com/2017/07/php-array-replace-function.html)

## 

## array_pad()

当你阅读手册的时候，你会发现这个数组函数有点难以理解，我花了几分钟才找到它在现实世界中的用途，但是我相信只要有一点想象力，你就会发现它非常有用。

array_pad()将指定类型的元素(可以是整数、字符串等)添加到数组中。

它考虑了三个参数，第一个是输入数组，

**第二个参数包含我们希望数组变成的最终大小**(这很棘手，所以要小心，如果数字是正数，我们在数组的末尾添加值，如果数字是负数，我们在输入数组的开头添加值)。

最后，第三个参数是要添加的值。

请注意，如果使用数字键作为第三个参数来填充关联数组，您的键将被重新编号。

```
$dwarfs = array('Doc', 'Grumpy', 'Happy', 'Sleepy', 'Dopey', 'Bashful', 'Sneezy');
$padRight = array_pad($dwarfs , 10, 'Snow White'); 
Array ( 
    [0] => Doc 
    [1] => Grumpy 
    [2] => Happy 
    [3] => Sleepy 
    [4] => Dopey 
    [5] => Bashful 
    [6] => Sneezy 
    [7] => Snow White 
    [8] => Snow White 
    [9] => Snow White 
) 
$dwarfs = array('first' => 'Doc', 'second' => 'Grumpy'); 
$padLeft = array_pad($dwarfs , -5, 'Snow White'); 
Array ( 
    [0] => Snow White 
    [1] => Snow White 
    [2] => Snow White 
    [first] => Doc 
    [second] => Grumpy 
) 
```

[从官方手册中阅读关于 array_pad()的内容](https://www.php.net/manual/en/function.array-pad.php)

## 范围()

另一种自动创建数组的方法是使用 array_range()函数，这非常简单，需要三个参数。

第一个是初始值，可以是整数也可以是字母，

**第二个参数是最终值，也可以是整数或字母，允许数组停止**。

相反，第三个参数不是强制的，它由三个步骤的长度组成，如果没有输入，PHP 默认将其作为一个值为 1 的整数。

```
foreach (range(1, 7) as $number) { echo $number; } 
// array(1, 2, 3, 4, 5, 6, 7) 

foreach (range('a', 'g') as $letter) { echo $letter; } 
// array('a', 'b', 'c', 'd', 'e', 'f', 'g'); 
```

## 

## array_fill()

这里是另一个函数，它的工作是将值添加到一个数组中，

**array_fill()比之前看到的函数要简单得多。**

它也有三个参数，

第一个由键的初始值组成，必须是整数，可以是正数也可以是负数，

第二个表示数组中元素的总数，它也必须是一个整数，

最后，我们有我们想要插入的值，它可以是混合类型的。

```
$dwarfs = array_fill(17, 3, 'Doc');
Array ( 
    [17] => Doc 
    [18] => Doc 
    [19] => Doc 
) 

$dwarfs = array_fill(-2, 3, 'Doc'); 
Array ( 
    [-2] => Doc 
    [0] => Doc 
    [1] => Doc 
) 
```

## 

## array_fill_key()

从上面的例子中可以看出，使用带有负值的 array_fill()得到的结果并不理想，**克服这一障碍的一种方法是手动定义键**。

我们能做到这一点要感谢函数:array_fill_key()。

它在 PHP 5.2 中首次发布，只考虑了两个参数，一个包含要使用的键的数组和第二个包含要插入的值的混合型参数。

显然，像它的老兄弟 array_fill()一样，它也返回一个数组。

```
$dwarfs = array_fill_keys(range(-2,1),'Doc');
Array ( 
    [-2] => Doc
    [-1] => Doc
    [0] => Doc
    [1] => Doc
)

$dwarfs = array('Doc', 'Grumpy', 'Happy', 'Sleepy', 'Dopey', 'Bashful', 'Sneezy');
$result = array_fill_keys($keys, 'Snow White');
Array ( 
    [Doc] => Snow White
    [Grumpy] => Snow White
    [Happy] => Snow White
    [Sleepy] => Snow White
    [Dopey] => Snow White
    [Bashful] => Snow White
    [Sneezy] => Snow White
) 
```

## 数组 _ 合并

PHP5 版本中最酷的数组函数之一无疑是 array_combine()，

这也是“创建”功能的一部分。

它接受两个数组类型的参数，并返回一个关联数组，其中第一个参数的元素作为键，第二个数组的元素作为值。

显然，您必须小心将被用作键的元素是有效的(整数或字符串)，

此外，如果键中有重复项，最后一个将覆盖其他键。

```
$keys = array('dwarfs', 'original', 'dwarfs', 'dwarfs', 'original', 'dwarfs', 'original');
$values = array('Doc', 'Mickey Mouse', 'Grumpy', 'Happy', 'Pete', 'Sleepy', 'Pluto');
array_combine($keys, $values);
Array ( 
    [dwarfs] => Doc
    [original] => Mickey Mouse
    [dwarfs] => Grumpy
    [dwarfs] => Happy
    [original] => Pete
    [dwarfs] => Sleepy
    [original] => Pluto
) 
```

## 数组 _ 行走()

通常，将回调作为参数的函数看起来很可怕，array_walk()就不那么可怕了。

它遍历数组的所有元素，并驱动一个函数来修改它们。

它接受 3 个参数，其中前两个是强制的，源数组和回调函数，第三个可以是不同的类型，可以在回调中使用。

如果循环结束时没有错误，array_walk()返回 true 布尔值，否则返回 false。

通过使用&$value 将值的参数指定为引用，可以在回调函数中更改元素的值。

```
function setString($value,$key,$sentence)
{
    echo "$value $sentence $key";
}
$dwarfs = array(
    'first' => 'Grumpy', 
    'second' => 'Happy', 
    'third' => 'Sleepy', 
    'fourth' => 'Dopey', 
    'fifth' => 'Bashful', 
    'sixth' => 'Sneezy',
    'seventh' => 'Doc'
);

array_walk($dwarfs,"setString"," is the ");
/*
Grumpy is the first
Happy is the second
Sleepy is the third
Dopey is the fourth
Bashful is the fifth
Sneezy is the sixth
Doc is the seventh
*/ 
```

array_walk()有一个弟弟叫做 array_walk_recursive()，

它已经到了 PHP 的第 5 版。

它们共享相同的格式和几乎相同的功能，但是通过使用递归，你可以处理更深层次的数组(也称为矩阵或数组内部的数组)

## 列表()

我在这里有点作弊，因为 list()不是一个数组函数，而是一个实际的 PHP 构造，

我在这篇文章中使用 list()函数的原因是，当我第一次遇到它时，它让我感到困惑，它有一个很少使用的特性，同时，它的格式不同于语言中的任何其他元素。

(可能只是我的印象吧，在评论里告诉我)。

然而，

正如手册所说，list()将变量赋值，就像它们是一个数组一样，

但这意味着什么呢？

简单来说，

它将一个数组作为参数，从左开始分解它的所有元素(PHP5 和 minors 从右开始),并根据它们的位置，将它们放入我们当时定义的变量中，你也可以通过留下一个空格来跳过一个元素。

```
$disney = array('Doc', 'Grumpy', 'Mickey Mouse', 'Pete');

list($first, $second, $third, $fourth) = $disney;
echo "$first and $second are friends whereas $third and $fourth are enemies";
// Doc and Grumpy are friends whereas Mickey Mouse and Pete are enemies
list($first, $second,, $fourth) = $disney;
echo "$first and $second are friends whereas $fourth has no enemies";
// Doc and Grumpy are friends whereas Pete has no enemies 
```

这里有一个由 Sebastian De Deyne 深入解释的列表函数示例

## 结论

正如我在上一集所说的，数组函数是一个很大的难题。

好消息是，了解所有这些并不是必要条件。

即使在最高层也没有。

虽然知道它们的存在对你来说很重要，但你可以随时使用本指南的这一部分和其他部分作为参考，并随时回到这里。

如果您喜欢这篇文章和下一组关于数组函数的例子，请继续关注并订阅时事通讯，我们会通知您的。

如果你想学习更多关于 PHP 数组函数的知识，可以点击下面的链接，看看本系列已经发布的教程。

[PHP 数组暴露(上)](http://anastasionico.uk/blog/php-array-functions-exposed)
[数组的创建和操作](http://anastasionico.uk/blog/array-function-in-php%5B%5D())

* * *

[![3944-517995.png](img/c1c45f737cc16b5e4ecacbd78d775ae4.png)](http://treehouse.7eer.net/c/1374240/294479/3944)

[学习编码，获得一项新技能，获得一份新工作](http://treehouse.7eer.net/c/1374240/294479/3944)
[无论你的目标是什么，树屋都会带你去那里](http://treehouse.7eer.net/c/1374240/294479/3944)

他们目前提供 4 个月的免费服务(价值 100 美元)。

看一看吧！
(附属链接)

* * *

[![subscribe-Medium.jpg](img/9cb2f543deec991b7617c53d43820419.png)](http://eepurl.com/dIZqjf)
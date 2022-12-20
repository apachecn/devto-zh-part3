# PHP 中的数组函数[第 2 部分]

> 原文：<https://dev.to/anastasionico/array-function-in-php-part-2-5864>

### 使用函数排序数组

如果您已经阅读了这个关于数组函数的系列文章，我相信您已经学到了很多。

如果你没有(真为你感到羞耻！)下面是一个快速回顾:

[在第一章中，我们看到了数组是什么样子，使用什么语法以及它是为](http://anastasionico.uk/blog/php-array-functions-exposed)而制造的，

在第二章中，[我们关注了最常用的功能](http://anastasionico.uk/blog/array-function-in-php)，或者至少是那些被认为最流行的功能，

在迄今为止发表的第三和最后一章中，[我们关注元素优化，我们通过阅读关于过滤函数](http://anastasionico.uk/blog/filtering-with-array-functions)来做到这一点。

在本系列的这一章中，您将看到关于数组的另一个基本部分。

事实上，在这篇文章中，你将会学到所有关于对数组中的元素进行排序的知识。

并且您将会以最快的方式完成它，也就是说，使用 PHP 核心中的函数。

PHP 的核心是用 C 语言编写的，C 语言是一种低级编程语言，因此比 PHP 本身更快

事实上，PHP 语言提供了几个用低级语言编写的内置函数，这使得您将要编写的脚本的执行速度更快，

更不用说功能已经存在，随时可以使用，所以为什么要重新发明轮子呢？

### 排序()

让我们从对数组中的元素进行排序时最符合逻辑的函数开始。

*sort()* 函数**将一个数组**作为参数，你猜对了。

如果里面的元素是一个数字，它将按照从最小到最大的顺序对元素进行排序，如果是字符串，则按照字母顺序进行排序。

即使一点也不流行，但是，我们必须注意到在这个函数中还有第二个参数，

**该参数不是强制性的，它由一个或多个标志**组成，这些标志定义了给定数组必须如何排序。

出现的标志有:

**SORT_REGULAR** —项目正常出现，标准评估；

**SORT_NUMERIC** —出现使用数值法的项目；

**SORT_STRING** —项目显示为字符串；

[这里是关于 locale 类的更多信息](https://www.php.net/manual/en/class.locale.php)

**SORT _ NATURAL**—使用“自然排序”将项目作为字符串进行比较[看看 *natsort()* 函数](http://anastasionico.uk/blog/sorting-arrays-element-using-php-functions#natsort)；

**SORT _ FLAG _ CASE**—可与 SORT_STRING 或 SORT_NATURAL 组合使用(按位或),对字符串进行不区分大小写的排序；

这个函数将数组作为一个参数而不是值来引用，这涉及到两个重要的因素。

第一个是**数组本身改变的不是它的副本，也不会创建副本**(所以要小心修改你不想改变的数组)，

这允许第二个函数返回一个不同的变量。

在这种情况下，变量是布尔类型的，指示函数是否成功执行。

```
$dwarfs = ['Doc', 'Grumpy', 'Happy', 'Sleepy', 'Dopey', 'Bashful', 'Sneezy']; 
sort($dwarfs); 
foreach ($dwarfs as $index => $name) { 
    echo "dwarfs[" . $index . "] = " . $name . "\n"; 
} 
dwarfs[0] = Bashful 
dwarfs[1] = Doc 
dwarfs[2] = Dopey 
dwarfs[3] = Grumpy 
dwarfs[4] = Happy 
dwarfs[5] = Sleepy 
dwarfs[6] = Sneezy

$dwarfs = ['Doc1', 'doc2', 'Doc3', 'doc20']; 
sort($dwarfs, SORT_NATURAL | SORT_FLAG_CASE);

foreach ($dwarfs as $index => $name) { 
    echo "dwarfs[" . $index . "] = " . $name . "\n"; 
} 
dwarfs[0] = Doc1 
dwarfs[1] = doc2 
dwarfs[2] = Doc3 
dwarfs[3] = doc20 
```

第二个例子中的代码可以简单地通过使用 *natcasesort()* 函数来编写

### asort()

*asort()* 函数在语法上等同于你刚才看到的姐妹函数。

两者的实质性区别在于 *asort()* (注意名字开头的‘a’)[与关联数组](https://www.youtube.com/watch?v=5qXvj56dmmw)一起使用。

原因是 *asort()* 的主要特性是**保存数组索引**。

当它对元素进行排序时，即使值的位置发生了变化，这些键仍然与相应的值相关联。

**它还使用标志作为第二个参数**，这不是强制的，并根据函数的结果返回真或假布尔值。

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

asort($dwarfs); 
foreach ($dwarfs as $index => $name) { 
    echo "dwarfs[" . $index . "] = " . $name . "\n"; 
}

dwarfs[fifth] = Bashful 
dwarfs[seventh] = Doc 
dwarfs[fourth] = Dopey 
dwarfs[first] = Grumpy 
dwarfs[second] = Happy 
dwarfs[third] = Sleepy 
dwarfs[sixth] = Sneezy 
```

### rsort()

这是对数组元素进行排序的函数家族的另一个近亲。

此外，这与您之前看到的其他数组函数一样，是在 PHP 第四版中首次发布的，具有相同的语法，只是在本质上有所不同。

这个特性就不多说了，把已经说过的关于 *sort()* 的都拿来，把结果反过来。

*rsort()* 取数组作为第一个参数，**一个非强制标志，确定排列作为第二个参数，返回一个布尔值作为函数的结果。**

这两个函数之间的唯一区别是，在这里，元素的顺序从最高到最低。

```
$dwarfs = ['Doc', 'Grumpy', 'Happy', 'Sleepy', 'Dopey', 'Bashful', 'Sneezy'];

sort($dwarfs); 
foreach ($dwarfs as $index => $name) { 
    echo "dwarfs[" . $index . "] = " . $name . "\n"; 
}

dwarfs[0] = Sneezy 
dwarfs[1] = Sleepy 
dwarfs[2] = Happy 
dwarfs[3] = Grumpy 
dwarfs[4] = Dopey 
dwarfs[5] = Doc 
dwarfs[6] = Bashful 
```

### arsort()

该函数基于 *asort()* 。

像你刚才看到的所有前面的函数一样，**它对一个数组的元素进行排序，并使用相同的语法。**

asort() 也用作关联数组，并保留连接值的键。

函数名开头的字母“a”实际上意味着联想。

正如您从代表反向的字母“r”中所理解的那样，该函数从最大或最高开始对元素的结果进行排序，并按照字母顺序逐渐下降

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

asort($dwarfs); 
foreach ($dwarfs as $index => $name) { 
    echo "dwarfs[" . $index . "] = " . $name . "\n"; 
}

dwarfs[seventh] = Doc 
dwarfs[fourth] = Dopey 
dwarfs[first] = Grumpy 
dwarfs[second] = Happy 
dwarfs[third] = Sleepy 
dwarfs[sixth] = Sneezy 
dwarfs[fifth] = Bashful 
```

### ksort()

是的，现在您已经理解了，对元素进行排序的数组函数非常广泛，并且它们都有相同的语法，

语法相同是一件好事，

他们都有相似的名字这一事实有点不太乐观。

到目前为止看到的所有函数都以' sort '这个词结尾，这很简单。

**容易混淆的部分是前缀，**

到目前为止，您已经看到了“r”和“a ”,现在您将看到另一个前缀。

**‘k’:字母‘k’代表 key，表示这次我们要排序的属性不是数组元素的值，而是它们的键，也叫索引。**

从语法上讲，您将再次看到相同的两个参数，其中强制数组(第一个)，第二个由标志组成。

下面是一个简单的 *ksort()* 函数的例子。

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

ksort($dwarfs); 
foreach ($dwarfs as $index => $name) { 
    echo "dwarfs[" . $index . "] = " . $name . "\n"; 
}

dwarfs[fifth] = Bashful 
dwarfs[first] = Grumpy 
dwarfs[fourth] = Dopey 
dwarfs[second] = Happy 
dwarfs[seventh] = Doc 
dwarfs[sixth] = Sneezy 
dwarfs[third] = Sleepy 
```

### krsort()

这里没有花太多时间，程序与 *ksort()* 相同，但是**用字母“r”描述，结果正好相反**。

这些键是从字母表的最后一个元素到第一个元素排序的。

```
$dwarfs = [ 
    'first' => 'Grumpy', 
    'second' => 'Happy', 
    'third' => 'Sleepy', 
    'fourth' => 'Dopey', 
    'fifth' => 'Bashful',
    'sixth' => 'Sneezy', 
    'seventh' => 'Doc' 
];

ksort($dwarfs); 
foreach ($dwarfs as $index => $name) { 
    echo "dwarfs[" . $index . "] = " . $name . "\n"; 
}

dwarfs[third] = Sleepy 
dwarfs[sixth] = Sneezy 
dwarfs[seventh] = Doc 
dwarfs[second] = Happy 
dwarfs[fourth] = Dopey 
dwarfs[first] = Grumpy 
dwarfs[fifth] = Bashful 
```

### usort()

这里我们有一些非常有趣的东西，

*usort()* 函数类似于其他带有后缀 sort 的函数，但它的工作方式略有不同。

**在这种情况下，用作前缀的‘u’代表‘user’**，并表示[该函数使用由用户](https://developer.mozilla.org/en-US/docs/Glossary/Callback_function)创建的回调函数，以便对数组中的元素进行排序。

这与我们迄今为止看到的其他特征有很大的不同，

这个函数有两个参数，**第一个是我们想要处理的数组，第二个参数，在这个例子中也是强制的，是回调函数。**

此函数返回的值将决定元素的排序位置，因此，它将始终被转换为整数。

回调函数是 PHP 7 中开发的飞船操作器在其理想环境中找到的地方之一。

如果你从未使用过它，也不知道如何使用它，看看 PHP 操作指南中的特性吧

```
$dwarfs = ['Doc', 'Grumpy', 'Happy', 'Sleepy', 'Dopey', 'Bashful', 'Sneezy'];

usort($dwarfs, function (int $a, int $b) { 
    return ($a <=> $b);
}); 
Array ( 
    [0] => Bashful 
    [1] => Doc 
    [2] => Dopey 
    [3] => Grumpy 
    [4] => Happy 
    [5] => Sleepy 
    [6] => Sneezy 
) 
```

让我们使用带有字符串的多维数组来做一些更复杂的事情

```
function compare($a, $b) { 
    return strcmp($a["dwarf"], $b["dwarf"]); 
} 
$dwarfs[0]["dwarf"] = 'Doc'; 
$dwarfs[1]["dwarf"] = 'Grumpy'; 
$dwarfs[2]["dwarf"] = 'Happy'; 
$dwarfs[3]["dwarf"] = 'Sleepy'; 
$dwarfs[4]["dwarf"] = 'Dopey'; 
$dwarfs[5]["dwarf"] = 'Bashful'; 
$dwarfs[6]["dwarf"] = 'Sneezy';

usort($dwarfs, "compare");

while (list($key, $value) = each($dwarfs)) { 
    echo "\$dwarfs[$key]: " . $value["dwarf"] . "\n"; 
} 
$dwarfs[0]: 'Bashful' 
$dwarfs[1]: 'Doc' 
$dwarfs[2]: 'Dopey' 
$dwarfs[3]: 'Grumpy'
$dwarfs[4]: 'Happy' 
$dwarfs[5]: 'Sleepy' 
$dwarfs[6]: 'Sneezy' 
```

您可能已经注意到了， *usort()* 函数删除了现有的键。

它在排序阶段给数组的元素分配新的键。

### uasort()

这里的情况比上面看到的函数有趣得多，

它也是“排序”函数家族的一部分，就像你刚刚读到的一些函数一样，由多个指示字母组成。

*uasort()* 包含两个字母，“u”表示该函数需要用户回调，“a”表示该函数更适合关联数组。

把这个函数的操作看作是 *usort()* 和 *asort()* 的交叉。

这个函数以这样一种方式对数组进行排序，即数组的索引保持它们与关联数组的值的相关性

```
function compare($a, $b){
    return ($a <=> $b);
} 
$dwarfs = [ 
    'first' => 'Grumpy', 
    'second' => 'Happy', 
    'third' => 'Sleepy',
    'fourth' => 'Dopey', 
    'fifth' => 'Bashful', 
    'sixth' => 'Sneezy', 
    'seventh' => 'Doc' ];

uasort($dwarfs, 'compare');

print_r($dwarfs);

Array ( 
    [fifth] => 'Bashful', 
    [seventh] => 'Doc',
    [fourth] => 'Dopey',
    [first] => 'Grumpy',
    [second] => 'Happy', 
    [third] => 'Sleepy', 
    [sixth] => 'Sneezy', 
) 
```

### ukort()

非常类似于 *uasort()* 函数，

这不同于它排序数组**键而不是值**的事实。

```
function compare($a, $b){ 
    return ($a <=> $b); 
} 
$dwarfs = [ 
    'first' => 'Grumpy', 
    'second' => 'Happy', 
    'third' => 'Sleepy', 
    'fourth' => 'Dopey', 
    'fifth' => 'Bashful',
    'sixth' => 'Sneezy', 
    'seventh' => 'Doc' ];

uksort($dwarfs, 'compare'); 
foreach ($dwarfs as $index => $name) { 
    echo "dwarfs[" . $index . "] = " . $name . "\n"; 
}

dwarfs[fifth] = Bashful 
dwarfs[first] = Grumpy 
dwarfs[fourth] = Dopey 
dwarfs[second] = Happy 
dwarfs[seventh] = Doc 
dwarfs[sixth] = Sneezy 
dwarfs[third] = Sleepy 
```

这是本文的第一个函数，它的语法与其他函数不同。

natsort() 其中“nat”代表自然秩序。

在推进和解释这个函数的目的是什么之前，[有必要解释一下自然顺序是什么](https://en.wikipedia.org/wiki/Natural_sort_order)。

不涉及太多细节，**人脑习惯于以不同于处理器分析数字和字母的方式思考，**

如果我让你像人一样对数字 1、5、10、15 进行排序，你会像上面写的那样准确地列出它们，

计算机取而代之的是第一个数字，在这种情况下，选项是 1 和 5，对它们进行排序，然后移动到第二个、第三个，依此类推。

最后的结果看起来更像这个 1，10，15，5。

现在，您已经对订单类型有了更多的了解，我们将深入研究这个数组函数的细节。

*natsort()* 只取一个参数，就是我们实际要修改的数组。

是作为参考考虑的，也就是说进入函数的数组不会和出来的一样，所以要小心。

**该函数返回的值是一个布尔值，表示操作的成功或失败。**

这个函数实现起来非常简单，下面是一个例子:

```
$arrayA = $arrayB = [1, 5, 10, 15];

asort($arrayA); 
Array ( [0] => 1 [2] => 10 [3] => 15 [1] => 5 )

natsort($arrayB); 
Array ( [0] => 1 [1] => 5 [2] => 10 [3] => 15 ) 
```

### natcasesort()

我们已经在几个例子中见过这个函数了，

这个函数以与人类相同的方式对数组元素进行排序，

natcasesort() 可以处理数字和字母，它的一个主要特性是维护元素之间的键值对。

***natcasesort()* 取单个参数，为强制元素，作为引用，**

这意味着在函数执行后，变量中元素的顺序会改变，并且无法返回。

该函数返回的变量类型是布尔型的，并且根据操作的成功或失败而变化。

这个和 *natsort()* 的区别在于这个函数是不区分大小写的。

```
$dwarfs = ['Doc1', 'doc2', 'Doc3', 'doc20']; 
print_r(natcasesort($dwarfs));

Array ( [0] => Doc1 [1] => doc2 [2] => Doc3 [3] => doc20 ) 
```

### array_multisort()

这是一个相当复杂的函数。

**它需要几个参数，包括多重数组、多维数组、标志等等等等。**

它用于同时对多个数组进行排序，或者对多维数组中的不同元素进行排序。

*array_multisort()* 适用于数值型和关联型数组，对于带有字符串类型键的元素，它们被保留，数值型元素被重置并从 0 开始。

如上所述，有几个参数，

它们分为必须排序的数组和指示要实现的顺序类型的标志。

**第一个参数**是强制的，是必须排序的主数组。

**第二个参数**表示用于上述数组的订单类型，该参数不是强制性的，实际上，它可以被第三个参数替代或完全取消。

这个标志的值是 SORT_ASC 和 SORT_DESC，它们是不言自明的。

**第三个参数**表示要执行的订单类型，按键与上面的 *sort()* 函数相关部分相同。

从第四个开始的参数只是其他数组，它们与第一个数组排序在一起。

```
$dwarfs = ['Doc', 'Grumpy', 'Happy', 'Sleepy', 'Dopey', 'Bashful', 'Sneezy'];
$originals = ["Mickey Mouse","Pete", "Goofy", "Minnie Mouse", "Pluto"];

array_multisort($dwarfs, $originals); print_r($dwarfs);

print_r($originals); 
Array ( 
    [0] => Bashful 
    [1] => Doc 
    [2] => Dopey 
    [3] => Grumpy 
    [4] => Happy 
    [5] => Sleepy 
    [6] => Sneezy 
)

Array ( 
    [0] => Goofy 
    [1] => Mickey Mouse
    [2] => Minnie Mouse 
    [3] => Pete 
    [4] => Pluto 
) 
```

### 结论

这里有另外几个数组函数可以添加到您的集合中。

我相信你会发现其中一些很容易马上采用，而另一些则需要更多的练习来充分发挥它们的潜力。

需要记住的一些细节是，几乎所有对数组中的元素进行排序的函数都以单词“sort”结尾，

它们有一个或两个字母作为前缀，表示操作的类型，并且大多数这些函数将参数数组作为引用而不是值。

**至于本系列中的其他函数，没有必要背下来，** [如果这些函数看起来太难了，可以看看如何构建数组的基础知识。](http://anastasionico.uk/blog/php-array-functions-exposed)

您可以简单地将此页面添加到浏览器的收藏夹中，或者如果您想了解更多信息，请注册订阅时事通讯，以便在每次发布新帖子时得到通知。

如果你已经准备好学习更多关于这个主题的内容，你可以查看一下最流行的 PHP 函数来处理数组元素。

***

学习编程是一个漫长的过程，需要大量的精力。

网上有很多信息，几乎所有的信息都是分散的，没有逻辑感。

出于这个原因，我专注于在一系列文章中创建帖子，就像你刚刚阅读的这篇文章一样。

它有所帮助，但其价值和结构不如一本书。

如果你不喜欢阅读或不想把时间花在笔记和便利贴之间，现在有一种更简单的方法，这些年来对我帮助很大。

视频课程和视频轨道。

Treehouse 是世界上最好的平台之一，其认证教师已经帮助了世界上成千上万的 web 开发人员。

今天对你来说，他们的基本年度计划也有 4 个月的假期

[点击此处，立即享受优惠](http://treehouse.7eer.net/c/1374240/294479/3944)

* * *

[![subscribe-Medium.jpg](img/9cb2f543deec991b7617c53d43820419.png)](http://eepurl.com/dIZqjf)

* * *

*原载于*[*http://anastasionico . uk*](http://anastasionico.uk/blog/sorting-arrays-element-using-php-functions)*。*
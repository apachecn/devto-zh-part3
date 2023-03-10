# Javascript 数组的更多乐趣:重新实现 Uniq

> 原文：<https://dev.to/jacoby/more-fun-with-javascript-arrays-reimplementing-uniq-5c4>

让我们从 Unix 命令`uniq`开始，给定一个排序的数字列表，它给出了唯一的值:

```
$ perl -e 'for ( 0..20 ) { print int rand 10 ; print "\n" }'| sort | uniq
0
1
2
3
5
6
7
8
9 
```

但是，如果最初不排序，`uniq`只是删除相邻的复制:

```
$ perl -e 'for ( 0..20 ) { print int rand 10 ; print "\n" }'|uniq
0
9
1
2
0
7
8
1
4
9
0
1
2
6
0
7
0
6
5
6
8 
```

这就是为什么所有酷小孩都知道`sort`有一个`-u`标志，允许你在一个命令中做`sort`和`uniq`，公然违反了[的 Unix 哲学](https://en.wikipedia.org/wiki/Unix_philosophy)。

```
$ perl -e 'for ( 0..20 ) { print int rand 10 ; print "\n" }'| sort -u
0
2
3
5
6
7
8
9 
```

看上面的`bash`例子(或者仅仅是了解我)，您可以看出我是一个 Perl 用户，并且有几种方式可以在 Perl 中处理唯一的值。最简单的是通过使用散列来消化东西。

```
my $hedgehogs->@* = map { int rand 10 } 0..9;
my $hash->%* = map { $_ => 1 } $hedgehogs->@*;
my $shrews->@* = keys $hash->%* ;

say join ',', $hedgehogs->@*;
say join ',', $shrews->@*;

# 5,4,7,4,2,5,2,4,6,2
# 2,7,4,6,5 
```

不过，关于散列顺序有两件事需要注意。哈希顺序没有排序。散列顺序不同于使用顺序，在本例中使用顺序是`5,4,7,2,6`。你可能不在乎，但它可能很重要。

有一种简单的方法。注意“容易”不一定是“简单”:

```
use List::Util qw{ uniqnum };

my $hedgehogs->@* = map { int rand 10 } 0..9;
my $shrews->@* = uniqnum $hedgehogs->@*;

say join ',', $hedgehogs->@*;
say join ',', $shrews->@*;

# 8,4,2,9,5,9,2,3,2,2
# 8,4,2,9,5,3 
```

`uniq`和`uniqnum`与 [`List::Util`](https://metacpan.org/pod/List::Util) 一起出现，它们在内核中，除非你的操作系统默认安装了一个坏掉的 Perl，否则你已经安装了。

我提到了很多这一点来对比 Javascript，它没有`uniq`但是有`filter`，这*非常*有用并且可以这样工作。

`filter`是一个函数，如果该函数返回`1`则传递该值，否则阻塞该值。

```
let hedgehogs = Array(10)
  .fill()
  .map(x => Math.floor(10 * Math.random()));
let shrews = hedgehogs.filter(
  (value, index, self) => self.indexOf(value) == index
);

console.log(hedgehogs.join(","));
console.log(shrews.join(","));

// 3,9,4,9,9,8,7,8,9,9
// 3,9,4,8,7 
```

这里需要知道两件事:

*   `filter`和`map`一样，被传递三个值；数组中位置的值、数组位置的索引和数组本身。
*   Javascript 有`indexOf`，在给定一个数组和值的情况下，它会告诉您该值出现的第一个位置。

在上面的例子中，用第一个`9`:

*   `filter`已通过`(9, 1, [3,9,4,9,9,8,7,8,9,9])`
*   `value`是`9`、`index` = `1`
*   `[3,9,4,9,9,8,7,8,9,9].indexOf(9)`是`1`
*   `1`等于`1`
*   返回`true`

但是第二个`9`:

*   `filter`已通过`(9, 3, [3,9,4,9,9,8,7,8,9,9])`
*   `value`是`9`、`index` = `3`
*   `[3,9,4,9,9,8,7,8,9,9].indexOf(9)`是`1`
*   `1`不等于`3`
*   返回`false`

如你所见，这保持了秩序。如果 3 先出现，它首先被处理和通过。

您可能想知道 Perl 的`List::Util`中的`uniq`是如何处理它的。老实说，我不知道。我知道`List::Util`使用 XS，Perl 利用 C 提高速度的方式，并且[我可以链接到回购](https://github.com/Dual-Life/Scalar-List-Utils/blob/master/ListUtil.xs)中的 XS，但是我根本不处理它，所以我还不能理解 XS 代码。

Perl 使用了`grep`而不是`filter`，但是通过一点工作，比如`use feature qw{ say state }`，你可以制作一个这样的过滤器:

```
my $hedgehogs->@* = map { int rand 10 } 0..9;
$shrews->@* = grep
    { state $x = {} ; $x->{$_}++ ; $x->{$_} == 1 }
    $hedgehogs->@*; 
```

这里我们使用一个内部散列来保存值，而不是在其上运行`keys`，我们迭代`$x->{value}`，如果它等于 1，我们就让它通过。

我们可以使用一个`indexOf`方法来制作一个更 Javascripty 的`grep`函数吗？当然，因为 [`https://metacpan.org/pod/List::MoreUtils`](https://metacpan.org/pod/List::MoreUtils) 有`first_index`，也有`uniq`，并且不在内核中，所以它增加了很多复杂性，却没有增加易用性。

JS: `let shrews = hedgehogs.sort()`中的`sort`有一个有趣的副作用，因为`.sort()`作用于`hedgehogs`，而不是传递给`shrews`的输出。所以:

```
let hedgehogs = Array(10)
  .fill()
  .map(x => Math.floor(10 * Math.random()));
let shrews = hedgehogs.sort();

console.log(hedgehogs.join(","));
console.log(shrews.join(","));

// 0,0,2,3,3,4,6,7,8,8
// 0,0,2,3,3,4,6,7,8,8 
```

很明显，我们不想那样。这个怎么处理？两种方式。要么在赋值后对`shrews`进行排序，要么使用`map`将`sort`与`hedgehogs`隔开。

```
let hedgehogs = Array(10)
  .fill()
  .map(x => Math.floor(10 * Math.random()));
let shrews = hedgehogs.map(x => x).sort();

console.log(hedgehogs.join(","));
console.log(shrews.join(","));

// 6,5,7,8,5,1,3,2,6,0
// 0,1,2,3,5,5,6,6,7,8 
```

(我有点想让那个`map(a=>a)`成为[一个深度的史蒂夫·迪特寇引用](https://en.wikipedia.org/wiki/Mr._A)，但是 Perl 的`sort`已经燃烧了我使用`a`和`b`作为变量名的欲望。)

如果你有任何问题或意见，我将很高兴听到它。在 [Twitter](https://twitter.com/jacobydave) 或[上问我，在我的博客回购](https://github.com/jacoby/jacoby.github.io)上做文章。
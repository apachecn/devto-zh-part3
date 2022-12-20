# C.S .基础:从十进制到二进制，然后再返回

> 原文：<https://dev.to/awwsmm/c-s-basics-from-decimal-to-binary-and-back-again-1j93>

## *或*，理解二进制和十六进制数

* * *

二进制和十六进制数字可能会让初学计算机科学的学生或任何不熟悉计算机内部工作的人望而生畏。新闻报道和[病毒式 YouTube 视频](https://www.youtube.com/watch?v=UDSfMmwNU2w)有时使用这些不同基数的数字来[吓唬](https://www.youtube.com/watch?v=aAwJlD-m_hE)或恐吓人们，暗示内容超出他们的理解范围或需要*高级黑客技能*才能理解。

但是如果你有一个好的基础，二进制和十六进制数字是很容易的。理解二进制数最简单的方法是从你已经知道的熟悉的十进制数开始，并以此为基础进行构建。首先，让我们从简单的地方开始:分数。

## 理货标志和罗马数字

如果你在记录一个缓慢增长的数字，或者你需要对举手表决之类的事情进行快速非正式的计数，你可以使用[计数标记](https://en.wikipedia.org/wiki/Tally_marks)。这种原始的计数系统包括简单地划一条斜线来计数某一特定事物的一个实例。两个斜线表示两个实例，等等。:

```
 | = 1
  || = 2
 ||| = 3
|||| = 4 
```

Enter fullscreen mode Exit fullscreen mode

...当我们数到五的时候，我们有时会把这些斜线组合起来，这样更容易数。我们可以把七写成 ~~`||||`~~ `||`，而不是七。这使得标记组更容易大量累加，但是我们仍然需要每次计数的单个计数，无论是垂直的还是水平的。

理货标志是一个 ***一元*** (发音:*尤* -nair-ee)编号系统，[一词来源于拉丁语 *unus* ，意为一个](https://en.wikipedia.org/wiki/1#Etymology)，因为只有一个单一的符号——理货。五个唛头表示五个，六百七十个唛头表示六百七十个。显然，理货标记会很快变得很麻烦。

稍微简单一点的编号方案是罗马数字系统。这是用来给超级猫头鹰、侠盗猎车手游戏和星球大战电影编号的。在罗马数字系统中，左边的数字代表右边的数字:

```
 I = 1
    II = 2
   III = 3
  IIII = 4 
```

Enter fullscreen mode Exit fullscreen mode

...很简单，对吧？它们看起来有点像计数标记，每个计数都将数字的值增加 1。

在某个时候，一个非常聪明的古人意识到你可以用其他符号来表示更大的数量。我们可以用`V`代替五个计数标记(`IIIII`，或者 ~~`IIII`~~ ，中间有一条水平斜线)。我们可以用一个`X`代替两个`V`来表示十个理货标志。我们的数字列表可以扩展:

```
 V     = 5
  VI    = 6
  VII   = 7
  VIII  = 8
  VIIII = 9
  X     = 10 
```

Enter fullscreen mode Exit fullscreen mode

古人意识到,`IIII`和`VIIII`乍一看可能会混淆。[人脑在工作记忆中一次只能容纳大约 3-5 个符号或想法](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC2864034/)，因此在视觉上将这么多符号组合在一起是一个挑战。这导致了罗马数字系统中*减法记数法*的发展，一个较小的数字紧接在一个较大的数字之前，意味着较小的数字从较大的数字中减去。于是`IIII`反而变成了`IV` ( `5 - 1 = 4`)，`VIIII`变成了`IX` ( `10 - 1 = 9`)，以此类推。改进后的编号系统是:

```
 I = 1    6  = VI
    II = 2    7  = VII
   III = 3    8  = VIII
    IV = 4    9  = IX
     V = 5    10 = X 
```

Enter fullscreen mode Exit fullscreen mode

更多的符号被介绍给五十(`L`)、一百(`C`)、五百(`D`)和一千(`M`)。当罗马数字被用于日常计数时，大于几千的数字是不常见的，也没有一个统一的方法来写数字，比如两千、五千或一万，等等。一个扩展的罗马数字表可能看起来像:

```
 I    = 1    XI    = 11   ...           ...
  II   = 2    XII   = 12   XLII   = 42   XCII   = 92
  III  = 3    XIII  = 13   XLIII  = 43   XCIII  = 93
  IV   = 4    XIV   = 14   XLIV   = 44   XCIV   = 94
  V    = 5    XV    = 15   XLV    = 45   XCV    = 95
  VI   = 6    XVI   = 16   XLVI   = 46   XCVI   = 96
  VII  = 7    XVII  = 17   XLVII  = 47   XCVII  = 97
  VIII = 8    XVIII = 18   XLVIII = 48   XCVIII = 98
  IX   = 9    XIX   = 19   XLIX   = 49   XCIX   = 99
  X    = 10   XX    = 20   L      = 50   C      = 100 
```

Enter fullscreen mode Exit fullscreen mode

罗马数字是一种按字母顺序排列的编号系统，每个数字总是代表一个特定的数字。所以`XXX`中的每一个`X`都表示`10`(共`10 + 10 + 10 = 30`)。这与我们熟悉的十进制系统形成了对比，在十进制系统中，“`50`中的“`5`”与“`5000`中的“`5`”(五个*千个*)具有不同的含义(五个*十个*),但我们稍后会回到这一点。

罗马数字适合大多数日常使用。它们很容易添加——只需将所有相似的符号组合在一起，并将每五个`I`符号“压缩”成一个`V`，然后将每两个`V`符号“压缩”成一个`X`，以此类推:

```
XXXVII + LXIII (= 37 + 63)
= L + XXXX + V + IIIII
= L + XXXX + VV
= L + XXXXX
= L + L
= C (= 100) 
```

Enter fullscreen mode Exit fullscreen mode

(这对于像`IV`和`XC`这样的符号来说有点困难，它们首先需要分别“扩展”为`IIII`和`LXXXX`。)它们也很容易减去——只需从 ***被减数*** (从中减去*的数)中移除所有出现在 ***减数*** (从*中减去*的数):* 

```
XLI - XXVIII (= 41 - 28)
= XXXXI - XXVIII (first, "expand" `XL` to `XXXX`)
=   XX  -   VII 
```

Enter fullscreen mode Exit fullscreen mode

...并根据需要扩展被减数中的任何因子

```
= XVIIIII - VII
= X   III
= XIII (= 13) 
```

Enter fullscreen mode Exit fullscreen mode

当处理大数或尝试处理小数时，罗马数字很快变得势不可挡(小数被分成十二分之一，而不是像我们熟悉的十进制那样分成十分之一 T2)。总而言之，有用，但不伟大。肯定有更好的办法吧？

## 位置编号和阿拉伯数字

没有单一的，*正确的*方式来表示一个数。无论哪个系统最有用、最容易使用，自然都会比其他系统更受欢迎。在中世纪，一种位置编号系统在印度被发明，并被阿拉伯学者传播到西方和东方，最终在欧洲、中国和俄罗斯流行起来。这个系统，现在被称为[印度-阿拉伯数字系统](https://en.wikipedia.org/wiki/Arabic_numerals)(又名。阿拉伯数字系统或只是“西方”数字)，可能是你最熟悉的一个。

在阿拉伯数字系统中，有十个不同的数字代表不同的量。这使得阿拉伯数字成为一个 ***十进制*** 的计数系统，来源于拉丁语 *decimus* ，意为“十分之一”。阿拉伯数字系统的十位数字及其英文名称是(与罗马数字和计数符号相比):

```
 0 ("zero")  =      = 
  1 ("one")   = I    = |
  2 ("two")   = II   = ||
  3 ("three") = III  = |||
  4 ("four")  = IV   = ||||
  5 ("five")  = V    = |||||
  6 ("six")   = VI   = ||||| |
  7 ("seven") = VII  = ||||| ||
  8 ("eight") = VIII = ||||| |||
  9 ("nine")  = IX   = ||||| |||| 
```

Enter fullscreen mode Exit fullscreen mode

最关键的是`0`，代表没有任何理货标志。对你来说可能不是这样，但是为“无”创造一个符号是一个巨大的概念飞跃。如果你习惯用数字来计数，为什么你还需要一个符号来表示“什么都没有”呢？没有符号不就代表“没有”吗？

阿拉伯数字系统也以极其巧妙的方式使用这个`0`数字。在罗马数字系统中，如果我们想写三个一(即。数字*三个*，我们需要把数字*一个*写三次(`III`)？而如果我们要写三个十(即。数字*三十*，我们需要把数字*写成十*的三倍(`XXX`)？同样适用于三百(`CCC`)和三千(`MMM`)！我们需要一个新的数字(`I`、`X`、`C`、`M`)来表示 10 的每一次幂。当我们没有数字的时候，我们就没有数字了。

阿拉伯系统并没有这样做，而是使用了所谓的 ***位置*** 编号系统，因为数字表达式中给定数字的*位置*决定了它的值。所以如果我们想要三个*个*，我们可以用数字`3`在一个数的特定位置(即*个的*位置)；如果我们想要三个*百元*，我们在相同数字的不同位置使用相同的数字(在*百元的*位置)。我们可以使用`0`作为占位符来显示不同图形的位置。

因为阿拉伯数字是一个以 10 为基数的*(***十进制*** )系统，也是一个位置系统，所以给定数字中的每个位置都是其前面位置的`10`倍。最右边的位置是*个位的*位，左边是*个位的*位，然后是*个位的*位，依此类推:* 

```
123
|||
|||____(3)__ ones = (3 * 10^0) = (3 *   1) =   3
||
||___(2)____ tens = (2 * 10^1) = (2 *  10) =  20
|
|__(1)__ hundreds = (1 * 10^2) = (1 * 100) = 100 
```

Enter fullscreen mode Exit fullscreen mode

数字`123`翻译过来就是`1`(一)百加`2`(二)十加`3`(三)一。如果我们从这个数字中减去三个 1，我们需要使用占位符`0`(零):

```
123 - 3 = 120 
```

Enter fullscreen mode Exit fullscreen mode

否则，如果我们简单地去掉 1 的位置(如果我们没有表示“无”的符号)，我们将得到数字`12`，它与`120`完全不同。阿拉伯数字系统巧妙地使用这种位置系统，加上数字`0`，以简洁的形式表示任何可以想象的“计数”数字(正整数和零)，只有十个不同的数字。

### 加减

位置编号系统也极大地改进了加法和减法。我们不用像罗马数字那样把数字组合在一起，我们可以简单地逐个位置地减去数字:

```
 345
-  23
-----
  322 
```

Enter fullscreen mode Exit fullscreen mode

我们从*个位的*位中减去*个位的*位，从*个位的*位中减去*个位的*位，以此类推。如果某个位置缺少一个图形，我们假设它是`0`(所以上面的`23`就变成了`023`)。但是，就像我们必须“压缩”和“扩展”罗马数字系统中的数字一样，我们也必须对阿拉伯数字这样做:

```
 234  Here, 5 is larger than 4, so we need to "expand" one
-  35  of the tens into ten ones. To make this calculation easier,
-----  I'll put each "place" into its own "box":

  [ 2][ 3][ 4]  We "trade" one of the tens in the minuend for ten
- [ 0][ 3][ 5]  ones, turning 4 in the ones' place into 14
--------------

  [ 2][ 2][14]  Now, we can easily subtract 5 from 14 to get 9.
- [ 0][ 3][ 5]  But we have a similar problem with the tens' place.
--------------

  [ 1][12][14]  "Expand" the 2 in the hundreds' place into ten tens
- [ 0][ 3][ 5]  in the tens' place, turning 2 tens into 12 tens
--------------

  [ 1][12][14]  Finally, we can subtract the numbers in each
- [ 0][ 3][ 5]  position without any more "expansions".
--------------
  [ 1][ 9][ 9] => 234-35=199 
```

Enter fullscreen mode Exit fullscreen mode

这个过程可能已经根深蒂固，以至于你甚至没有意识到自己正在做这件事。为了更好地了解上面的例子中发生了什么，让我们为阿拉伯数字建立一个加法表。

#### 十进制的加法和乘法

下面，将每行第一个数字之后最左边的数字加到每列顶部的数字，得到每个单元格中的数字。第一行很简单- `0`加任何一个数字等于原始数字(`x`点数加`0`(否)点数等于`x`点数):

| + | Zero | one | Two | three | four | five | six | seven | eight | nine |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Zero | Zero | one | Two | three | four | five | six | seven | eight | nine |

减法表看起来与第一行完全相同，因为任何数字减去`0`都是相同的数字。我们从减法表中得不到太多的启示，所以我们暂时放弃它。第二行稍微复杂一些，将`1`加到一个数字 ***上，将*** 加到序列中的下一个数字上:

| + | Zero | one | Two | three | four | five | six | seven | eight | nine |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Zero | Zero | one | Two | three | four | five | six | seven | eight | nine |
| one | one | Two | three | four | five | six | seven | eight | nine | ？ |

> *注:一个数减 1 叫做**递减**。*

...于是`1+2=3`、`1+4=5`等等。但是`9`之后呢？我们没有特殊的符号代表十个一的数字，就像我们在罗马体系中做的那样。相反，我们使用`0`作为 1 的占位符，并在 10 的位置放置一个`1`(1)来创建数字`10`——我们的十进制位置数字系统的结果。一个比九个一多就是十个一*或者*一个十:

| + | Zero | one | Two | three | four | five | six | seven | eight | nine |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Zero | Zero | one | Two | three | four | five | six | seven | eight | nine |
| one | one | Two | three | four | five | six | seven | eight | nine | Ten |

将`2`加到一个数字上，使其增加两倍，增加两位数:

| + | Zero | one | Two | three | four | five | six | seven | eight | nine |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Zero | Zero | one | Two | three | four | five | six | seven | eight | nine |
| one | one | Two | three | four | five | six | seven | eight | nine | Ten |
| Two | Two | three | four | five | six | seven | eight | nine | Ten | Eleven |

让我们填写加法表的其余部分:

| + | Zero | one | Two | three | four | five | six | seven | eight | nine |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Zero | Zero | one | Two | three | four | five | six | seven | eight | nine |
| one | one | Two | three | four | five | six | seven | eight | nine | Ten |
| Two | Two | three | four | five | six | seven | eight | nine | Ten | Eleven |
| three | three | four | five | six | seven | eight | nine | Ten | Eleven | Twelve |
| four | four | five | six | seven | eight | nine | Ten | Eleven | Twelve | Thirteen |
| five | five | six | seven | eight | nine | Ten | Eleven | Twelve | Thirteen | Fourteen |
| six | six | seven | eight | nine | Ten | Eleven | Twelve | Thirteen | Fourteen | Fifteen |
| seven | seven | eight | nine | Ten | Eleven | Twelve | Thirteen | Fourteen | Fifteen | Sixteen |
| eight | eight | nine | Ten | Eleven | Twelve | Thirteen | Fourteen | Fifteen | Sixteen | Seventeen |
| nine | nine | Ten | Eleven | Twelve | Thirteen | Fourteen | Fifteen | Sixteen | Seventeen | Eighteen |

你可以在上面看到，当我们用完符号(即。当我们需要一个大于`9`的数字时，我们利用我们的位置编号系统，将*的下一个位置*从`0`(这是隐含的)增加到`1`，并将当前位置从`9`减少回`0`。我们一遍又一遍地进行相同的递增，直到到达`19`，当我们再次尝试递增时，十位将从`1`递增到`2`，但一位将从`9`跳回到`0`。所以`19`后面的数字是`20`。

> *你可以把这些“位置”想象成*齿轮*。我们必须将“个位齿轮”通过九个档位，在第十个档位上，下一个最大的齿轮(十位齿轮)增加一个档位，而当前的齿轮(个位齿轮)已经转了一整圈，回到它开始的地方——在`0`。*

同样，我们可以建立一个乘法表。前两行很简单，因为`0`乘以任意数就是`0`，而`1`乘以任意数就是那个数:

| * | Zero | one | Two | three | four | five | six | seven | eight | nine |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Zero | Zero | Zero | Zero | Zero | Zero | Zero | Zero | Zero | Zero | Zero |
| one | Zero | one | Two | three | four | five | six | seven | eight | nine |

将一个数乘以`2`等同于将该数加零两次(`2`次)。所以`2 x 1`(或`2 * 1`)和`0 + 1 + 1`是一样的，从我们的加法表中已经知道是`2`。同理，`2 * 2 = 0 + 2 + 2 = 4`，和`2 * 3 = 0 + 3 + 3 = 6`。这种模式沿着加法表的对角线继续下去，得到乘法表的第二行。为了避免过度解释整个事情(你可能已经非常熟悉了)，这里是一个基于阿拉伯数字的十进制系统的完整乘法表:

| * | Zero | one | Two | three | four | five | six | seven | eight | nine |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Zero | Zero | Zero | Zero | Zero | Zero | Zero | Zero | Zero | Zero | Zero |
| one | Zero | one | Two | three | four | five | six | seven | eight | nine |
| Two | Zero | Two | four | six | eight | Ten | Twelve | Fourteen | Sixteen | Eighteen |
| three | Zero | three | six | nine | Twelve | Fifteen | Eighteen | Twenty-one | Twenty-four | Twenty-seven |
| four | Zero | four | eight | Twelve | Sixteen | Twenty | Twenty-four | Twenty-eight | Thirty-two | Thirty-six |
| five | Zero | five | Ten | Fifteen | Twenty | Twenty-five | Thirty | Thirty-five | Forty | Forty-five |
| six | Zero | six | Twelve | Eighteen | Twenty-four | Thirty | Thirty-six | forty-two | Forty-eight | Fifty-four |
| seven | Zero | seven | Fourteen | Twenty-one | Twenty-eight | Thirty-five | forty-two | forty-nine | fifty-six | Sixty-three |
| eight | Zero | eight | Sixteen | Twenty-four | Thirty-two | Forty | Forty-eight | fifty-six | Sixty-four | seventy-two |
| nine | Zero | nine | Eighteen | Twenty-seven | Thirty-six | Forty-five | Fifty-four | Sixty-three | seventy-two | Eighty-one |

注意，*这个*表对角线上的数字是非负整数(`0^2 = 0 * 0 = 0`、`1^2 = 1`、`2^2 = 4`、`3^3 = 9`)的平方，...).这当然是你的第二天性，但是如果你理解这些数字是如何构造的，那么你将有一个更好的基础去理解二进制和十六进制以及其他数字是如何构造的。

## 【王牌贱种】

我们称十进制为*基数为 10* 的计数系统，因为在该系统中有十个唯一的十进制数字可以用来构造数字(`0`到`9`)。但是用十个符号也没什么特别的。古代的十进制计数系统可能是从人们用手指计数开始的(通常一个人有十个手指)。但情况并非总是如此。

### 八进制(基数-8)

为了保持距离，中美洲的帕米人雇佣了一名[基地 8](https://www.degruyter.com/dg/viewarticle/j%24002flity.2006.10.issue-1%24002flingty.2006.002%24002flingty.2006.002.xml) (又名。 ***八进制*** 系统。这可能像十进制系统一样自然出现；例如，现在南加州的土著 Yuki 人最初采用了类似的系统，依靠手指之间的空间，而不是手指本身。

一个帕米怎么数到十？在八进制数字系统中，从 0 到 7 的所有数字都有唯一的名称和符号:

```
0 = "zero"
1 = "one"
2 = "two"
3 = "three"
4 = "four"
5 = "five"
6 = "six"
7 = "seven" 
```

Enter fullscreen mode Exit fullscreen mode

但是，就像我们在十进制中没有一个特殊的符号代表十进制的十进制(实际上是一个 T0 和一个 T1 相邻)，在八进制中也不会有一个特殊的符号代表八进制的八进制。事实上，如果这个八进制的计数系统也是位置性的，就像阿拉伯数字系统一样，那么将`1`加到`7`应该会将*的八位从`0`增加到`1`，并且将*的一位*减少回`0`，就像十进制中一样。为了避免混淆，从现在开始，我们将在数字的名称中指定基数，所以十进制中的`8`将改为`8 base 10`。上面的八进制数列表可以展开:* 

```
0 = "zero base eight"   10 = "eight base eight"     20 = "sixteen base eight"
1 = "one base eight"    11 = "nine base eight"      21 = "seventeen base eight"
2 = "two base eight"    12 = "ten base eight"       22 = "eighteen base eight"
3 = "three base eight"  13 = "eleven base eight"    23 = "nineteen base eight"
4 = "four base eight"   14 = "twelve base eight"    24 = "twenty base eight"
5 = "five base eight"   15 = "thirteen base eight"  25 = "twenty-one base eight"
6 = "six base eight"    16 = "fourteen base eight"  26 = "twenty-two base eight"
7 = "seven base eight"  17 = "fifteen base eight"   27 = "twenty-three base eight" 
```

Enter fullscreen mode Exit fullscreen mode

请注意，在任何地方都没有代表“八”的单个符号，就像在十进制系统中没有代表“十”的单个符号一样。相反，当我们到达 1 的位置`7`时，如果我们希望增加数字，我们增加*8 的位置*，减少 1 的位置到`0`。

用八进制表示一个数，就像用十进制一样，其中包含了一个计算公式。我们为十进制制作的上图的模拟，在八进制中可以是:

```
123 (base 8)
|||
|||____(3)_____ ones = (3 * 8^0) = (3 * 1)  =   3
||
||___(2)_____ eights = (2 * 8^1) = (2 * 8)  =  16
|
|__(1)__ sixty-fours = (1 * 8^2) = (1 * 64) =  64 
```

Enter fullscreen mode Exit fullscreen mode

所以`123 base eight`不等于`123 base ten`，而是`3 + 16 + 64 = 83 base ten`。这是有道理的，因为(以下都是十进制):

```
 __ "tens' place"
                                       |          __ "ones' place"
                                       |          |
[octal representation]                 |          |
(1 * 64) + (2 *  8) + (3 * 1)    =    (8 * 10) + (3 * 1)
 |          |          |              [decimal representation]
 |          |          |
 |          |          |__ "ones' place"
 |          |__ "eights' place"
 |__ "sixty-fours' place" 
```

Enter fullscreen mode Exit fullscreen mode

八进制加法表如下所示:

| + | Zero | one | Two | three | four | five | six | seven |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Zero | Zero | one | Two | three | four | five | six | seven |
| one | one | Two | three | four | five | six | seven | Ten |
| Two | Two | three | four | five | six | seven | Ten | Eleven |
| three | three | four | five | six | seven | Ten | Eleven | Twelve |
| four | four | five | six | seven | Ten | Eleven | Twelve | Thirteen |
| five | five | six | seven | Ten | Eleven | Twelve | Thirteen | Fourteen |
| six | six | seven | Ten | Eleven | Twelve | Thirteen | Fourteen | Fifteen |
| seven | seven | Ten | Eleven | Twelve | Thirteen | Fourteen | Fifteen | Sixteen |

注意，它看起来和十进制加法表完全一样，直到我们得到大于`7`的值。然后，我们必须增加 8 的位置，并将 1 的位置设置回`0`。八进制乘法表应该是这样的:

| * | Zero | one | Two | three | four | five | six | seven |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Zero | Zero | Zero | Zero | Zero | Zero | Zero | Zero | Zero |
| one | Zero | one | Two | three | four | five | six | seven |
| Two | Zero | Two | four | six | Ten | Twelve | Fourteen | Sixteen |
| three | Zero | three | six | Eleven | Fourteen | Seventeen | Twenty-two | Twenty-five |
| four | Zero | four | Ten | Fourteen | Twenty | Twenty-four | Thirty | Thirty-four |
| five | Zero | five | Twelve | Seventeen | Twenty-four | Thirty-one | Thirty-six | Forty-three |
| six | Zero | six | Fourteen | Twenty-two | Thirty | Thirty-six | forty-four | fifty-two |
| seven | Zero | seven | Sixteen | Twenty-five | Thirty-four | Forty-three | fifty-two | Sixty-one |

这需要一点时间，特别是如果你不熟悉八进制数，但你可以很容易地检查它的准确性。取表格中的任意一对数字，得到它们乘积的十进制等效值(例如`7 base ten`乘以`6 base ten`等于`42 base ten`)，然后尽可能多地除以 8，不加分数(因此`42 base ten`除以`8 base ten`等于`5 base ten`，余数为`2 base ten`)。结果和余数将分别是该数的八进制表示的八位数字和一位数字(`52 base eight` == `42 base ten`)。

`4`列有另一个很酷的属性，每个数字要么以`0`结尾，要么以`4`结尾。这是因为当你在八进制中把`4`加到`0`时，你得到`4`，但是当你把`4`加到`4`时，你得不到`8`(因为`8`在八进制中不存在)，反而得到`10`。在这方面，八进制中的`4`与十进制中的`5`作用相同。

在这个加法表上，还有一点需要注意。在小学的时候，你可能已经知道，当你把从`2`到`9`的任何一个数乘以`9`(基数为 10)时，结果有一些有趣的性质:

```
2 * 9 = 18  (all base-10)
3 * 9 = 27
4 * 9 = 36
5 * 9 = 45
6 * 9 = 54
7 * 9 = 63
8 * 9 = 72
9 * 9 = 81 
```

Enter fullscreen mode Exit fullscreen mode

结果的第一个数字(十位数)是第一个数字减一。而结果的第二位是第一位的*九的补码*。换句话说，就是你需要将*加到*的乘积的第一个数字上，使总和成为`9` :

```
 __ [first digit]
                                  |      __ [second digit]
                                  |     |
x = 2:  2 * 9 = 18 => [x] * 9 = [x-1][9-(x-1)]
x = 3:  3 * 9 = 27 => [x] * 9 = [x-1][9-(x-1)]
x = 4:  4 * 9 = 36 => [x] * 9 = [x-1][9-(x-1)]
x = 5:  5 * 9 = 45 => [x] * 9 = [x-1][9-(x-1)]
x = 6:  6 * 9 = 54 => [x] * 9 = [x-1][9-(x-1)]
x = 7:  7 * 9 = 63 => [x] * 9 = [x-1][9-(x-1)]
x = 8:  8 * 9 = 72 => [x] * 9 = [x-1][9-(x-1)]
x = 9:  9 * 9 = 81 => [x] * 9 = [x-1][9-(x-1)] 
```

Enter fullscreen mode Exit fullscreen mode

同样的事情也发生在八进制中，但是我们有了一个*七进制补码* :
而不是*九进制补码*

```
x = 2:  2 * 7 = 16 => [x] * 7 = [x-1][7-(x-1)]
x = 3:  3 * 7 = 25 => [x] * 7 = [x-1][7-(x-1)]
x = 4:  4 * 7 = 34 => [x] * 7 = [x-1][7-(x-1)]
x = 5:  5 * 7 = 43 => [x] * 7 = [x-1][7-(x-1)]
x = 6:  6 * 7 = 52 => [x] * 7 = [x-1][7-(x-1)]
x = 7:  7 * 7 = 61 => [x] * 7 = [x-1][7-(x-1)] 
```

Enter fullscreen mode Exit fullscreen mode

...很酷吧。

### 第四纪(底数-4)

如前所述，数系可以是任何基数。他们可以是基地`20`或`60`或`6.02 * 10^24`。更大的基数需要更多独特的符号，但可以更简洁地表示大数。*(如果你有一个基数为 10，000 的系统，那么你可以用一个唯一的字符代表从`0`到`9,999`的任何数字。较小的基数需要较少的符号，但即使是相对较小的数字也需要我们多次使用这些符号，当打印在一页上时，就面积而言，这些数字会变得“大”。*

如果你熟悉内存在计算机中的存储方式，你可能会熟悉术语 ***位*** 和 ***字节*** 。一个比特是信息的单个二进制数字、一个`0`或一个`1`(马上就会回到这个问题)。一个字节由八个连续的位组成(也称为位的*八位字节*)。但是一个字节的定义并不总是明确的 8 位。[在 20 世纪 50 年代中后期](https://en.wikipedia.org/wiki/Byte#History)，一个“字节”的大小取决于你在和谁通话，它可以是`8`、`6`，甚至是`4`位。如今，四个连续的位(一个“四重奏”)有时也被称为`nybble`。

`2`的能力在计算机科学中非常重要，原因我们将在讨论二进制时讨论。因此，让我们从基数为 8 的数字跳到基数为 4 的数字，而不是看基数为 7 或 5 的数字。为了了解我们开发加法和乘法表的方法是如何按比例缩小的，让我们试着构建一个四元加法表。首先，我们不需要任何大于`3`的数字(因为四将由`10`表示):

| + | Zero | one | Two | three |
| --- | --- | --- | --- | --- |
| Zero |  |  |  |  |
| one |  |  |  |  |
| Two |  |  |  |  |
| three |  |  |  |  |

我们可以填充琐碎的行和列，在那里我们添加`0`:

| + | Zero | one | Two | three |
| --- | --- | --- | --- | --- |
| Zero | Zero | one | Two | three |
| one | one |  |  |  |
| Two | Two |  |  |  |
| three | three |  |  |  |

然后，让我们填入任何小于 4 的数:

| + | Zero | one | Two | three |
| --- | --- | --- | --- | --- |
| Zero | Zero | one | Two | three |
| one | one | Two | three |  |
| Two | Two | three |  |  |
| three | three |  |  |  |

现在，在总和为四的地方，我们写`10`，因为在四进制中，我们有一个*四位*和一个*一位*，所以将`1`加到`3`会导致四位递增，一位“翻转”回`0`:

| + | Zero | one | Two | three |
| --- | --- | --- | --- | --- |
| Zero | Zero | one | Two | three |
| one | one | Two | three | Ten |
| Two | Two | three | Ten |  |
| three | three | Ten |  |  |

如果我们把第四纪的`1`加到`10`上，就得到`11`；如果我们把`1`加到`11`上，我们得到`12`:

| + | Zero | one | Two | three |
| --- | --- | --- | --- | --- |
| Zero | Zero | one | Two | three |
| one | one | Two | three | Ten |
| Two | Two | three | Ten | Eleven |
| three | three | Ten | Eleven | Twelve |

太好了！我们的表格更容易填写和使用，现在也小了很多。让我们检查一下它是否有意义。在最后一排，我们有`3 base 4` + `2 base 4` = `11 base 4`。核实了吗？嗯，`11 base 4`在四位有个`1`，在一位有个`1`，所以相当于`5 base ten`，也就是`3 base ten` + `2 base ten`！好像还行！四进制乘法表看起来像:

| * | Zero | one | Two | three |
| --- | --- | --- | --- | --- |
| Zero | Zero | Zero | Zero | Zero |
| one | Zero | one | Two | three |
| Two | Zero | Two | Ten | Twelve |
| three | Zero | three | Twelve | Twenty-one |

再一次，因为`2`是基数的一半，我们得到在`2`列向下的 1 的位置的交替`0` - `2` - `0` - `2`，类似于八进制中的`4`列。在`3`列中我们还有一个*三进制补码*，尽管只有两个例子:

```
x = 2:  2 * 3 = 12 => [x] * 3 = [x-1][3-(x-1)]
x = 3:  3 * 3 = 21 => [x] * 3 = [x-1][3-(x-1)] 
```

Enter fullscreen mode Exit fullscreen mode

所以符号更少了，加法和乘法表也更容易算出来。但是较小的基数的一个缺点是数字需要更多的墨水来打印，因为它们需要更多的字符。例如，数字`123 base four`只等于`27 base ten` :

```
123 (base 4)
|||
|||____(3)__ ones = (3 * 4^0) = (3 * 1)  =   3
||
||___(2)___ fours = (2 * 4^1) = (2 * 4)  =   8
|
|__(1)__ sixteens = (1 * 4^2) = (1 * 16) =  16 
```

Enter fullscreen mode Exit fullscreen mode

...还有`3 + 8 + 16 = 27 base ten`。这种效应对于小数量来说并不明显，但是对于非常大的数量来说就变得很明显了。例如，在撰写本文时，世界人口估计约为 75.5 亿人。在十进制和四进制中，这些数字是:

```
7550000000        (decimal)
13002000331232000 (quaternary) -- 70% more digits 
```

Enter fullscreen mode Exit fullscreen mode

> *在基数`b`中写一个数`n`时，如果`n`相对于`b`较大，那么在那个基数中写`n`所需的位数与`ln(b)`成反比。由于`b`是四进制的`4`，一个大的数应该要求`ln(10)/ln(4) ~ 1.66x`的四进制位数和十进制位数一样多。* [【来源】](https://math.stackexchange.com/questions/335055/what-is-the-relationship-between-base-and-number-of-digits)

### 二进制(基数-2)

最小的有用的位置计数系统是基数 2 或 ***二进制*** 。如果我们试图使用一元系统，就像我们对计数标志所做的那样，那么*每个*“位置”就变成了*个 1 的位置*。*以 1 为基数的*编号系统意味着每个位置只代表`1` :

```
111 (base 1) = 3 (base 10)
|||
|||____(1)__ ones = (1 * 1^0) = (1 * 1) = 1
||
||___(1)____ ones = (1 * 1^1) = (1 * 1) = 1
|
|__(1)______ ones = (1 * 1^2) = (1 * 1) = 1 
```

Enter fullscreen mode Exit fullscreen mode

更糟糕的是，对于一元系统，我们没有第二个符号(根据定义)，所以我们没有`0`字符作为占位符。这使得一元系统...有点没用，对吧？这只是理货标记。相反，如果我们在(`0`)中添加第二个符号来创建一个*二进制*系统，我们可以做得更多一点。事实上，计算机能做的事情，我们都能做。

在二进制系统中，我们有两个符号:`0`和`1`。当我们把`1`加到`0`(反之亦然)，我们得到`1`。当我们将`1`加到`1`时，我们已经用完了所有的符号！因此，就像我们之前做的那样，我们增加下一个位置(在本例中，是*二进制的位置*)，并将一进制的位置减少到`0` :

```
1 + 1 = 10 (binary) 
```

Enter fullscreen mode Exit fullscreen mode

> 于是有了这个老笑话:这个世界上有 10 种人。懂二进制的，不懂的。

二进制加法表非常简单:

| + | Zero | one |
| --- | --- | --- |
| Zero | Zero | one |
| one | one | Ten |

二进制乘法表甚至更简单，因为乘以`1`和`0`的所有常规规则都适用:

| * | Zero | one |
| --- | --- | --- |
| Zero | Zero | Zero |
| one | Zero | one |

二进制数不是有一位、十位、百位等等，而是有一位、二位、四位等等，通过 2 的所有幂:

```
10101 (base 2) = 21 (base 10)
|||||
|||||______(1)__ ones = (1 * 2^0) = (1 *  1) =  1
||||
||||_____(0)____ twos = (0 * 2^1) = (0 *  2) =  0
|||
|||____(1)_____ fours = (1 * 2^2) = (1 *  4) =  4
||
||___(0)______ eights = (0 * 2^3) = (0 *  8) =  0
|
|__(1)______ sixteens = (1 * 2^4) = (1 * 16) = 16 
```

Enter fullscreen mode Exit fullscreen mode

注意，二进制表示比十进制表示要简洁得多，但是它只需要两个数字- `0`和`1`。*二进制数字*在计算机研究中变得如此普遍，以至于人们创造了一个词来缩短这个短语: *bit* 。一个比特是一个单一的二进制数字——要么是一个`0`，要么是一个`1`。

二进制是计算机的“语言”。任何可以用两种状态之一表示的东西都可以用一个比特来表示。开关可以打开或关闭，打开或关闭；电流可以在电路中流动，也可以不流动；磁铁的正极可以朝上或朝下。宇宙中充满了可以用一个比特来表示的非黑即白的情况。

计算机最初是由大量杂乱的电路、大量磁性材料和真空管组成的。在那些早期，二进制代码是(现在仍然是)在复杂电路中表示信息的最自然的方式——电流要么*是*要么*不是*流经这条特定的导线或晶体管或其他什么东西。尽管在 20 世纪 50 年代和 60 年代，[制造了几台极其成功的十进制计算机](https://en.wikipedia.org/wiki/Decimal_computer)，但是硬件的萎缩[和一系列其他因素](https://qr.ae/TW1s9c)导致二进制在过去的一个世纪中期击败了十进制。要理解计算机，就必须理解二进制。

### 十六进制(16 进制)

到目前为止，我们已经关注于从阿拉伯数字系统中移除符号，创建基数为 8、基数为 4 甚至基数为 2 的数字系统。但是如果我们尝试用*加上*符号会怎么样呢？如果我们开发一个以 16 为基数的数字系统，而不是以 10 为基数，会怎么样？一个十六进制是一个 ***十六进制*** 系统(来自“hexa”，六；以及“deca”，十)。

这一次，我们需要添加新的数字，而不是删除一些我们熟悉的阿拉伯数字。我们需要从 0 到 15 的所有数字的单字符表示:

```
 0 ("zero")     =      = 
  1 ("one")      = I    = |
  2 ("two")      = II   = ||
  3 ("three")    = III  = |||
  4 ("four")     = IV   = ||||
  5 ("five")     = V    = |||||
  6 ("six")      = VI   = ||||| |
  7 ("seven")    = VII  = ||||| ||
  8 ("eight")    = VIII = ||||| |||
  9 ("nine")     = IX   = ||||| ||||
  ? ("ten")      = X    = ||||| |||||
  ? ("eleven")   = XI   = ||||| ||||| |
  ? ("twelve")   = XII  = ||||| ||||| ||
  ? ("thirteen") = XIII = ||||| ||||| |||
  ? ("fourteen") = XIV  = ||||| ||||| ||||
  ? ("fifteen")  = XV   = ||||| ||||| ||||| 
```

Enter fullscreen mode Exit fullscreen mode

所以我们需要六个新符号。通常，我们借用罗马人的想法，用字母字符组成这六个剩余的符号，但它们真的可以是任何东西。你可以使用表情符号、箭头、翅膀或者任何让你开心的东西。不过，为了方便使用，我们使用字母表的前六个字母:

```
 0 ("zero")     =      = 
  1 ("one")      = I    = |
  2 ("two")      = II   = ||
  3 ("three")    = III  = |||
  4 ("four")     = IV   = ||||
  5 ("five")     = V    = |||||
  6 ("six")      = VI   = ||||| |
  7 ("seven")    = VII  = ||||| ||
  8 ("eight")    = VIII = ||||| |||
  9 ("nine")     = IX   = ||||| ||||
  A ("ten")      = X    = ||||| |||||
  B ("eleven")   = XI   = ||||| ||||| |
  C ("twelve")   = XII  = ||||| ||||| ||
  D ("thirteen") = XIII = ||||| ||||| |||
  E ("fourteen") = XIV  = ||||| ||||| ||||
  F ("fifteen")  = XV   = ||||| ||||| ||||| 
```

Enter fullscreen mode Exit fullscreen mode

在这个编码中，`B`的意思是“十一”。这与在十进制系统中表示“11”的`11`一样有效。这两种制度本身没有“好”或“坏”之分。当它们适合我们时，我们使用不同的碱基。让我们看看十六进制加法表，先填写到`F`的所有内容:

| + | Zero | one | Two | three | four | five | six | seven | eight | nine | A | B | C | D | E | F |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Zero | Zero | one | Two | three | four | five | six | seven | eight | nine | A | B | C | D | E | F |
| one | one | Two | three | four | five | six | seven | eight | nine | A | B | C | D | E | F |  |
| Two | Two | three | four | five | six | seven | eight | nine | A | B | C | D | E | F |  |  |
| three | three | four | five | six | seven | eight | nine | A | B | C | D | E | F |  |  |  |
| four | four | five | six | seven | eight | nine | A | B | C | D | E | F |  |  |  |  |
| five | five | six | seven | eight | nine | A | B | C | D | E | F |  |  |  |  |  |
| six | six | seven | eight | nine | A | B | C | D | E | F |  |  |  |  |  |  |
| seven | seven | eight | nine | A | B | C | D | E | F |  |  |  |  |  |  |  |
| eight | eight | nine | A | B | C | D | E | F |  |  |  |  |  |  |  |  |
| nine | nine | A | B | C | D | E | F |  |  |  |  |  |  |  |  |  |
| A | A | B | C | D | E | F |  |  |  |  |  |  |  |  |  |  |
| B | B | C | D | E | F |  |  |  |  |  |  |  |  |  |  |  |
| C | C | D | E | F |  |  |  |  |  |  |  |  |  |  |  |  |
| D | D | E | F |  |  |  |  |  |  |  |  |  |  |  |  |  |
| E | E | F |  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| F | F |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |

...`F`之后是什么？嗯，我们做的事情与之前所有的基数相同:增加下一个位置(在本例中是*十六位*)并将当前位置减少回`0`。所以`F + 1 = 10`用十六进制表示:

| + | Zero | one | Two | three | four | five | six | seven | eight | nine | A | B | C | D | E | F |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Zero | Zero | one | Two | three | four | five | six | seven | eight | nine | A | B | C | D | E | F |
| one | one | Two | three | four | five | six | seven | eight | nine | A | B | C | D | E | F | Ten |
| Two | Two | three | four | five | six | seven | eight | nine | A | B | C | D | E | F | Ten |  |
| three | three | four | five | six | seven | eight | nine | A | B | C | D | E | F | Ten |  |  |
| four | four | five | six | seven | eight | nine | A | B | C | D | E | F | Ten |  |  |  |
| five | five | six | seven | eight | nine | A | B | C | D | E | F | Ten |  |  |  |  |
| six | six | seven | eight | nine | A | B | C | D | E | F | Ten |  |  |  |  |  |
| seven | seven | eight | nine | A | B | C | D | E | F | Ten |  |  |  |  |  |  |
| eight | eight | nine | A | B | C | D | E | F | Ten |  |  |  |  |  |  |  |
| nine | nine | A | B | C | D | E | F | Ten |  |  |  |  |  |  |  |  |
| A | A | B | C | D | E | F | Ten |  |  |  |  |  |  |  |  |  |
| B | B | C | D | E | F | Ten |  |  |  |  |  |  |  |  |  |  |
| C | C | D | E | F | Ten |  |  |  |  |  |  |  |  |  |  |  |
| D | D | E | F | Ten |  |  |  |  |  |  |  |  |  |  |  |  |
| E | E | F | Ten |  |  |  |  |  |  |  |  |  |  |  |  |  |
| F | F | Ten |  |  |  |  |  |  |  |  |  |  |  |  |  |  |

我们可以继续将`1`加到大于`F`的数字上，直到`1F`之前，我们不会进行另一次“翻转”，这在加法表中不会发生:

| + | Zero | one | Two | three | four | five | six | seven | eight | nine | A | B | C | D | E | F |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Zero | Zero | one | Two | three | four | five | six | seven | eight | nine | A | B | C | D | E | F |
| one | one | Two | three | four | five | six | seven | eight | nine | A | B | C | D | E | F | Ten |
| Two | Two | three | four | five | six | seven | eight | nine | A | B | C | D | E | F | Ten | Eleven |
| three | three | four | five | six | seven | eight | nine | A | B | C | D | E | F | Ten | Eleven | Twelve |
| four | four | five | six | seven | eight | nine | A | B | C | D | E | F | Ten | Eleven | Twelve | Thirteen |
| five | five | six | seven | eight | nine | A | B | C | D | E | F | Ten | Eleven | Twelve | Thirteen | Fourteen |
| six | six | seven | eight | nine | A | B | C | D | E | F | Ten | Eleven | Twelve | Thirteen | Fourteen | Fifteen |
| seven | seven | eight | nine | A | B | C | D | E | F | Ten | Eleven | Twelve | Thirteen | Fourteen | Fifteen | Sixteen |
| eight | eight | nine | A | B | C | D | E | F | Ten | Eleven | Twelve | Thirteen | Fourteen | Fifteen | Sixteen | Seventeen |
| nine | nine | A | B | C | D | E | F | Ten | Eleven | Twelve | Thirteen | Fourteen | Fifteen | Sixteen | Seventeen | Eighteen |
| A | A | B | C | D | E | F | Ten | Eleven | Twelve | Thirteen | Fourteen | Fifteen | Sixteen | Seventeen | Eighteen | Nineteen |
| B | B | C | D | E | F | Ten | Eleven | Twelve | Thirteen | Fourteen | Fifteen | Sixteen | Seventeen | Eighteen | Nineteen | 1A |
| C | C | D | E | F | Ten | Eleven | Twelve | Thirteen | Fourteen | Fifteen | Sixteen | Seventeen | Eighteen | Nineteen | 1A | 1B |
| D | D | E | F | Ten | Eleven | Twelve | Thirteen | Fourteen | Fifteen | Sixteen | Seventeen | Eighteen | Nineteen | 1A | 1B | 1C |
| E | E | F | Ten | Eleven | Twelve | Thirteen | Fourteen | Fifteen | Sixteen | Seventeen | Eighteen | Nineteen | 1A | 1B | 1C | 1D |
| F | F | Ten | Eleven | Twelve | Thirteen | Fourteen | Fifteen | Sixteen | Seventeen | Eighteen | Nineteen | 1A | 1B | 1C | 1D | 1E |

然而，乘法表是一个不同的故事。它可能看起来像一堆混乱的字母和数字，但我重复一遍，它和其他任何编号系统一样有效。外行人只是不熟悉:

| * | Zero | one | Two | three | four | five | six | seven | eight | nine | A | B | C | D | E | F |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Zero | Zero | Zero | Zero | Zero | Zero | Zero | Zero | Zero | Zero | Zero | Zero | Zero | Zero | Zero | Zero | Zero |
| one | Zero | one | Two | three | four | five | six | seven | eight | nine | A | B | C | D | E | F |
| Two | Zero | Two | four | six | eight | A | C | E | Ten | Twelve | Fourteen | Sixteen | Eighteen | 1A | 1C | 1E |
| three | Zero | three | six | nine | C | F | Twelve | Fifteen | Eighteen | 1B | 1E | Twenty-one | Twenty-four | Twenty-seven | 2A | 2D |
| four | Zero | four | eight | C | Ten | Fourteen | Eighteen | 1C | Twenty | Twenty-four | Twenty-eight | 2C | Thirty | Thirty-four | Thirty-eight | 3C |
| five | Zero | five | A | F | Fourteen | Nineteen | 1E | Twenty-three | Twenty-eight | 2D | Thirty-two | Thirty-seven | 3C | Forty-one | Forty-six | 4B |
| six | Zero | six | C | Twelve | Eighteen | 1E | Twenty-four | 2A | Thirty | Thirty-six | 3C | forty-two | Forty-eight | 4E | Fifty-four | 5A |
| seven | Zero | seven | E | Fifteen | 1C | Twenty-three | 2A | Thirty-one | Thirty-eight | 3F | Forty-six | 4D | Fifty-four | 5B | Sixty-two | sixty-nine |
| eight | Zero | eight | Ten | Eighteen | Twenty | Twenty-eight | Thirty | Thirty-eight | Forty | Forty-eight | Fifty | Fifty-eight | Sixty | sixty-eight | Seventy | seventy-eight |
| nine | Zero | nine | Twelve | 1B | Twenty-four | 2D | Thirty-six | 3F | Forty-eight | Fifty-one | 5A | Sixty-three | 6C | Seventy-five | 7E | Eighty-seven |
| A | Zero | A | Fourteen | 1E | Twenty-eight | Thirty-two | 3C | Forty-six | Fifty | 5A | Sixty-four | 6E | seventy-eight | Eighty-two | 8C | Ninety-six |
| B | Zero | B | Sixteen | Twenty-one | 2C | Thirty-seven | forty-two | 4D | Fifty-eight | Sixty-three | 6E | Seventy-nine | Eighty-four | 8F | 9A | A5 号 |
| C | Zero | C | Eighteen | Twenty-four | Thirty | 3C | Forty-eight | Fifty-four | Sixty | 6C | seventy-eight | Eighty-four | Ninety | 9C | A8 | B4 |
| D | Zero | D | 1A | Twenty-seven | Thirty-four | Forty-one | 4E | 5B | sixty-eight | Seventy-five | Eighty-two | 8F | 9C | A9 | B6 | C3 |
| E | Zero | E | 1C | 2A | Thirty-eight | Forty-six | Fifty-four | Sixty-two | Seventy | 7E | 8C | 9A | A8 | B6 | 补体第四成份缺乏 | D2 |
| F | Zero | F | 1E | 2D | 3C | 4B | 5A | sixty-nine | seventy-eight | Eighty-seven | Ninety-six | A5 号 | B4 | C3 | D2 | E1 |

通过查看上表中的`2`栏可以看出`A`、`C`和`E`是十六进制的*偶数*数。在`8`栏中，你也可以看到我们在八进制的`4`中注意到的熟悉的`8-0-8-0`行为。`F`列还显示了(在`1`行之后)两位数，其中的数字是 *F 的补码*(`(1 + E) = (2 + D) = ... = F`)，就像我们之前看到的十进制和八进制一样。最后，上表对角线上的数字也是这些数字的平方，所以`2^2 = 4`、`3^2 = 9`和`4^2 = 10`，记住，这意味着十六进制的`1`十六和`0`一。

这些底座真的很美。无论你的基数是 10、8 还是 16，都会出现相同的规律。这些系统中的任何一个在特定的情况下都是有用的，事实上它们被计算机科学家经常使用。在以 10 为基数之后，计算机科学中最常见的两种基数可能是以 2 为基数(二进制)和以 16 为基数(十六进制)。

## 处理多个碱基

### 基数- `N`小数点

有时，我们需要处理不同基数的数字，并且我们需要能够从一个基数到另一个基数来回转换。通常，把非十进制转换成十进制是很容易的。你只需要记住每个位置在给定的基数下是如何定义的:

```
base-N

[4][1][2][3]
 |  |  |  |
 |  |  |  |__ ones' place (always)
 |  |  |_________ N place
 |  |________ (N^2) place
 |___________ (N^3) place, etc. 
```

Enter fullscreen mode Exit fullscreen mode

所以要从基数- `N`转换成十进制，你要把最右边的数字乘以`1`；并把它加到下一个数字上，再乘以`N`；并把它加到下一个数字上，再乘以`N^2`；...

```
base-N to decimal

[4][1][2][3] => 3 + (2 * N) + (1 * N^2) + (4 * N^3) = ... 
```

Enter fullscreen mode Exit fullscreen mode

显然，如果数字中有一个`4`，那么基数至少必须是`N = 5`(因为，记住，在基数- `N`系统中没有`N`的符号，就像在基数-10 系统中没有“十”的符号一样)。假设上面的基数是`N = 5`，那么:

```
base-5 to decimal

[4][1][2][3] => 3 + (2 * 5) + (1 * 5^2) + (4 * 5^3) = 3 + 10 + 25 + 500 = 538 base ten 
```

Enter fullscreen mode Exit fullscreen mode

### 十进制转为二进制

将*从*十进制转换成特定的基数有点棘手。假设我们有一个数字`637 base ten`，我们想把它转换成二进制(基数为 2)。我们必须找到`2`的最大幂，我们可以从原始数字`637 base 10`中减去它，其中减法的结果不是负数。我们来做一个`2` :
的权力简表

```
 2^0 =    1
 2^1 =    2
 2^2 =    4
 2^3 =    8
 2^4 =   16
 2^5 =   32
 2^6 =   64
 2^7 =  128
 2^8 =  256
 2^9 =  512
2^10 = 1024 
```

Enter fullscreen mode Exit fullscreen mode

看起来`2^9`是`2 base ten`的最大幂，我们可以从`637 base ten`中减去它，而结果不会是负的:

```
637 - 2^9 = 637 - 512 = 125 
```

Enter fullscreen mode Exit fullscreen mode

太好了！记住`2^9`让我们继续前进。我们现在有了`125 base 10`。我们可以从*中减去`2`的最大功率这个*就是`2^6` :

```
125 - 2^6 = 125 - 64 = 61 
```

Enter fullscreen mode Exit fullscreen mode

好吧！也写下`2^6`，让我们继续几个术语:

```
61 - 2^5 = 61 - 32 = 29  (remember 2^5)
29 - 2^4 = 29 - 16 = 13  (remember 2^4)
13 - 2^3 = 13 -  8 =  5  (remember 2^3)
 5 - 2^2 =  5 -  4 =  1  (remember 2^2)
 1 - 2^0 =  1 -  1 =  0  (...and   2^0) 
```

Enter fullscreen mode Exit fullscreen mode

太棒了。我们在这里使用的幂赋予我们基数为 2 的数的*位置*，它们是`1`，而不是`0`。在这种情况下，*第一个*位置编号为`0`(代表一位的`2^0 = 1`)，第二个*第二个*编号为`1`(代表两位的`2^1 = 2`)。所以我们在第 0、第 2、第 3、第 4、第 5、第 6 和第 9 个位置有`1`，在其他地方有`0`:

```
[1][0][0][1][1][1][1][1][0][1] = 1001111101 = 637 base ten 
```

Enter fullscreen mode Exit fullscreen mode

### 十进制到八进制

对于二进制，我们只能在给定的位置有一个`1`或一个`0`。我们可以从一个数字中减去`2`的特定幂，也可以不减(不变负)。基数越大，难度就越大。例如，让我们试试八进制。

让我们把`637 base ten`转换成八进制。我们开始写 8 的幂(以 10 为基数):

```
8^0 =    1
8^1 =    8
8^2 =   64
8^3 =  512
8^4 = 4096 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们从`637 base ten` - `512 base ten` ( `8^3` ):
中减去最大的一个

```
637 - 512 = 125 
```

Enter fullscreen mode Exit fullscreen mode

从`125 base ten`我们可以减去`64 base ten` ( `8^2`)一次:

```
125 - 64 = 61 
```

Enter fullscreen mode Exit fullscreen mode

在这一点上，我们遇到了与二进制不同的情况。这里，我们可以减去 8 的幂次*乘以*。事实上，我们可以减去`8^1`(或者只是`8`)正好七次:

```
61 - (8^1)*7 = 61 - 56 = 5 
```

Enter fullscreen mode Exit fullscreen mode

那么，我们必须减去`1` ( `8^0`)五次:

```
5 - (8^0)*5 = 5 - 5 = 0 
```

Enter fullscreen mode Exit fullscreen mode

所以总的来说，我们发现`637 base ten`等于...

```
637 = (1 * 8^3) + (1 * 8^2) + (7 * 8^1) + (5 * 8^0) 
```

Enter fullscreen mode Exit fullscreen mode

仔细检查上面等式的右边是否等于以十为基数的左边。为了将十进制转换为八进制，我们取上面的八次幂的乘法因子(`1`、`1`、`7`、`5`)，并对它们进行排列，使得第 0 次幂是最右边的数字，然后第 1 次幂是最左边的数字，以此类推:

```
637 base ten = 1175 base 8 
```

Enter fullscreen mode Exit fullscreen mode

### 十六进制到十进制

对于网页设计师来说，可能你经常遇到的最常见的非十进制数字系统是十六进制系统。十六进制代码——或者简称为“hex”代码——被用来描述网页上使用的颜色。例如，`#3CB371`是一种偏蓝、偏绿、长满苔藓的颜色；`#708090`是一种非常略带蓝色的灰色；`#D2691E`是橙色/棕色；诸如此类。

但是这些数字和它们所代表的颜色是怎么对应的呢？

嗯，一个十六进制代码实际上是*三个*十六进制数字合二为一:

```
#3CB371 => [3C] [B3] [71]
#708090 => [70] [80] [90]
#D2691E => [D2] [69] [1E] 
```

Enter fullscreen mode Exit fullscreen mode

两位十六进制数的范围是从`00`到`FF`，其中`00`相当于`0 base ten`,`FF`表示`F`十六进制，`F`表示十六进制。`F base 16`和`15 base 10`一样，所以:

```
hexadecimal: FF base 16 = ( F * 16) + ( F * 1) = 
    decimal:              (15 * 16) + (15 * 1) = 255 
```

Enter fullscreen mode Exit fullscreen mode

所以一个两位数的十六进制数可以编码从`0 base ten`到`255 base ten`之间的任何数，包括 T0 和 T1。上面的六位十六进制代码给出了`0-255 base ten`系列的三个数字。这些数字给出了颜色
中*红色*、*绿色*和*蓝色*的相对“数量”

```
 red      green     blue         red     green     blue
#3CB371 => [3C] [B3] [71] => [ 60/255] [179/255] [113/255] => [23.53%] [70.20%] [44.31%]
#708090 => [70] [80] [90] => [112/255] [128/255] [144/255] => [43.92%] [50.20%] [56.47%]
#D2691E => [D2] [69] [1E] => [210/255] [105/255] [ 30/255] => [82.35%] [41.18%] [11.76%] 
```

Enter fullscreen mode Exit fullscreen mode

你屏幕上的像素实际上是三个“像素”合二为一——有一小点红色、一小点绿色和一小点蓝色。十六进制代码中的数字告诉每个像素块应该发出多亮的光。如果这三种光以同样的强度照射，颜色会呈现为白色、黑色或介于两者之间的灰色。当三种颜色以不同的相对强度发光时，我们几乎可以得到任何可以想象的颜色。

## 总结

我希望这是一个有用的介绍，介绍了不同的基数，我们如何处理这些基数中的数字，以及二进制和十六进制数字是多么有用。虽然一开始它们看起来很可怕，但是处理二进制和十六进制数字就像处理小数一样简单——你只需要练习！*
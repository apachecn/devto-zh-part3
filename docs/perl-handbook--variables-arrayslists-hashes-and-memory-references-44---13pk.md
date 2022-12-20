# Perl 手册:变量、数组/列表、散列和内存引用(4/4)

> 原文：<https://dev.to/vitalipom/perl-handbook--variables-arrayslists-hashes-and-memory-references-44---13pk>

### 变量、数组/列表、散列和内存引用

我们可能想要匹配我们之前存储在两个或更多正则表达式中的东西，或者可能更实际一点的东西:在变量中存储我们发现的东西，即使我们还不知道它是什么。这很酷。

看下面这个程序:

```
my $name1 = undef;
my $line = “Her name was Angel and she behaved like an angel… When she was sleeping.”
if($line =~ /.+ name .+ (.+)/){
    $name1 = $1;
} 
```

Enter fullscreen mode Exit fullscreen mode

多新鲜的东西啊。嗯，一如既往，一切都相当简单

```
`my` - Would be the security statement which tell us that variables ‘name’ and `line` are active only within the current .pl file 
   (.pl is a typical extension for Perl files) 
=~ - Would act like the usually disabled == for strings 
   (the one instead of which we’d usually use Compare() in other programming languages), 
   it’s just in Perl =~ stands for ‘matches’. 
   We behave as if $line  ‘looks like’ the following up regex. 
$1 - Would refer for the brackets as with some additional meaning, where they store inside the following up block anything that was inside them and within an appropriate group number: 
   first pair pair of brackets inside the regex would be inside $1, second pair of brackets inside the same regex would be inside $2, etc. 
```

Enter fullscreen mode Exit fullscreen mode

复杂？不。但是如果我们想解析整个文件呢？

为了解析整个文件，我们有文件处理程序——一些我们打开文件的引用，我们可以从中读取和写入。同样没什么特别的，下面是一些代码:

```
open FILE;
while(<FILE>){
    my $line = $_;
    print $line; 
}
close FILE; 
```

Enter fullscreen mode Exit fullscreen mode

再次解释一下这个例子:

```
FILE - Would be the file handler,
   which is very important to close once you open it.
<FILE> - Would be the act of reading a line from the FILE, 
   which also returns a positive value when succeeds
   and fails at EOF (End Of File - the last char of a file).
$_ - Would be the read line from the file. 
```

Enter fullscreen mode Exit fullscreen mode

#### 一旦我们想要真正地分析我们读到的内容并将其存储到一个典型的 Perlish 数据结构中，事情就变得有点困难了。为此我们有散列，列表，这里我们也表示内存引用。

***首先，在 Perl 中- List 和 Array 是同一个家伙。*T3】**

我是一个数组，在 Perl 中我也是一个列表，这意味着你可以通过 push、pop 和通常的数组索引来访问我。事实上，我是一个数组列表，这并不意味着你可以简单地把东西推到我中间，但这意味着你可以把我和其他像我一样的人连接起来。

`print “$array[1]\n”; #prints b`

```
my @weirdArray = (‘boo’, ‘waaa’);
push @arr, @weirdArray; 
#now @arr = (‘a’, ‘b’, ‘c’, ‘d’, 1, 2, 3, ‘boo’, ‘waaa’) 
```

Enter fullscreen mode Exit fullscreen mode

所以现在当我们知道 Perlish 数组是奢侈的和特殊的……那么我们想要用来分析的散列呢？准备好一个很长的例子。

## 真长的例子

# Perl 哈希

假设我们有一篇描述卧室的课文，卧室里有一个壁橱和一堆袋子。我们有一个描述它的文本，我们想把那个房间“模拟”成一个散列。为什么是哈希？因为即使 Perl 确实有对象，你可能也不想去猎取它们，因为使用它们的工作很复杂，也许在日常使用中更现实地看待 Perl 对象的不可访问性。至于其他数据存储选项，Perl 像任何基本编程语言一样，数量非常有限，假设我们没有也不会涉及 Perl 对象，您可以很容易地在脑海中列出它们。

例(注意！一个长的):

进入模式室...房间里有壁橱。
衣柜内检测出袜子。
在壁橱里发现毛衣。
衣柜里检出手套。
打开第二扇门…
关闭厕所异常:关闭厕所。

房间里有小包。
小包内检出口红。
小包内检测到化妆品。
小袋内检测到组织。
小包内检测到黑洞。
退出小包。
房间有大包。
进入大行李…
行李错误:离开行李。请买个新包，然后再来。

在开始用代码包装我们的房间日志文件之前，我们将首先尝试表示我们将如何看到结果数据模型:

```
-room
    -closet
        -socks
        -sweater
        -gloves
   -small bag
        -lipstick 
        -makeup
        -tissues
        -black hole
   -big bag
        N/A 
```

Enter fullscreen mode Exit fullscreen mode

当然还有承诺的代码块:

```
open FILE;
my %room;
my $storage = undef;

while(<FILE>){
    my $line = $_;
    my $found_stuff = undef;
    if($line =~ /Room has (.+)/i){
        $storage = $1;
        $room{$storage} = ();
        continue;
    }
    if($line =~ /(.+) detected inside the $storage/i){
        $found_stuff = $1;
        ##push $found_stuff, into the Array that is located inside the Hash %room under the key $storage 
        push @{$room{$storage}} , $found_stuff;    
   }
}
close FILE; 
```

Enter fullscreen mode Exit fullscreen mode

```
my $four_spaces = “    “;

print “-room\n”;
foreach my $hashed_storage(keys %room){ 
    print “$four_spaces”;
    print “-$hashed_storage\n”;
    foreach my $hashed_stuff (@{$room{$hashed_storage}}){
        #print everything just like in the text example
        print “$four_spaces”;
        print “-$hashed_stuff\n”; 
    } 
} 
```

Enter fullscreen mode Exit fullscreen mode

我们打赌你已经猜到了，这次花了 5 分多一点的时间将想法转储到代码中。由于劳累的工作和疲劳的神经不停地流汗所导致的焦虑，我们将直接给出解释

```
undef - would act like null, but in Perlish style 
```

Enter fullscreen mode Exit fullscreen mode

```
defined - would return true if the variable is defined 
```

Enter fullscreen mode Exit fullscreen mode

```
foreach - would be in the form of ‘foreach $variable(list of values)’ where the loop goes on each    and every value and stores it into variable before entering the loop scope. 
```

Enter fullscreen mode Exit fullscreen mode

```
@{} - would be a dereferenced Array. We stored a reference to an empty Array, now we need to push some value into it. 
```

Enter fullscreen mode Exit fullscreen mode

```
continue - would continue straight to the next iteration of the enclosing loop 
```

Enter fullscreen mode Exit fullscreen mode

```
keys - would return a list of the keys in the following up hash table 
```

Enter fullscreen mode Exit fullscreen mode

```
% - would be like @ for arrays, just for hashes.
Hashes in their simplest form as you might have understood are accessed like the following:
$hash{‘key’} = ‘value’; 
```

Enter fullscreen mode Exit fullscreen mode

所以我们提到了@{}，它作为一个数组解引用。就 Perl 编程而言，同样的解引用术语也适用于散列和变量。让我们更仔细地看看我们遇到的解引用/引用术语。

## 解除引用/引用仔细看

假设我们有一些彩色笔，我们想把它们放进我们的学校盒子里，但是铅笔盒似乎是在我们的 Perl 代码中在学校盒子之前创建的。在现实生活的故事案例中，我们买了分类铅笔盒，我们在一些商店为我们的学校盒买了一些东西，我们在一些第三商店买了一个学校盒。现在我们想把所有东西都放在学校盒子里，并用 Perl 语言建模。

继续假设，这一次请假设你有下面的代码块，它已经在一些流程中运行，你决定把你写的任何东西添加到它的末尾，以避免破坏已经启动并运行的东西——不幸的是，对你来说，工作也是如此。

```
@pens = (‘red_pen’, ‘pink_pen’, ‘yellow_pen’, ‘orange_pen’, ‘cyan_pen’, ‘blue_pen’);
%school_box = (‘eraser’ => ‘Sarahs_eraser’, ‘round_object’ => ‘bottle_cap’); 
```

Enter fullscreen mode Exit fullscreen mode

***后来由工程师鲍勃加上。测试和工作。IT 部门运作所必需的。*T3】**

```
$school_box{‘paper_reminder_note’} = ‘Reminder: Pay back the loan to Bob from IT by the end of the year’; 
```

Enter fullscreen mode Exit fullscreen mode

所以我们有了这个学生学校盒子模型，工程师 Bob 已经成功地装载了一些纸笔记提醒，现在我们决定把笔数组放在里面。我们首先想到的是这样做:

```
 #### $school_box{‘pens’} = @pens;
#Because why not 
```

Enter fullscreen mode Exit fullscreen mode

我们发现在“pens”键下有一些数字。

* * *

### 偏离主题

这是我们又一次遇到某种特殊的字符，它在程序的不同位置表现不同，就像正则表达式中的美元符号:

/hello world！$/

这里我们有一个正则表达式，它可以匹配所有以字符串“hello world！”结尾的行。
范思哲:

my $ name = " John
/name 是$name/

这里我们有一个正则表达式，可以匹配所有包含字符串“name is John”的地方。

回到我们那个奇怪的数字，我们承诺过，如果你在你的机器上运行程序，你会得到秘密，而不是数组。对于数组的“@”字符,“出现在不同位置时表现不同”的情况适用。当您将数组原样赋给变量时，变量将获得数组的长度:

`my $number_of_pens = @pens; #NO!`

你可以从它的名字猜出$number 支钢笔里面的数字。

我们回到开始讨论的最初问题，将整个数组存储在 hash(也称为数组 hash)中的某个键下。

* * *

为此，我们需要存储一些对数组的引用，[参见我们的第一篇离题独白，了解我们为什么需要引用]这是用反斜杠:
完成的

```
my $pens_arr_ref= \@pens; 
```

Enter fullscreen mode Exit fullscreen mode

为了将它解引用回 pens 数组，我们做:

```
my @orig_pens_arr = @{$pens_arr_ref}; 
```

Enter fullscreen mode Exit fullscreen mode

最终我们得到了下面的学校盒子模型程序:

```
@pens = (‘red_pen’, ‘pink_pen’, ‘yellow_pen’, ‘orange_pen’, ‘cyan_pen’, ‘blue_pen’);
%school_box = (‘eraser’ => ‘Sarahs_eraser’, ‘round_object’ => ‘bottle_cap’);

#####
#Added later by Bob the Engineer. Tested and working. Necessary for IT department to function.
#####
$school_box{‘paper_reminder_note’} = ‘Reminder: Pay back the loan to Bob from IT by the end of the year’; 

#####
# Added by John from Angela’s team
# P.S Guys, pay your bills ;)
#####

####
###Uncomment for debug
##
#
#$school_box{‘pens’} = \@pens;
#
#foreach my $pen (@{$school_box{‘pens’}){      #Dereferencing in action 
#   print “$pen detected. You’ll forget what you read.\n”
#}
#
##
#### 
```

Enter fullscreen mode Exit fullscreen mode

您可能会问自己，当您想要打印整个数组时，应该怎么做。我们没有涉及任何关于检查我们是否在看一个引用或者一个普通的人类可读变量的内容。基于这一点，您不需要在日常工作中随机访问数据结构中的内容，Perl 有一个用于调试目的的特殊包，就像您通常在脚本语言中使用的许多其他包一样。如果你在某个大公司工作，或者使用某个支持 Perl 的 SDK，你可能已经在你的 Perl 发行版中有了所有你需要的包——这样你就不会在下载安装过程中出错。无论如何，对于基础知识之外的包和信息，请继续在线研究，因为这不在本 Perl 教程的范围之内。

在这个非正式的结束语中，我们建议你保持开放的心态，继续寻找任何对你的程序在你最喜欢的搜索引擎上运行有用的东西。掌握了 Perl 的基础知识，假设您已经进行了一些实践(如果您还没有这样做，那么就去做吧！)现在，您可以开始编写自己的脚本，并在浏览网页、阅读家庭书籍和与同事在咖啡店时发现小的 Perli-lian 技巧。

# 感谢阅读:)

# 去争取吧，你拥有了！

# EOF
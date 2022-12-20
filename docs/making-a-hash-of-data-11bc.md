# 制作数据散列

> 原文：<https://dev.to/fluffy/making-a-hash-of-data-11bc>

当我在 web 发布引擎中用 PonyORM 替换 peewee 时，我评估了几种选择，包括完全放弃 ORM，简单地将元数据存储在内存的索引表中。这也有助于解决一些小的恼人的设计问题(比如将实际内容状态不正确地封装到应用程序实例中)，但是我最终没有这样做。

一个很大的原因是，对于 Python 来说，实际上似乎没有任何有用的内存索引表库。或许多其他语言。

早在 90 年代，当我还是一名年轻的软件工程师时，教授数据结构的常见做法总是从基础开始:链表，然后是二叉树(首先是天真的，然后是自平衡的，如 [AVL](https://en.wikipedia.org/wiki/AVL_tree) 或[红黑树](https://en.wikipedia.org/wiki/Red%E2%80%93black_tree)，可能还会尝试一下 [B 树](https://en.wikipedia.org/wiki/B-tree)，然后是*然后是*哈希表，通常从固定哈希表开始，然后是动态哈希表。哈希表本身甚至经常被视为事后的想法，一种在一般情况下没有用处的性能优化！同时，基于排序列表和类似列表的二分搜索法算法也将是课程的主要部分。

但是，在某些方面，似乎每个人都已经从有序的索引结构转向了基于散列表的结构。不知何故，作为一个行业，我们都认为能够在固定键上进行 O(1)常量时间查找是非常重要的，它们之间没有排序的必要。

> 作为题外话，我不认为这与经验有任何关系，而是与人们学习数据结构的环境有关；我的计算机科学教育*非常*关注数据结构和算法的工作方式，但是似乎所有和我交谈过的年轻程序员(哦，天哪，我现在听起来太老了)都被教导说，除了作为实现细节或基本理论，数据结构本身并不那么重要，此外，计算机有如此大量的可用资源，以至于你*真的*不需要关心这个(哦，天哪，我*现在已经*这么老了)。

C++和 Java 都提供了有序的关联结构。以 [`std::map`](http://www.cplusplus.com/reference/map/map/) 和 [`TreeMap<T>`](https://docs.oracle.com/javase/7/docs/api/java/util/TreeMap.html) 为例。但是现在每当你问一个程序员(尤其是一个年轻的程序员)关于它们的问题时，人们只是指出它们很“慢”,因为它们是用于键查找的 O(log₂ N ),并且你应该只使用`std::unordered_map`或`HashMap<T>`来代替，因为它们是 O(1)。

但是专注于此会忘记一些事情:

*   big-O 复杂度因子对于非常大的 N 值很重要，忽略了底层计算的恒定开销(在字符串散列的情况下，这可能非常慢，尤其是与少量的词法比较相比)
*   单键查找并不是你唯一想对数据做的事情！

有序索引对很多东西来说真的非常有用。例如，尝试查找与前缀匹配的所有关键字，或者查找大于或小于某个关键字的值(或者进行一般的范围查询)。或者快速找到内容存储中的下一个或前一个条目。

所有这些都需要全表扫描——这意味着 O(N)操作——在只有散列的场景中。或者，如果你想做一个范围查询，然后在最后排序，它需要 O(N log₂ N)，因为你必须首先过滤表(这是 O(N))然后排序(这是 O(N log₂ N))。这比仅仅使用 O(log₂ N)一次性查找更有效吗？

### 面试应聘者

作为一名全职软件工程师，我经常要做的一件事就是面试其他求职的工程师。我的一个主要问题是写一个[字谜](https://en.wikipedia.org/wiki/Boggle)求解器。该解决方案通常分为三个阶段:

1.  确定总体算法
2.  给定一个函数，用于确定单词列表中是否存在前缀，遍历棋盘以找到解决方案
3.  实现用于确定前缀是否存在的函数

第三阶段通常是最难的部分，也是大多数候选人最头疼的部分。不过，这个问题有非常简单的解决方案。你可以首先在一个数组(O(N log₂ N))中对单词列表进行排序，然后对每个单词进行 O(log₂ N)前缀搜索，或者你可以把它存储在一个树型映射中(初始存储也是 O(N log₂ N))，然后对每个单词进行 O(log₂ N)下界搜索， 或者，您可以像大多数候选人一样，将单词列表存储在哈希表(O(N))中，并在整个表中搜索每个检查(也是 O(N))，或者他们构建一个 [trie](https://en.wikipedia.org/wiki/Trie) 来存储所有单词，并带有一个关于节点是否是叶子的标志(即，单词是否完整)，这本质上是一个 O(N)初始阶段和一个 O(L)查找(其中 L 是单词的长度)。 这些都是可以接受的解决方案，*但是*你必须为每件事写的代码量是...高度可变。

例如，在 C++中，下面是如何对`std::set` :
进行前缀搜索

```
bool has_prefix(const std::set<string>& wordlist, std::string prefix) {
    auto iter = wordlist.lower_bound(prefix);
    return iter != wordlist.end && iter->substr(0, prefix.length()) == prefix;
} 
```

或者这是你如何在 Java 中用`TreeSet<String>` :
来做这件事

```
boolean has_prefix(TreeSet<String> wordlist, string prefix) {
    String first = wordlist.floor(prefix);
    return first != null && first.startsWith(prefix);
} 
```

或者，如果你使用一种没有基于树的集合概念的语言，比如 Python，并且你把你的字典存储在一个排序列表中:

```
def has_prefix(wordlist, prefix):
    import bisect
    index = bisect.bisect_left(wordlist, prefix)
    return index < len(wordlist) and wordlist[index].startswith(prefix) 
```

我不知道这个概念是从哪里消失的，也不知道为什么每当我向人们询问他们选择的语言中的索引数据结构时，他们看着我，好像我长了一两个脑袋。即使在了解索引数据结构的工程师中，我得到的反对意见也是它并不真正相关，我们都应该只是用哈希表构建所有东西或者一直进行全表扫描。我觉得这一切都来自大约十年前，当时“地图简化”思维突然占据了整个行业，所有软件开发都基于大数据、大规模扩展和并行集群。对我来说，这真的很奇怪。就像，不是所有的问题都存在于那个范围，你知道吗？

(我的意思是 Map-Reduce 在小范围内也很有用，只是它不是通用的推理方式。尤其是当它的借口归结为，“嗯，有 CPU 能力闲置，为什么麻烦？”)

### 进一步面试挫折

当我试图反省那些什么都依赖哈希表的软件工程师时，我试图弄清楚他们是否知道哈希表是如何工作的。

几乎*无一例外*，被问及底层数据结构的候选人最终以获取密钥开始，在其上构建散列，然后*将密钥插入二叉树*。这太令人恼火了！这意味着他们为二叉树的复杂性*和哈希表的有限能力*买单——这是一种两败俱伤的情况。没有与 O(log₂ N)查找相结合的有序遍历。当我向其他人抱怨候选人不理解这些基础知识时，我得到的反馈是，也许我不应该指望人们记住计算机科学入门课程(通常暗示我与之交谈的人也不知道)。

我们是如何走到这一步的？

### C++

无论如何，仅仅出于好奇，我决定在实现一个拼字游戏解算器的几种不同方法之间做一个时间比较；这些算法之间的唯一区别是基于所讨论的数据结构的`has_prefix`的实现。(还要注意，代码本身是我多年前为一个非常具体的挑战编写的，我为本文所做的一切就是改变使用的数据结构。这肯定不是高质量的代码，我怀疑如果我真的看了大部分代码，我现在会被它吓到。)

使用一个[有序集合](http://beesbuzz.biz/static/blog/hashing/boggle-orderedset.cpp) :

```
$ g++ -O3 -Wall --std=c++11 boggle-orderedset.cpp
$ time ./a.out < board.txt > /dev/null

real    0m0.173s
user    0m0.160s
sys     0m0.010s 
```

使用一个[排序向量](http://beesbuzz.biz/static/blog/hashing/boggle-sortedvector.cpp) :

```
$ g++ -O3 -Wall --std=c++11 boggle-sortedvector.cpp
$ time ./a.out < board.txt > /dev/null

real    0m0.048s
user    0m0.039s
sys     0m0.007s 
```

使用一个[散列表](http://beesbuzz.biz/static/blog/hashing/boggle-hashtable.cpp) :

```
$ g++ -O3 -Wall --std=c++11 boggle-hashtable.cpp
$ time ./a.out < board.txt > /dev/null

real    0m44.075s
user    0m43.867s
sys     0m0.110s 
```

使用一个[手卷的 trie](http://beesbuzz.biz/static/blog/hashing/boggle-trie.cpp) :

```
$ g++ -O3 -Wall --std=c++11 boggle-trie.cpp
$ time ./a.out < board.txt > /dev/null

real    0m0.362s
user    0m0.320s
sys     0m0.038s 
```

在上述解决方案中，令人惊讶的是排序向量比有序集快得多；然而，不足为奇的是，这两种方法都比哈希表方法快很多数量级，而 trie 方法比有序集方法慢一些。当然，trie 实现可以更好一点(例如，实际的搜索算法可以跟踪它在 trie 中的位置，而不是每次都从根开始搜索)，但是编写的代码量也很重要:

```
$ wc -l *.cpp | sort -n
     131 boggle-orderedset.cpp
     132 boggle-sortedvector.cpp
     137 boggle-hashtable.cpp
     161 boggle-trie.cpp
     561 total 
```

因此，排序的 vector 实现起来稍微复杂一点(事实上，唯一的行计数差异是在字典加载后对`std::sort`的调用——如果你的字典一开始就是排序的话，这实际上是不必要的)，*然而*是目前所有这些中最快的。

好的，这个算法实际上并没有使用索引数据结构。但是导致实现它的思维过程与导致使用有序索引数据结构的思维过程是一致的；实际上，从更广泛的意义上来看，`vector` *是*一个指数。而且，每当我带着这个问题采访一个候选人，*没有一个*带着一个排序数组和一个二分搜索法！(我至少有几个人点了一个`set`。但是几乎每个人都用 trie——他们中的大多数人以前甚至从来没有听说过 trie，就像是当场发明的。这很酷，但是*还是*...)

此外，排序的`vector`方法只有在输入集是静态的情况下才真正有效。一旦你开始向它添加东西，那么，每一个添加都是一个潜在的 O(N)操作，它可能很快变得非常昂贵。例如，如果您正在编写一个软件负载平衡器，并且您的表跟踪您的后台服务器的负载水平，那么对它的每次更新都需要更改索引中的一行，并且如果您有很多行(比如，您正在进行某种规模的工作，在这种规模下，map-reduce 思想占了上风)，那么每次更新都会很快增加。

无论如何，只要有标准的 C++你就可以自己构建自己的索引，*或者*你可以使用 [Boost。MultiIndex](https://www.boost.org/doc/libs/1_62_0/libs/multi_index/doc/index.html) ，为您维护任意多个索引。很整洁。

### 巨蟒

总之，回到我最初的难题。我想研究将 Publ 的数据存储移到内存表中，并为必要的排序标准建立各种索引。最简单的方法是坚持使用数据库，尽管它的封装性很差(因为对象存储本质上是全局状态)。但是我还能做什么呢？

当我四处询问时，每个人都不停地给我指着 [`collections.OrderedDict`](https://docs.python.org/3/library/collections.html#collections.OrderedDict) ，这是一个保持其按键顺序的`dict`。但是“保持顺序”实际上意味着保持*插入*的顺序，而不是任何正在进行的排序顺序。对于维护索引来说，这实际上没有什么用处。(即使是这样，它也没有提供任何原语来执行范围查询或兄弟迭代或其他任何操作。)

然而， [`blist`包](http://stutzbachenterprises.com/blist/)是一个相当全面的 B 树实现，特别是它提供了一个 [`sorteddict`](http://stutzbachenterprises.com/blist/sorteddict.html) ，它确实保持了键的排序顺序。它还提供了一个`KeysView`，允许您`bisect_left`按键本身，以便遍历从特定点开始的项目。这当然不是最糟糕的事情。所以，在未来，如果我决定完全放弃 ORM，这可能是我会重点使用的。然而，这增加了一点复杂性，因为如果你知道你的排序键将要改变，你需要记住从 b 树中删除你的条目，然后在它被更新后重新读取它。(幸运的是，这与典型的 CRUD 机制并没有什么不同。)

当然，Publ 的用例需要大量的读操作，而不是大量的写操作，所以定期将索引重建为一个排序列表并以这种方式使用`bisect_left`也没什么大不了的。不过，这仍然是需要直接管理的事情。

也许当我决定去定义 public 的时候，我会写一个库来使索引表更容易管理。我肯定找不到任何真实存在的东西。(如果有人知道任何事情，请告诉我！)

(我的意思是，除非我能找到类似于 [Berkeley DB](https://en.wikipedia.org/wiki/Berkeley_DB) 的东西，并且实际上在 Python 3 中得到支持，并且与 MIT-license 兼容...)

(自我提醒: [lmdb](https://lmdb.readthedocs.io/en/release/) 是一个不错的选择)

### Java

如前所述，Java 同时提供了`TreeMap/TreeSet`和`HashMap/HashSet`。但是由于某些原因，人们总是被教导只使用`HashMap/HashSet`版本，这导致人们甚至不知道`TreeMap/TreeSet`甚至*的存在，甚至不考虑他们为什么想要使用它们。我觉得这非常令人困惑。*

### Lua 和 JavaScript

Lua 和 JavaScript 因为简单而非常受欢迎；他们都做了相同的简化假设，即*所有的*数据结构都是哈希表——包括基本数组。在许多情况下，这些被优化为基本数组，但也有许多情况下最终会分崩离析，这就是为什么在 Lua 中，尤其是在顺序很重要的情况下，你通常希望使用`ipairs`而不是`pairs`。

这样做的结果是，绝对没有索引的有序关联数组的概念。要么你的数组遍历是无序的(或者，在 JavaScript 中，是插入顺序的，因为 JS 的数组的行为或多或少类似于 Python 的`collections.OrderedDict`，除非它不是)，要么你从数组中取出所有的键，对其进行排序，然后对该数组进行迭代。这增加了更多的复杂性，并进一步降低了性能。

在 Lua 中，你不太可能写任何利用索引结构的东西(如果是，你可能用 C 或 C++实现这些东西，并用`ffi`调用它们)，但是 JavaScript 呢？它用于运行大量的*web 服务，虽然 node.js 提供了一个`ffi`绑定，但这通常被视为最后的手段。那么当人们需要在 node.js 服务中处理索引时，他们会怎么做呢？*

嗯，根据我的经验，他们似乎只是将其推迟到 ORM，或者耸耸肩，忍受糟糕的性能。

### 去吧

我已经很久没有接触过 Go 了，但是上次我接触的时候，golang 的正统观点是你不需要有序的索引。从一些粗略的网络搜索中，我发现这种情况在[看来仍然存在](https://nathanleclaire.com/blog/2014/04/27/a-surprising-feature-of-golang-that-colored-me-impressed/)。

幸运的是，有*的* [容器库](https://github.com/emirpasic/gods)纠正了这一点。看起来`TreeMap`甚至[现在也提供了`floor`和`ceiling`](https://github.com/emirpasic/gods/pull/82) ，它们可以用来实现范围查询。看起来它只是在最近才获得这个功能(也就是在撰写本文的一个月前)。

### C#

C#提供了一个`Dictionary`类。

你知道你能用现实生活中的字典做什么吗？你可以快速找到一个你想要的单词，然后看看它的前后有哪些单词。

你知道你不能用 C# `Dictionary`做什么吗？

好吧，所以 C#确实也提供了 [`OrderedDictionary`](https://docs.microsoft.com/en-us/dotnet/api/system.collections.specialized.ordereddictionary?view=netframework-4.7.2) ，但是据我所知，它没有提供任何迭代方法来获取下一个或上一个条目，或者任何类型的范围查询。不过，通过 LINQ 大概这些都是可能的。

### 哦我想我忘了写结论了

所以是的。对我来说，这是软件发展的方式，这很奇怪。所有的东西都是一大堆哈希表，没有人会去区别对待任何东西或者学习任何利用其他存储表示的算法。我觉得这很可悲。
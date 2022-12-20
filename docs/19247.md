# 代码 2018 的来临-第 2 天

> 原文：<https://dev.to/ballpointcarrot/advent-of-code-2018---day-2-1a3p>

欢迎回来！今天，我们将继续代码挑战的[来临。我通过我在](https://ladventofcode.com) [dev.to](https://dev.to) 社区中发现的令人敬畏的人们发现了这些挑战，他们已经接受了这些挑战，提供了讨论板等等。

### 第二天，问题第一

第二天的多空问题是这样的:

您会得到一个箱子 id 列表，它是一系列字母。为了验证箱子中的全部内容，您用“任何字母中的两个”来计算 id 的数量，并分别计算“任何字母中的三个”来对这些值进行校验和。使用问题陈述中的示例:

例如，如果您看到以下框 id:

1.  `abcdef`不包含恰好出现两三次的字母。
2.  `bababc`包含两个 a 和三个 b，所以两个都算。
3.  `abbcde`包含两个 b，但没有一个字母恰好出现三次。
4.  `abcccd`包含三个 c，但没有一个字母恰好出现两次。
5.  `aabcdd`包含两个 a 和两个 d，但只算一次。
6.  `abcdee`包含两个 e
7.  `ababab`包含三个 a 和三个 b，但只算一次。

因此，我们有四个 id 包含两个重复的字母(2、3、5 和 6)，三个 id 包含三个重复的字母(2、4、7)。如果你将这些值相乘，那么 12 就是你的校验和值。现在要求您计算测试输入的校验和值。

为了不被昨天的麻烦咬到，我想设置一个完整的项目(具有测试能力)。这比我预期的时间要长一些，因为我陷入了“我如何让它自动提供测试结果？”(我花了大约 2000 万)，以及“为什么这个测试没有正确执行？”(这花费了相当长的时间)。

一旦我最终让我的环境正常运行，我就着手写一些测试用例:

```
(ns  aoc.aoc2-test  (:require  [aoc.aoc2  :as  sut]  [clojure.test  :refer  [deftest  testing  is]]))  (deftest  aoc2-part1  (testing  "the example statement"  (let  [input  ["abcdef"  "bababc"  "abbcde"  "abcccd"  "aabcdd"  "abcdee"  "ababab"]]  (is  (=  12  (sut/checksum  input)))))  (testing  "a simple second example"  (let  [input  ["abab"  "abaa"  "aab"  "aac"  "aaa"  "abc"]]  (is  (=  6  (sut/checksum  input)))))) 
```

Enter fullscreen mode Exit fullscreen mode

这使用了问题中给出的示例场景，以及我自己编造的一个小例子。有了在文件保存时执行的测试，我就可以构建解决方案了。

同样，这个问题非常适合 Clojure 提供的 [`frequencies`](https://clojuredocs.org/clojure.core/frequencies) 函数——它会根据值出现的频率自动对字符串中的值进行分组。然而，因为我们只关心二和三，所以我们需要在这些结果中搜索这些值。 [`reduce`](https://clojuredocs.org/clojure.core/reduce) 函数(“映射/减少”名声)是我们想要的地方——它给了我们一个简单的方法来获取一个事物列表并从中生成一个标量值。在我们的例子中，我们希望将每个地图简化为三三两两的存在；理想情况下，{:a 3 :b 1 :c 2}会产生{:three 1:two 1 }。

构建一个复杂的`reduce`函数通常是痛苦的；因为 reduce 的内部函数就是那个(函数)，所以我在逻辑之外定义了它。因为 clojure 值通常是不可变的，所以我需要一种简单的方法来维护循环期间的状态- `transient`在这里有所帮助，因为我将 map 视为临时可变的，但是只在函数的边界内和临时绑定的生存期内。

下面是我对第 1 部分解决方案的总结:

```
(ns  aoc.aoc2)  (defn  reduce-twos-threes  "check the given frequency map n for twos or threes matches, and update
   the memo map to indicate if the string has a match. Used for a reducer."  [memo  n]  (let  [t-memo  (transient  memo)]  (if  (some  (fn  [[k  v]]  (=  v  2))  n)  (assoc!  t-memo  :twos  (inc  (:twos  t-memo))))  (if  (some  (fn  [[k  v]]  (=  v  3))  n)  (assoc!  t-memo  :threes  (inc  (:threes  t-memo))))  (persistent!  t-memo)))  (defn  checksum  [input]  (let  [sum-maps  (map  frequencies  input)  twos-threes  (reduce  reduce-twos-threes  {:twos  0  :threes  0}  sum-maps)]  (*  (:twos  twos-threes)  (:threes  twos-threes)))) 
```

Enter fullscreen mode Exit fullscreen mode

这使得`checksum`函数读起来非常简单:找到每个输入值的频率，找到二进制数和三进制数(存储在一个映射中)，然后最后将二进制数和三进制数相乘。

### 初二第二题(电动布加洛)

再次提炼问题陈述:

给定您的 id 列表，您要搜索的特定 id 只有一个字符不同；比如“text”和“tent”就符合这个要求，因为你只需要替换“x”和“n”这两个字符。问题中给出的例子是这样的:

*   `abcde`
*   `fghij`
*   `klmno`
*   `pqrst`
*   `fguij`
*   `axcye`
*   `wvxyz`

其中`fghij`和`fguij`是“相邻”的盒子。
你在比较匹配字符串的接近程度时遇到的任何问题**尖叫**T5】Levenshtein 距离。两个字符串的 Levenshtein 距离是将一个字符串转换为另一个字符串所需的最小变化数的度量——这通常包括插入、删除和替换，但我们的需求只需要替换(更好的说法是 Hamming 距离——两位独立的数学家研究字符串差异的同一领域。这是通过比较每个字符串中的值，并在遇到的每个差异上加 1 来实现的——这是一个非常简单的比较函数。

```
(defn  hamming  "Compute the Hamming Distance between two equal length
   strings."  [str1  str2]  (if-not  (=  (count  str1)  (count  str2))  (throw  (IllegalArgumentException.  "Strings must be equal size.")))  (loop  [s1  str1  s2  str2  score  0]  (if  (empty?  s1)  score  (recur  (rest  s1)  (rest  s2)  (+  score  (if  (=  (first  s1)  (first  s2))  0  1)))))) 
```

Enter fullscreen mode Exit fullscreen mode

这使用 Clojure 的尾部递归优化函数`loop`和`recur`来逐个比较字符串，并计算它们的汉明距离(我们在这里进行了检查，以确保它们长度相同，如果不相同就抛出错误)。

完成后，我设计了一种方法来筛选盒子列表，只找到汉明距离为 1 的盒子。这将返回一个包含两个条目的序列，每个条目对应一个匹配交换(这里可能有一个不遍历整个列表的优化):

```
(defn  find-christmas-boxes  [input]  (keep  identity  (map  (fn  [base-str]  (let  [target-box  (filter  #(=  1  (hamming  %  base-str))  input)]  (if-not  (empty?  target-box)  [base-str  (first  target-box)])))  input))) 
```

Enter fullscreen mode Exit fullscreen mode

这里有几件很酷的事情需要指出。首先，基本的`map`函数会返回大量的`nil` (Clojure 的`null`值)。为了摆脱这些，我们使用了`identity`函数(基本上是说“如果为真，则返回真”)；它实际上是一个清洁器`(not (nil? thing))`。对于非 Clojurists 的人来说，需要指出的另一件事是在`filter`调用中使用的速记函数声明:

```
;; These two are equivalent:  #(+  %  3)  (fn  [arg]  (+  arg  3)) 
```

Enter fullscreen mode Exit fullscreen mode

最后，AoC 谜题要求两个字符串之间的公共字符。这接近于上面的汉明距离函数，但是如果不匹配，我们只需要丢弃一个字符。这简化为以下内容:

```
(defn  remove-uncommon-letter  [s1  s2]  (apply  str  (keep-indexed  #(if  (=  (get  s2  %1)  %2)  %2)  s1)))  ;; To execute everything:  ;; (apply remove-uncommon-letter (first find-christmas-boxes input))  ;; where input is the raw input string from the project. 
```

Enter fullscreen mode Exit fullscreen mode

`apply`是这里最酷的一点——使用`apply`允许您使用向量(读取:数组)条目作为函数的参数:

```
(let  [numbers-to-add  [1  2  3  4  5  6]]  (apply  +  numbers-to-add))  =>  21 
```

Enter fullscreen mode Exit fullscreen mode

嘿-今天不要向任何人借，就在第三天上映之前。不过，我要留到明天。:Github 今天问题的要点是[就在这里](https://gist.github.com/ballpointcarrot/8bec8ebd2bfe8b3a0739eba824eeed1b)。
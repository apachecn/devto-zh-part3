# STL 算法教程:find 等。

> 原文：<https://dev.to/sandordargo/the-big-stl-algorithms-tutorial-find-et-al-271c>

在[大 STL 算法教程](https://dev.to/sandordargo/the-big-stl-algorithms-tutorial-introduction-2kph)的下一部分中，我们将发现`<algorithm>`头的不同功能，我们可以用它来查找容器中的项目。

也就是说，我们将检查以下函数:

*   `find`
*   `find_if`
*   `find_if_not`
*   `find_end`
*   `find_first_of`
*   `search`
*   `search_n`
*   `adjacent_find`

如果你感觉某些功能缺失了，你可能会想到`find_first_not_of`和类似的功能。它们不是`<algorithm>`头的一部分，但是它们是由`<string>`头提供的，因此，它们只对字符串进行操作。因此，它们不是本系列的一部分。

## `find`

我们今天的第一个函数是`find`，通过将容器和值传递给`find`方法，它可以用来查找容器中的元素。

就这么简单。它返回一个迭代器到第一个匹配我们正在寻找的值的元素。如果没有匹配的元素，迭代器指向容器的末尾(最后一个元素之后)。

```
#include <iostream> #include <algorithm> #include <vector>  
int main () {
  auto myvector = std::vector<int>{1, 2, 3, 4, 5};

  auto it = std::find(myvector.begin(), myvector.end(), 3);
  if (it != myvector.end()) {
    std::cout << "Element found in myvector: " << *it << '\n';
  } else {
    std::cout << "Element not found in myvector\n";
  }

  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

## `find_if`

`find`和`find_if`的区别在于，当 find 在容器中寻找一个值时，`find_if`接受一个一元谓词，并检查该谓词是否返回给定元素的`true`或`false`。

它将返回一个迭代器，指向谓词返回的第一个元素`true`。像往常一样，在没有匹配的情况下，迭代器将指向容器的最末端。

一元谓词可以是函数对象、指向函数的指针或 lambda 函数。您应该使用哪一种取决于您的使用案例。

```
#include <iostream> #include <algorithm> #include <vector>  
int main () {
  auto myvector{1, 2, 3, 4, 5};

  auto it = find_if(myvector.begin(), myvector.end(), {return number % 2 == 0;});
  if (it != myvector.end()) {
    std::cout << "Even element found in myvector: " << *it << '\n';
  } else {
    std::cout << "No even element found in myvector\n";
  }

  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

## `find_if_not`

和`find_if`差不多。但是它返回的不是给定集合中谓词的第一个匹配项，而是第一个不匹配项。

出于演示的目的，让我们以前面的例子为例，只添加一个*而不是* :
来修改它

```
#include <iostream> #include <algorithm> #include <vector>  
int main () {
  auto myvector{1, 2, 3, 4, 5};

  auto it = find_if_not(myvector.begin(), myvector.end(), {return number % 2 == 0});
  if (it != myvector.end()) {
    std::cout << "Even element found in myvector: " << *it << '\n';
  } else {
    std::cout << "No even element found in myvector\n";
  }

  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

前一个带有`find_if`的例子返回所有偶数，带有相同谓词的`find_if_not`将返回所有奇数。

## `find_end`

您可以使用`find_end`在容器中查找子序列。正如`end`后缀所暗示的，它将返回与上一次匹配相关的内容。这个东西将是匹配子序列(最后一个匹配子序列)的第一个元素的迭代器。你可以用两种不同的方式来使用它。在第一个示例中，通过值来比较项目。

```
#include <iostream> #include <algorithm> #include <vector>  
int main () {
  std::vector<int> numbers {1,2,3,4,5,1,2,3,4,5};

  std::vector<int> subsequence {1,2,3};

  auto it = std::find_end (numbers.begin(), numbers.end(), subsequence.begin(), subsequence.end());

  if (it!=numbers.end()) {
    std::cout << "needle1 last found at position " << (it-haystack.begin()) << '\n';
  }

  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

另一种可能是传入一个谓词作为比较函数。除了用那个代替一个按值比较的*，没有任何区别:* 

```
#include <iostream> #include <algorithm> #include <vector>  
int main () {
  std::vector<int> numbers {1,2,3,4,5,1,2,3,4,5};

  std::vector<int> subsequence {4,5,1};

  // using predicate comparison:
  auto it = std::find_end (numbers.begin(), numbers.end(), subsequence.begin(), subsequence.end(), {return i == j;});

  if (it!=numbers.end())
    std::cout << "subsequence last found at position " << (it-numbers.begin()) << '\n';

  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

通常，谓词可以是 lambda、函数对象或函数本身。

就我个人而言，我觉得奇怪的是，基于这个名字，除了搜索的方向之外，我认为`find_end`和`find`的行为是一样的。从`find`我期待第一场比赛，从`find_end`期待最后一场。相反，`find`寻找单个值，但是`find_end`试图匹配整个子序列。

虽然可以使用`find_end`生成一个长度为 1 的子序列来查找最后一个匹配的元素，但是不能使用`find`来搜索子序列。

## `find_first_of`

现在你可能希望我给出一个函数，从一个容器的开始寻找一个子序列。抱歉，但是如果你真的期待，我不得不让你失望。

`find_first_of`类似于`find_end`,它要么接受两对迭代器，要么接受两对迭代器和谓词。但是它和输入有什么关系呢？

它将向第一对迭代器和第一个元素返回一个迭代器，第一个元素匹配第二个传递的范围中的任何元素或谓词为真的第二个范围中的任何元素。

举下面这个例子:

```
#include <iostream> #include <algorithm> #include <vector>  
int main () {
  std::vector<int> numbers {1,2,3,4,5,1,2,3,4,5};

  std::vector<int> targets {4,5,2};

  // using predicate comparison:
  auto it = std::find_first_of (numbers.begin(), numbers.end(), targets.begin(), targets.end(), {return i == j;});

  if (it!=numbers.end())
    std::cout << "first match found at position " << (it-numbers.begin()) << '\n';

  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

输出将是

```
first match found at position 1 
```

Enter fullscreen mode Exit fullscreen mode

我们来看看为什么。`targets`的第一个元素是 4。它在`numbers`中第一次出现在位置 3(从零开始)。下一个元素 5 可以在位置 4 找到，最后一个元素 1 可以在位置 1 找到。这意味着最早可以在`numbers`容器中找到的是 1。

## `search`

开始了。还记得`find_end`在容器中寻找子序列的最后一个匹配吗？这是寻找第一个的对应对象。为了直观起见(小心，讽刺刚过去)，叫`search`！

就像前面提到的两个函数`find_end`和`find_first_of`一样，它可以接受两对迭代器定义的两个范围，也可以接受一个谓词。

这就是它的实际应用。

```
#include <iostream> #include <algorithm> #include <vector>  
int main () {
  std::vector<int> numbers {1,2,3,4,5,1,2,3,4,5};

  std::vector<int> subsequence {4,5,1};

  // using predicate comparison:
  auto it = std::search (numbers.begin(), numbers.end(), subsequence.begin(), subsequence.end(), {return i == j;});

  if (it!=numbers.end())
    std::cout << "subsequence first found at position " << (it-numbers.begin()) << '\n';

  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

## `search_n`

`search_n`也可以通过数值或借助谓语进行比较。它将寻找匹配值或值/谓词组合的`n`。

它将返回指向第一个匹配元素的迭代器。如果没有匹配，通常，返回的迭代器将指向最后一个元素之后。

```
#include <iostream> #include <algorithm> #include <vector>  
int main () {

  std::vector<int> myvector{10,20,30,30,20,10,10,20};

  auto it = std::search_n (myvector.begin(), myvector.end(), 2, 30);

  if (it!=myvector.end()) {
    std::cout << "two 30s found at position " << (it-myvector.begin()) << '\n';
  } else {
    std::cout << "match not found\n";
  }

  it = std::search_n (myvector.begin(), myvector.end(), 2, 10,  {return i == j;});

  if (it!=myvector.end()) {
    std::cout << "two 10s found at position " << int(it-myvector.begin()) << '\n';
  } else {
    std::cout << "match not found\n";
  }

  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

## `adjacent_find`

起初我不打算在这一集讨论`adjacent_find`，但后来我觉得它更属于这里，而不是其他话题。毕竟也是用来找元素的。

我们可能已经习惯了，这另一个 find 方法提供了两个重载签名，一个接受谓词，另一个不接受。除了这个可选参数，它只需要两个迭代器来定义它应该迭代的范围。

除非你这样写谓词，`adjacent_find`不会在容器中寻找一个特定的值。相反，它寻找任何两个匹配的相邻元素，或者任何两个相邻的元素满足谓词传递的条件。一个重要的注意事项是，你必须对 lambda 中的两个元素进行测试，你马上就会看到。

*像往常一样*，它返回一个迭代器到第一个匹配的元素，如果不匹配，返回到容器的末尾。

我们将在同一个容器上看到两个例子。在第一次调用中，我们将返回前两个相邻的匹配元素，在下一次调用中，我们将返回前两个相邻的偶数元素。

```
#include <iostream> #include <algorithm> #include <vector>  
int main () {

  std::vector<int> myvector{1, 0, 1, 1, 2, 3, 4, 6};

  auto it = std::adjacent_find (myvector.begin(), myvector.end());

  if (it!=myvector.end()) {
    std::cout << "two 1s found next to each other starting at position " << (it-myvector.begin()) << '\n';
  } else {
    std::cout << "no two equal elements found next to each other\n";
  }

  it = std::adjacent_find (myvector.begin(), myvector.end(), {return (i % 2 == 0) && (j % 2 == 0);});

  if (it!=myvector.end()) {
    std::cout << "two adjacent even numbers found starting at position " << int(it-myvector.begin()) << '\n';
  } else {
    std::cout << "no two neighbouring equal numbers found\n";
  }

  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

在本文中，我们学习了标准库中的函数，这些函数可以用来搜索容器中的一个或多个元素，而无需修改它们。

我们还可以看到 STL 的一些奇怪之处。就像`find`和`find_end`之间意想不到的差异，以及互补的`search`算法的不匹配名称。但是仔细想想也很奇怪，`find_end`、`search`和`search_n`把一个谓词作为可选参数，而`find`和`find_if`是不同的方法。我不知道背后的确切原因，但我认为这是历史性的，委员会不想改变现有的 API，也不想让额外接受的新方法过于复杂。

不考虑所有这些奇怪的东西，这些函数非常有用，应该成为每个 C++开发人员工具箱的一部分。

请继续关注，在下一集我们将讨论剩余的非修改序列操作。

*本文最初发表在[我的博客上。](http://sandordargo.com/blog/2019/05/15/stl-algorithm-tutorial-part-3-find)T3】*
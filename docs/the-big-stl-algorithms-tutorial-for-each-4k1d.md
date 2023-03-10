# 大 STL 算法教程:for_each

> 原文：<https://dev.to/sandordargo/the-big-stl-algorithms-tutorial-for-each-4k1d>

在大 STL 算法教程的下一部分，我将只解释一个函数。`for_each`算法。

它是做什么的？

`for_each`取一个范围和一个函数，应用于给定范围的每个元素。

[正如我们已经看到的](https://dev.toany-all-none)，一个范围(除非你正在使用范围库)意味着描述一个范围的开始和结束的两个迭代器。

该函数必须是一元的，这意味着它应该接受一个具有给定范围元素类型的参数。或者至少它应该是可转换的(例如，int 可以转换为 boolean)。

但是如何传递一个函数呢？在这种情况下，什么是功能？

它可以是函数本身，也可以是函数指针、函数对象或 lambda 函数。

让我们在下一个例子中拥有它们:

```
#include <iostream> #include <vector> #include <algorithm> #include <string>  
void printElement(const std::string& i_element) {
    std::cout << i_element << "\n";
}

class ElementPrinter {
public:

    void operator()(const std::string& i_element) const {
        std::cout << i_element << "\n";
    }
};

int main () {

  std::vector<std::string> strings {"The", "best", "revenge", "is", "not", "to", "be", "like", "your", "enemy"};

  std::for_each(strings.begin(), strings.end(), printElement);
  std::for_each(strings.begin(), strings.end(), ElementPrinter());
  std::for_each(strings.begin(), strings.end(),  {
        std::cout << i_element << "\n";
  });

  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

第一个 for_each 接受一个函数。

第二个函数接受一个仿函数的实例。

在第三种情况下，我们使用一个[λ表达式](http://sandordargo.com/blog/2018/12/19/c++-lambda-expressions)。

你应该用哪一个？这真的取决于你的用例。有时你需要一个一次性的逻辑，你不想把它存储在任何地方，你就用一个 lambda。在其他一些情况下，您可以使用前两者中的任何一个。更多细节参见[我对 lambda 函数的介绍](http://sandordargo.com/blog/2018/12/19/c++-lambda-expressions)。

如果你和函子一起去，要特别注意[五](https://en.cppreference.com/w/cpp/language/rule_of_three)法则。`for_each`需要能够移动和复制构造的函子。使用 lambda 就没有这样的问题——所需的一切都会生成。

你还应该注意，应用的函数返回什么并不重要，它将被省略。

你可能记得`for_each`是一个不可修改的序列操作。这是否意味着，我们不能修改序列中的内容？

让我们试一试！

```
#include <iostream> #include <vector> #include <algorithm>  
int main () {

  std::vector<int> numbers {1,2,3,4,5};

  std::for_each(numbers.begin(), numbers.end(),  {
        i = i * i;
  });

  for(auto num : numbers) {
    std::cout << num << "\n";
  }

  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

产量是多少？

```
1
4
9
16
25 
```

Enter fullscreen mode Exit fullscreen mode

这样我们就可以修改列表了！我们只需要通过引用将元素传递给函数。太好了！但是，那非修饰部分呢？

不能用 for_each 修改容器中元素的数量，不能添加或删除元素，但可以修改给定元素的值。无论如何，迭代一个在迭代期间长度被修改的序列是相当困难的，对吗？

## 替代品

我们已经看到了`for_each`是用来做什么的，我们已经看到了如何使用它，但是我们为什么要使用它呢？它的替代品是什么？

### 为带索引的循环

遍历容器的好方法。太不酷了，不是吗？

```
#include <iostream> #include <vector> #include <algorithm> #include <string>  
int main () {

  std::vector<std::string> strings {"The", "answer", "is", "within", "you"};

  for (size_t i=0; i<strings.size();++i) {
      std::cout << strings[i] << "\n";

  }

  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

冷静不是问题。但是在 for 循环中处理索引是乏味且不优雅的。但是如果你需要一个元素的索引，它是 goto 选项。除非你手头有 boost 并且[想用一些花哨的东西](https://www.fluentcpp.com/2018/10/26/how-to-access-the-index-of-the-current-element-in-a-modern-for-loop/)。

### 为带迭代器的循环

你可以使用迭代器遍历一个列表。你再也不用管索引了！

```
#include <iostream> #include <vector> #include <string>  
int main () {

  std::vector<std::string> strings {"Be", "tolerant", "with", "others", "and", "strict", "with", "yourself"};
  for (std::vector<std::string>::iterator it = strings.begin(); it != strings.end(); ++it) {
      std::cout << *it << "\n";

  }

  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

初始化迭代器简直太可怕了，不是吗？他们是长型的，就是这个原因。除此之外，迭代器的行为就像指针一样，因此如果你想得到值，你需要去引用它。

从 C++11 开始，我们可以通过使用`auto`关键字很容易地摆脱那个可怕的迭代器声明。

```
#include <iostream> #include <vector> #include <string>  
int main () {

  std::vector<std::string> strings {"Be", "tolerant", "with", "others", "and", "strict", "with", "yourself"};
  for (auto it = strings.begin(); it != strings.end(); ++it) {
      std::cout << *it << "\n";
  }

  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

你看，不再不方便了。但是我们有更好的。

### 基于范围进行循环

我们在声明时使用了`auto`关键字来省略迭代器的类型。但是我们可以将这个`auto`用于更好的目的。

```
#include <iostream> #include <vector> #include <string>  
int main () {

  std::vector<std::string> strings {"The", "best", "revenge", "is", "not", "to", "be", "like", "your", "enemy"};
  for (auto element: strings) {
      std::cout << element << "\n";
  }

  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

### 基于范围的`for`循环与`for_each`

主要问题是当我们不需要索引时，我们应该使用什么？基于范围的 for 循环还是`for_each`算法？

对我来说，基于范围的循环是*到*的解决方案。另一方面，它只能用于整个容器，而使用`for_each`则由您来指定想要迭代的范围。

如果您想抽象出循环必须对每个元素执行的逻辑，那么使用`for_each`可能更好。

```
#include <iostream> #include <vector> #include <algorithm> #include <string>  
void printElement(const std::string& i_element) {
    std::cout << i_element << "\n";
}

int main () {

  std::vector<std::string> strings {"The", "best", "revenge", "is", "not", "to", "be", "like", "your", "enemy"};

  std::for_each(strings.begin(), strings.end(), printElement);

  for(const auto& element: strings) {
    printElement(element);
  }

  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

哪个读起来更好？可能是第一个。但是将`for_each`与 lambda 一起使用是不值得的。

```
std::for_each(strings.begin(), strings.end(),  {
    std::cout << i_element << "\n";
}]); 
```

Enter fullscreen mode Exit fullscreen mode

这不太好读。所以选择主要是一个抽象的问题。[在这里](https://www.fluentcpp.com/2018/03/30/is-stdfor_each-obsolete/)你可以阅读关于这个话题的更深入的分析。

## 结论

今天，我们已经看到了`for_each`算法，这是前 C++11 次很酷的增强，当时我们没有基于范围的`for`循环。与它相比，它不是容器循环的默认解决方案，但是我们仍然有它合理的用法。不要忘记藏在这篇文章里的马可·奥勒留和塞内卡的智慧之珠。

请继续收听，下一集我们将讨论如何在容器中寻找物品。

*本文最初发表在[我的博客上。](http://sandordargo.com/blog/2019/04/03/stl-algorithm-tutorial-part-2-for_each)T3】*
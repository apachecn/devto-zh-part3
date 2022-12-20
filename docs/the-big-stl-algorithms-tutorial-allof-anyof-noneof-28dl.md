# 大 STL 算法教程:所有的，任何的，没有的

> 原文：<https://dev.to/sandordargo/the-big-stl-algorithms-tutorial-allof-anyof-noneof-28dl>

在大 STL 算法教程的第一部分中，我将从非修改序列操作的第一部分开始。

也就是说，在这篇文章中，你将会读到`all_of`、`any_of`和`none_of`函数。

它们的名字很直观，你可能会怀疑，它们都返回布尔值，并且在 STL 容器上操作。

除非你使用范围(这应该是另一篇文章的一部分)，否则你不会直接把它们传递给一个容器，而是传递给同一个容器上的两个迭代器。这些迭代器定义了函数的工作范围。

在两个迭代器之后，传入一个谓词。该谓词可以是函数指针或函数对象(包括[λs](https://dev.to/sandordargo/lambda-expressions-in-c-4pj4)),返回布尔值或至少是可转换为布尔值的东西。

意味着下一段代码甚至无法编译:

```
#include <iostream> #include <vector> #include <algorithm>  
int main()
{

  auto nums = {1,2,3,4,5,3};
  if (std::any_of(std::begin(nums), std::end(nums), 3) {
      std::cout << "there is a 3 in the list" << std::endl;
  } else {
      std::cout << "there is NOT ANY 3 in the list" << std::endl;
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

相反，让我们来看两个可行实现。第一个将使用一个函数对象:

```
#include <iostream> #include <vector> #include <algorithm>  

class IsEqualTo {
public:
    IsEqualTo(int num) : m_num(num) {}

    bool operator()(int i) {
        return i == m_num;
    }
private:
    int m_num;
};

int main()
{

auto nums = {1,2,3,4,5,3};
if (std::any_of(std::begin(nums), std::end(nums), IsEqualTo(3))) {
      std::cout << "there is a 3 in the list" << std::endl;
  } else {
      std::cout << "there is NOT ANY 3 in the list" << std::endl;
}

} 
```

Enter fullscreen mode Exit fullscreen mode

它有点长，但是由于名副其实的仿函数(function object ),它很容易阅读。

现在让我们来看看带有 lambda 表达式的版本:

```
#include <iostream> #include <vector> #include <algorithm>  
int main()
{

  auto nums = {1,2,3,4,5,3};
  if (std::any_of(std::begin(nums), std::end(nums), {return i == 3;})) {
      std::cout << "there is a 3 in the list" << std::endl;
  } else {
      std::cout << "there is NOT ANY 3 in the list" << std::endl;
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

这个版本更短，更密集，你只有这个 lambda 表达式:`{return i == 3;})`，而不是我们类`IsEqualTo`的整个定义。

用哪个比较好？这要看上下文。你可以在本文的[中阅读关于这个问题以及如何用 C++编写 lambdas 的一些细节。](https://dev.to/sandordargo/lambda-expressions-in-c-4pj4)

现在，让我们来谈一谈这三个函数的作用，但也许你已经很清楚了。

### `std::all_of`

对于所有的项，如果谓词被评估为真或者可以被转换为真，则`std::all_of`将返回真，否则返回假。

*“可转换”*部分意味着谓词不必返回布尔值。例如，它可以返回一个数字。但实际上任何可以被当作布尔值的东西。

### `std::any_of`

如果谓词被评估为真，或者对于任何项目的**可以被转换为真，则`std::any_of`将返回真，否则返回假。也就是说，如果谓词只对一百个元素中的一个为真，`std::any_of`将返回 true。**

 **### `std::none_of`

如果谓词被评估为真或者对于**无**项可被转换为真，则`std::none_of`将返回真，否则返回假。反过来说，`std::none_of`如果对**所有**项的谓词为假，则返回 true！如果至少有一个返回 true，函数本身将返回 false。

## 结论

第一部分到此为止。提供的三个函数- `all_of`、`any_of`和`none_of` -可以在代码中用 if 和 break 替换难看的循环，使代码更具表现力和可读性。尽可能不加节制地使用它们，敬请关注下一集！

*本文最初发表在[我的博客上。](http://sandordargo.com/blog/2019/02/20/stl-algorithm-tutorial-part-1-any-all-none)T3】***
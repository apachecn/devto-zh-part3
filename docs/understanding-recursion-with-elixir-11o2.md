# 用灵药理解递归

> 原文：<https://dev.to/sophiedebenedetto/understanding-recursion-with-elixir-11o2>

*这篇文章最初发表在[仙丹学校](https://elixirschool.com)的博客上。仙丹学校是一个开源的仙丹课程，我们正在寻找贡献者！你可以写一篇短的 TIL 博客文章，一篇更长的博客文章，添加课程，帮助翻译等等。查看我们未解决的问题或添加您自己的问题[点击](https://github.com/elixirschool/elixirschool/issues)* 。

对于我们这些不熟悉其应用的人来说，“递归”可能是一个可怕的词。在本帖中，我们将揭开递归概念的神秘面纱，并通过在 Elixir 中编写我们自己的递归函数来更深入地理解如何以及为什么使用它。

## 什么是递归？

简而言之，“递归”就是函数调用自己。首先，我们将看一个人为的例子。在这篇文章的后面，我们将构建一个更实用的递归函数。

下面我们定义了一个函数`RecursionPractice.hello_world/0`，它调用自己:

```
defmodule RecursionPractice do
  def hello_world do
    IO.puts("Hello, World!")
    hello_world()
  end
end 
```

如果你认为调用我们的`RecursionPractice.hello_world/0`函数会导致`"Hello, World!"`无限地输出到终端——那你就对了！`hello_world`函数做两件事:

1.  发出“你好，世界！”
2.  (再次)呼叫`hello_world/0`

当再次调用`hello_world/0`时，它将做两件事:

1.  发出“你好，世界！”
2.  (再次)呼叫`hello_world/0`

诸如此类。虽然“调用自身的函数”是递归的基本定义，但它的*而不是*我们想要如何实现递归函数。

任何递归函数都需要一种方法在一定条件下*停止自称*。这种情况通常被称为**基础情况**。让我们为我们的`RecursionPractice.hello_world/0`函数创建一个基本案例。我们会计算调用这个函数的次数，一旦达到 10 次就停止调用。

```
def hello_world(count \\ 0) do
  IO.puts("Hello, World!")
  if count < 10 do
    new_count = count + 1
    hello_world(new_count)
  end
end 
```

`if`条件控制我们的递归函数。*如果*计数小于 10，则计数加 1，并再次调用`hello_world/1`。否则，*不做任何事情*，即停止调用递归函数！

我们可以在[保护条款](https://elixirschool.com/en/lessons/basics/functions/#guards)的帮助下重构这段代码。我们将定义另一个版本的`RecursionPractice.hello_world/1`函数来处理我们的基本情况，而不是在我们的函数中编写一个`if`条件。这个版本将在计数大于或等于 10 时运行*。* 

```
defmodule RecursionPractice do
  def hello_world(count \\ 0)
  def hello_world(count) when count >= 10, do: nil

  def hello_world(count) do
    IO.puts("Hello, World!")
    new_count = count + 1
    hello_world(new_count)
  end
end 
```

注意，我们已经将默认的参数定义移到了函数头中。如果你用多个子句*和*定义一个缺省值，缺省值定义属于一个函数头。在[这个灵丹妙药学校课程](https://elixirschool.com/en/lessons/basics/functions/#default-arguments)中学习更多关于默认参数、函数头和函数子句的知识。

## 为什么有用？

每当我们需要在一定条件下重复一个动作时，递归就很有用。任何时候你想使用一个`while`或者`until`循环，你都可以用递归来实现你的解决方案。

你如何决定使用递归方法而不是像`while`循环这样的迭代方法？编写递归函数时使用递归会比循环方法产生更简单、更易读的代码。但是要小心，如果你写一个没有“基本情况”或停止点的递归函数，你会以堆栈溢出错误结束——你会永远调用函数*！*

 *## 用仙丹构建递归函数

现在我们对什么是递归及其工作原理有了更好的理解，让我们构建一个更实用的递归函数。

Elixir 的`List`模块为我们提供了许多方便的操作列表的函数，包括一个如下工作的`List.delete/2`函数:

给定一个列表和该列表中的一个元素，返回一个新列表，该列表不包含给定元素的第一次出现的。比如:

```
List.delete(["Apple", "Pear", "Grapefruit"], "Pear")
=> ["Apple", "Grapefruit"] 
```

但是，我们会看到，如果给定的列表包含不止一个`"Pear"`的出现，`List.delete/2`只会删除第一个*`"Pear"`* 

```
List.delete(["Apple", "Pear", "Grapefruit", "Pear"], "Pear")
["Apple", "Grapefruit", "Pear"] 
```

如果我们想从列表中删除某个特定元素的所有出现的*和*,该怎么办？`List`模块没有实现这样的功能。我们自己造吧！

我们期望的行为是这样的:

```
List.delete(["Apple", "Pear", "Grapefruit", "Pear"], "Pear")
["Apple", "Grapefruit"] 
```

在我们开始构建我们的函数之前，让我们看看如何使用递归和模式匹配来操作药剂列表。

### 在列表上使用递归

> Elixir 中的列表是有效的链表，这意味着它们在内部成对表示，包含列表的头部和尾部。- [十六进制文档](https://hexdocs.pm/elixir/List.html)

这意味着我们可以使用[模式匹配](https://elixirschool.com/en/lessons/basics/pattern-matching/)来获取第一个元素，或者列表的“头”:

```
iex> [head | tail] = [1,2,3]
iex> head
1
iex> tail
[2,3] 
```

使用这种模式匹配方法，我们可以对列表中的每个成员进行操作:

```
iex> list = [1,2,3,4]
[1, 2, 3, 4]
iex> [head | tail] = list
[1, 2, 3, 4]
iex> head
1
iex> tail
[2, 3, 4]
iex> [head | tail] = tail
[2, 3, 4]
iex> head
2
iex> tail
[3, 4]
iex> [head | tail] = tail
[3, 4]
iex> head
3
iex> tail
[4]
iex> [head | tail] = tail
[4]
iex> head
4
iex> tail
[] 
```

使用这种方法，让我们定义一个定制函数来递归遍历列表中的每个元素。
我们的函数将抓取列表的`head`并`puts`输出到终端。然后，我们将把`tail`分成它自己的*`head`和`tail`。我们将继续这样做，直到列表为空。* 

```
defmodule MyList do
  def my_each([head | tail]) do
    IO.puts(head)
    if tail != [] do
      my_each(tail)
    end
  end
end 
```

当`tail`为空时，即当列表中不再有元素时，我们的**基本情况**发生。我们可以利用 [Elixir 的模式匹配功能 arity](https://elixirschool.com/en/lessons/basics/functions/#functions-and-pattern-matching) 来稍微清理一下。

我们不会在递归函数中实现一个`if`条件，而是定义另一个版本的函数，当用一个空列表的参数调用`my_each`时，该函数将运行。所以，如果用一个非空的列表参数调用`my_each`，函数的第一个版本将会运行。它会抓取列表中的`head`并将`puts`取出。然后它将再次调用`my_each`*，并使用列表中的`tail`作为参数。如果尾部为空，将运行函数的第二个版本。在这种情况下，我们将*而不是*再次调用`my_each`。* 

```
defmodule MyList do
  def my_each([head | tail]) do
    IO.puts(head)
    my_each(tail)
  end

  def my_each([]), do: nil
end 
```

让我们来看看它的作用:

```
iex> MyList.my_each([1,2,3,4])
1
2
3
4 
```

现在我们已经掌握了如何使用递归和模式匹配来处理 Elixir 列表，让我们回到递归的“delete all”函数。

### 定义一个递归`delete_all/2`函数

#### 期望的行为

在我们开始编码之前，让我们规划出我们的函数需要如何表现。由于 Elixir 是一种函数式语言，我们*不会*改变原始列表。相反，我们将构建一个新的列表，包含原始列表中的所有元素，*减去*所有匹配我们想要排除的元素的元素。

我们的方法大概是这样的:

*   看看列表的最前面。如果该元素等于我们想要删除其出现的值，我们将*而不是*获取该元素以添加到新列表中。
*   如果元素*不*等于我们想要移除的值，我们将把它添加到新列表中。
*   无论哪种情况，我们都将抓住列表的尾部，重复上一步。
*   一旦尾部为空，即我们已经查看了列表中的每个元素，停止递归。

#### 造吧！

首先，我们将定义一个接受两个参数的`MyList.delete_all/2`函数:原始列表和我们想要删除其出现的元素。

```
defmodule MyList
  def delete_all(list, el) do
    # coming soon!
  end
end 
```

然而，我们需要访问一个新的空列表，我们将用我们*没有*删除的原始列表的元素填充它。因此，我们将定义一个版本的`delete_all`，它接受*三个*参数:原始列表、我们想要删除的元素 who occurrences 和新的空列表。

`MyList.delete_all/2`将调用`MyList.delete_all/3`函数。这使得用户不必用空列表的第三个参数调用`delete_all`，并允许我们提供一个漂亮整洁的 API。

```
defmodule MyList
  def delete_all(list, el) do
    delete_all(list, el, [])
  end

  def delete_all([head | list], el, new_list) do
  end
end 
```

`MyList.delete_all/3`函数的第一项工作是确定当前列表中的第一个元素，即列表的`head`，是否与我们想要移除的元素具有相同的值。

如果是这样，我们*不会*将其添加到我们的新列表中。相反，我们将使用当前列表的剩余部分`tail`再次调用`MyList.delete_all/3`，并原封不动地传入我们的`new_list`。我们可以用一个保护子句来完成这个任务:

```
def delete_all([head | tail], el, new_list) when head === el do
  delete_all(tail, el, new_list)
end 
```

然而，如果当前列表的头部是与我们想要移除的值相等的*而不是*，我们*想要在继续之前将它添加到我们的`new_list`中。*

我们将定义另一个`delete_all/3`函数，这次没有 guard 子句，以满足这个条件:

```
def delete_all([head | tail], el, new_list) do
  delete_all(tail, el, [head | new_list])
end 
```

我们将当前的`head`添加到新列表中，如下所示:

```
[ head | new_list ] 
```

我们再次调用`delete_all/3`，向它传递列表的剩余部分(`tail`)、要删除的元素和更新后的`new_list`。

什么时候应该停止递归？换句话说，会导致我们停止调用`delete_all/3`的基本情况是什么？当我们递归了原始列表中的所有元素，使得`tail`为空时，我们将*停止*调用`delete_all/3`并返回新列表。让我们定义一个最终的`delete_all/3`函数来匹配这个条件:

```
def delete_all([], el, new_list) do
  new_list
end 
```

这种方法的唯一问题是，它构建并返回一个新的列表，其中我们从原始列表中保留的所有元素都以相反的顺序填充。这是因为通过像这样构建我们的新列表:

```
[ head | new_list ] 
```

我们将我们想要保留的元素添加到新列表的前面，而不是后面。

一旦到达我们的空列表基本情况:
，我们可以通过在`new_list`上使用`Enum.reverse`来解决这个问题

```
def delete_all([], el, new_list) do
  Enum.reverse(new_list)
  |> Enum.reverse
end 
```

如果我们把它们放在一起，我们会有:

```
defmodule MyList do
  def delete_all(list, el) do
    delete_all(list, el, [])
  end

  def delete_all([head | tail], el, new_list) when head === el do
    delete_all(tail, el, new_list)
  end

  def delete_all([head | tail], el, new_list) do
    delete_all(tail, el, [head | new_list])
  end

  def delete_all([], el, new_list) do
    Enum.reverse(new_list)
  end  
end 
```

我们甚至可以更进一步，用 Elixir 的模式匹配函数 arity 的能力替换我们的 guard 子句。不使用 guard 子句在`head === el`时运行我们函数的某个版本，我们可以这样写函数:

```
def delete_all([el | tail], el, new_list) do
  delete_all(tail, el, new_list)
end 
```

现在我们应该可以调用我们的函数:

```
iex> MyList.delete_all(["Apple", "Pear", "Grapefruit", "Pear"], "Pear")
["Apple", "Grapefruit"] 
```

就是这样！****
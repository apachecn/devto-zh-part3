# 使用二分搜索法树搜索和排序

> 原文：<https://dev.to/blake/searching-sorting-w-binary-search-trees-3354>

### **简介**

二分搜索法树是一种非常有用的数据结构，它以特定的顺序保存可比较的值。

有了这种特殊的排序，它允许快速查找，并以一种简单的方式对树中的值进行排序。

关于用例，二分搜索法树是更抽象的数据结构的基础之一，例如[集合](https://en.wikipedia.org/wiki/Set_(abstract_data_type))和[地图](https://en.wikipedia.org/wiki/Associative_array)。

### **定义**

在我们讨论使用二分搜索法树(BST)进行搜索和排序之前，让我们确保我们在术语上是一致的。

一个**节点**保持

*   一个值。
*   对其他节点的引用。

**树**是一种数据结构，它具有

*   一个父节点。
*   零个、一个或多个子节点。
*   没有重复的节点。
*   没有引用以前定义的节点的节点(即没有循环)。

树被实现为一个根节点，由具有上述属性的子树的子节点组成。

*   父节点被视为该特定子树的根节点。
*   根节点是整个树的起始节点。

当你听到“二叉树”这个术语时，你可能会感到有点害怕！但是不用担心，这是一个很容易理解的概念。

一棵**二叉树**是一棵具有

*   任何父节点最多两个子节点(一左一右)。

现在让我们来定义一下这篇文章的内容——二分搜索法树。

二叉查找树是一种特殊类型的二叉树，其中

*   左侧子节点小于父节点。
*   右边的子节点大于父节点。

### **实现**

以下代码示例是用 c 语言编写的。

我们可以将一个节点表示为一个结构，该结构包含保存一个值(在本例中是一个整数)的字段，以及指向左右子节点的指针。

```
// Node Structure
typedef struct node {
    struct node *left; // Pointer pointing to left child node.
    struct node *right; // Pointer pointing to right child node.
    int val; // The value the node is holding.
} node_t; 
```

Enter fullscreen mode Exit fullscreen mode

### **排序**

有不同的方法可以遍历一棵树。有[前序](https://en.wikipedia.org/wiki/Tree_traversal#Pre-order_(NLR))、[中序](https://en.wikipedia.org/wiki/Tree_traversal#In-order_(LNR))、[无序](https://en.wikipedia.org/wiki/Tree_traversal#Out-order_(RNL))、[后序](https://en.wikipedia.org/wiki/Tree_traversal#Post-order_(LRN))等。因为这篇文章是专门关于二分搜索法树的，所以我们不打算详细讨论一般的树遍历。然而，**按序遍历**在 BSTs 中起着重要的作用。

有序遍历遵循模式`LEFT PARENT RIGHT`。
让我们以下面的 BST 为例:

[![BST: 5, 3, 7](img/572898d4062a290e4ffbf3f9e6af5bf0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7Ma9bbVO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/IT0I6cK.png)

如果您使用有序遍历来遍历 BST，您将从左边的节点(3)开始，返回到父节点(5)并以右边的节点(7)结束，结果是 3，5，7。

看看这个结果，有什么让你印象深刻的吗？如果你没有注意到，它返回了一个排序的结果！

有序遍历结果的这个性质不是巧合。

用任何 BST 试试；你总是会得到一个排序的结果。不服气？让我们用更复杂的 BST 再试一次。

[![BST: 50, 27, 99, 7, 42, 84](img/5d4ab86538a9430677cfeeb56b732762.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--D3KTI14B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/RUE2Ynr.png)

记住顺序遍历是这样的:左，父，右。

所以从 50 开始，向左走到 27。27 有个左孩子(7)，接下来去那里。7 没有左孩子，所以这是我们的第一个条目。然后向上回到 7 的父条目(27)，这是我们的第二个条目，然后转到 27 的右边的子条目(42)，这是我们的第三个条目。

到目前为止，我们的结果是:
`7, 27, 42`

现在，我们完成了以 27 为根的子树。50 是 27 的父，所以这是我们的第四个条目。然后我们去 50 的右边的孩子，99。但是，99 有一个左子(84)，所以我们改为去那里。84 现在是我们的第五个条目。然后我们转到 84 的父(99)，这是我们的第六个条目。然后我们去找 99 的右子，这个右子是不存在的——这样就完事了！

我们从有序遍历得到的结果是:
`7, 27, 42, 50, 84, 99`

你看看那个，已经整理好了！

下面是有序遍历的 C 实现:

```
void printInOrder(node_t *parent) {
    // Safety Check.
    if (parent == NULL) {
        return;
    }

    // Print Left Subtree.
    printInOrder(parent->left);

    // Print Parent.
    printf("%d, ", parent->val);

    // Print Right Subtree.
    printInOrder(parent->right);
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个递归实现中，我们检查所提供的节点是否是`NULL`。这是我们的基本情况。

然后我们递归调用左边子节点上的有序函数。这将继续调用，直到不再有留守儿童。然后，它将打印当前(左)节点值。之后，它将再次递归调用我们的右子节点的有序函数。

我们最终得到的是我们提供的 BST 的值，这些值是经过排序打印出来的。

### **搜索**

如果我们想在 BST 中搜索一个提供的值，我们该怎么做呢？

搜索利用了 BSTs 的三个特性:

*   没有重复的。
*   值可以相互比较。
*   左边的值小于父值，右边的值大于父值。

我们从根节点开始搜索。如果提供的值与根节点的值匹配，我们可以简单地返回，因为已经找到了该值。

但是如果值不匹配，我们有两个选择:向左遍历或向右遍历。

我们应该做哪一个？

如果提供的值小于当前节点，我们就向左搜索。否则，我们搜索正确。左子元素具有较小的值，而右子元素具有较大的值，这一特性唯一地定义了二叉查找树，所以要好好利用它！

在 C 语言中，这个`contains`函数是这样实现的:

```
bool contains(node_t *parent, const int *value) {
    // Safety Check.
    if (parent == NULL) {
        return false;
    }

    // Found match!
    if (parent->val == *value) {
        return true;
    }

    // Check left subtree.
    if (*value < parent->val) {
        return contains(parent->left, value);
    }

    // Check right subtree.
    return contains(parent->right, value);
} 
```

Enter fullscreen mode Exit fullscreen mode

这种数据结构不仅有利于提高搜索效率，而且易于实现。搜索功能可以递归实现。

正如您在实现中看到的，如果我们寻找的值小于子树的父值，我们就忽略右边的子树。同样，如果我们搜索的值大于子树的父值，我们就忽略左边的子树。

因为我们要么向左搜索，要么向右搜索，直到找到提供的值，所以我们平均只搜索 BST 中一半的节点。这使得在 BST `O(log n)`中搜索的平均效率。最坏的情况是需要搜索所有节点的`O(n)`(这就是[自平衡树](https://en.wikipedia.org/wiki/Self-balancing_binary_search_tree)有用的地方)。

### **完整试玩**

如果您想试用或查看包含一些测试的完整 C 实现演示，[请单击此处](https://gist.github.com/heyimblake/122d9e519b18266e4f24cbe5e550dcbe)！

### **收拾东西**

在搜索和排序时，二分搜索法树是非常有用的数据结构。通过利用有序遍历和这种特殊树的属性，您不仅可以高效地实现搜索和排序功能，而且还可以轻松地实现这两种功能。

感谢阅读！
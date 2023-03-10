# 算法案例研究:如何找到字谜！

> 原文：<https://dev.to/tttaaannnggg/anagrams-3030>

通过做这个问题，我学到了一些关于排列和重复数据删除/修剪树的有趣的东西，所以我想我应该为你们写一点东西！

挑战本身如下:

> 给定一个字符串，返回一个包含其所有可能的变位词的数组
> 例如:`anagrams("abc")`返回`["abc", "acb", "bac", "cab", "cba"]`
> 奖励:不使用`uniq`从数组中删除重复项

让我们了解一下我们问题的范围，好吗？

我们这里有一个[排列](https://en.wikipedia.org/wiki/Permutation)的问题。我们已经得到了有限数量的项目(在这种情况下，字符)，并希望找出每一个可能的顺序，我们可以安排他们。如果我们没有重复字符，这将产生`n!` (n 阶乘)结果，其中`n`是我们要排列的项目数。`"abc"`是一个 3 字符长的字符串，所以结果数组应该包含 6 项(`3*2*1`)。我们可以用这个来帮助检查我们的字谜生成器是否工作。

那么，我们如何开始排列我们的字符串呢？

我选择把它想象成一棵树。

[![](img/002210f93b4959905d4b13d7bac945c7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--t79FX9GF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wo0bg29wx8u7omznzs61.png)

这可能看起来有点晦涩，但逻辑是基于我手动生成组合的方式。

如果我们从字符串`"abc"`开始，我们可以先选择`"a"`、`"b"`或`"c"`。

如果我们选择`"a"`，我们可以在剩余的`"b"`和`"c"`之间进行选择。如果我们选择`"b"`，我们还有`"c"`，或者如果我们选择`"c"`，我们还有`"b"`。按照我画出的树的方式，你只需简单地跟随你的选择，就能得到最终的排列。`"a"->"b"->"c"`，给你`"abc"`或者`"a"->"c"->"b"`，给你`"acb"`。

## 穿越

因此，我们可以使用类似于遍历一个 [Trie](https://en.wikipedia.org/wiki/Trie) 的策略，以确保我们命中每一个可能的排列。我们将使用递归 DFS 遍历来实现这一点。

我们将沿着树向下遍历，直到我们碰到一片叶子(没有子节点的节点)，在这一点上，我们将知道我们已经完成了一个排列的创建，这是基于我们为到达该点所做的选择。

这不足以完成我们的功能，但它是很多核心功能，所以我们将从遍历开始。

```
function traverse(string){
  for (let i = 0; i < string.length; i++){
    traverse(string.slice(0,i) + string.slice(i+1));
  }
} 
```

本质上，如果我们首先选择`"a"`，我们希望用字符串`"bc"`调用`traverse`。为了做到这一点，我们使用本机的`slice`方法来复制和连接除了当前索引处的字符之外的所有内容，然后我们将递归调用我们的`traverse`。

仅此还不够。我们还需要两样东西:

*   来记录我们正在创造的排列
*   来维护我们已经完成的所有排列的数组

让我们来追踪我们的排列。我们将简单地添加第二个参数，该参数将以空字符串开始。当我们选择每个字符时，我们会将该字符连接到字符串的末尾，以便进行下一步遍历。

```
function traverse(string, perm = ''){
  for (let i = 0; i < string.length; i++){
    traverse(string.slice(0,i) + string.slice(i+1), perm + string[i]);
  }
} 
```

维护和返回输出数组会带来一些子任务。我们需要

*   创建并返回一个输出数组
*   当我们到达一个叶节点时，推进到我们的输出数组

我们将处理输出数组的创建和返回。我们的输出数组不会是递归的一部分，所以我们将把它放在一个外部函数中，该函数将包装我们的递归`traverse`函数。

```
function anagram(string){
  const output = [];
  function traverse(string, perm = ''){
    for (let i = 0; i < string.length; i++){
      traverse(string.slice(0,i) + string.slice(i+1), perm + string[i]);
    }
  }
  return output
} 
```

现在，当遍历到达一个叶节点时，我们必须通过推送来维护数组。假设我们在每一步都减少了一个字符，我们最终会到达一个点，在这个点上`string`中不再有任何字符。在这一点上，我们将希望推进到我们的输出数组。

```
function anagram(string){
  const output = [];
  function traverse(string, perm = ''){
    if (!string) output.push(perm)
    for (let i = 0; i < string.length; i++){
      traverse(string.slice(0,i) + string.slice(i+1), perm + string[i]);
    }
  }
  return output
} 
```

现在，我们有了一个数组，我们有了一种从字符串隐式生成和遍历树的方法，沿途维护每个可能的排列，还有一个数组来存储和返回它。我们只需要调用我们的遍历。

```
function anagram(string){
  const output = [];
  function traverse(string, perm = ''){
    if (!string) output.push(perm)
    for (let i = 0; i < string.length; i++){
      traverse(string.slice(0,i) + string.slice(i+1), perm + string[i]);
    }
  }
  traverse(string)
  return output
} 
```

因此，这对于没有重复字符的字符串非常有效，比如`"abc"`。但是这是关于复制品的什么？让我们看一看。

[![](img/1574fa1407b1b350cd3d8a1b4b0db0c5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qliyY6M6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yb82nppqkidvoj85fprf.png)

如果我们遍历到`"a"`，两个子树是一样的！我们得到`"abb"`和`"abb"`。如果我们遍历到`"b"`，我们得到`"bab"`和`"bba"`，如果我们遍历到最后一个字符，另一个`"b"`，结果是一样的。

我们计算唯一项排列数的公式是`n!`。如果我们想要计算包含重复项目的项目集合的排列，我们只需取重复项目的每个子集的阶乘，将它们彼此相乘，然后除以`n!`。它看起来像这样:`n!/(a!*b!*c!....)`。在我们的例子中，我们有 3 个字符，2 个字符是重复的。所以，期望的组合数是`3!/2!`，也就是`(3*2*1)/(2*1)`，或者`6/2`，剩下`3`。`"abb", "bab", "bba"`。

那么，我们如何消除重复呢？一种方法是将我们所有的排列放入一个对象中，并使用`Object.keys()`返回唯一的键。这是可行的，但这是额外的工作，占用了额外的时间和空间。

我们可以做得更好。

## 做得更好

在这一点上，我们能做的最好的事情是，当我们要从我们的字符串中生成一个重复的子树时，注意避免遍历这条路径。

我们如何做到这一点？答案在描述里；我们避免重复。再来看`"abb"`。这些`"b"`一模一样，不是吗？选择一个`"b"`和选择另一个是一样的，所以如果我们已经从一个生成了子树，我们可以完全忽略另一个。

让我们用一个`Set`来记录我们已经看过的角色。如果我们以前见过它，我们可以跳过遍历它，因为我们已经做了工作。

```
function anagram(string){
  const output = [];
  function traverse(string, perm = ''){
    const seen = new Set();
    if (!string) output.push(perm)
    for (let i = 0; i < string.length; i++){
      if (!seen.has(string[i])){
        seen.add(string[i]);
        traverse(string.slice(0,i) + string.slice(i+1), perm + string[i]);
      }
    }
  }
  traverse(string)
  return output
} 
```

这并不太复杂，但是关于这个解决方案有几个细节值得一提。我们非常有意地把我们的`seen`放在`traverse`里面。顶层节点的`"b"`与一两层深度的`"b"`不同，因此为每个堆栈帧保持不同的集合是至关重要的。

另一个是，在最坏的情况下(没有重复)，我们的空间复杂度几乎增加了一倍，但是一旦我们有了两个相同的字符，我们的工作就减少了将近一半，同时也节省了用递归打开新堆栈帧的空间。我们可以通过对字符串进行初步扫描来优化这一点，以便在将字符串发送到一个遍历之前检查是否有重复，该遍历可以维护或不维护重复数据删除`set`。

`slice`也不是一个理想的操作，我们可以简单地将`i`传递给下一次对`traverse`的调用，并跳过那个字符。

感谢您的关注！
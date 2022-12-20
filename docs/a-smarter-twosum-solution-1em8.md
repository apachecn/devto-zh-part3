# 更智能的二和解决方案

> 原文：<https://dev.to/tttaaannnggg/a-smarter-twosum-solution-1em8>

你们都很熟悉二和问题，对吧？它的要点是这样的:你得到一个数字数组，和一个目标和(一个数字)，你想看看数组中的任何一对数字加起来是否会达到给定的目标和。

一个显而易见的解决方案是，您可以简单地遍历数组并添加所有可能的数字组合，检查它们是否等于目标值，如果它们等于目标值，则返回 true，如果您遍历了数组但没有找到匹配项，则返回 false。大概是这样的:

```
function twoSum(arr, target){
    for (let i = 0; i < arr.length; i++){
        for(let j=0; j < arr.length; j++){
            if (i !== j){
                if(arr[i] + arr[j] === target){
                    return true;
                }
            }
        }
    }
    return false
} 
```

Enter fullscreen mode Exit fullscreen mode

显然那是一团糟。它大约有 5 层巢。不可读，效率也低。我们可以做得更好。

现在，我们给 arr[i]加上每个不是它本身的元素。这是一个问题，因为这意味着我们实际上在重复我们的工作。假设我们有一个由[1，2，3，4，5]组成的集合。如果我们已经检查了 1+2、1+3、1+4 和 1+5，我们不需要检查 2+1、3+1、4+1 或 5+1！

```
function twoSum(arr, target){
    for (let i = 0 ; i < arr.length; i++){
        for(let j = i + 1 ; j < arr.length; j++){
            if (arr[i] + arr[j] === target) return true;
        }
    }
    return false;
} 
```

Enter fullscreen mode Exit fullscreen mode

你会注意到将`j`设置为`i+1`允许我避免第二个条件。现在我们只做我们实际需要做的数字组合，这绝对是一个很大的改进！然而，我们仍然在寻找嵌套的“for”循环，当我们的数据增长时，它真的会扰乱我们算法的速度。就时间复杂性而言，这个特定的解决方案在 O(n^2 时间内解决。

让我们继续介绍一个新的想法。

我们在检查`arr[i] + arr[j] = target`是不是？嗯，我们也可以把它框定为检查`arr[j] = target - arr[i]`。虽然这对我们的帮助可能不太明显，但我们至少可以看看下一个解决方案:

```
function twoSum(arr, target) {
  const numbers = {};
  for (let i = 0; i < arr.length; i += 1) {
    if (numbers[arr[i]] === true) return true;
    const complement = target - arr[i];
    numbers[complement] = true;
  }
  return false;
} 
```

Enter fullscreen mode Exit fullscreen mode

变化很大，是吧？

最酷的是，我们可以在一次运行中完成整个过程，这意味着现在是 O(n)时间。让我们看看这是怎么可能的。首先，我们创建一个叫做“数字”的对象。这个对象是用来存储我们进行中的补语的。这些在我们的调中表现出来。

回到我们之前的观点，`item A = target - itemB`。因此，如果我们使用一个键`target - itemB`，我们可以简单地访问我们的对象，看看它是否有这个特殊的键。

让我们以一个目标为`5`的数组`[3, 7, 12, 1, 2, 5]`为例。我们的第一步是去看看`3`。这是我们找到的第一个号码。5(我们的目标)- 3(我们的数字)= 2(我们的补数)，这意味着如果我们在数组中看到 2，我们知道我们有一个加起来达到目标的和。

我们的对象看起来是这样的:`{'2': true}`。

我们来看下一个数字，`7`。首先，我们在我们的`numbers`对象上检查它的值。`numbers[7]`解析为 undefined，所以我们知道它不是我们要寻找的总和。因此，我们找到补码，`5-7`，并将其存储在我们的对象上。

现在，`numbers`对象看起来是这样的:`{'2': true, '-2': true}`。

我们继续，检查数组中的项是否是对象上的键，如果不是，就在对象上存储它的补码。最终我们会到达 2。当我们这样做时，我们的对象将看起来像这样:`{'2': true, '-2': true, '-7': true, '4': true}`。所以我们检查`numbers[2]`，得到`true`，打破循环，告诉我们我们的数组确实包括两个加到`5`的数。

如果我们从来没有达到总和，我们到达数组的末尾，循环结束，我们简单地返回'假'。

轻松点。

**附录**:如果`numbers`物体在你看来有点怪异，那是因为它确实如此。ES6 引入了`Set`，它有一个`add`和一个`has`方法，在`O(1)`时间内运行。它可以替代我在这里使用的语法！
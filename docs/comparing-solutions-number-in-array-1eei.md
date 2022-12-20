# 比较解决方案:数组中的数字

> 原文：<https://dev.to/lindsayspencer/comparing-solutions-number-in-array-1eei>

在我的 bootcamp 体验开始时，我遇到的第一个即兴代码挑战之一是创建一个接受数组和数字的函数，如果数组包含数字，则返回 true。我还没有练习或遇到这个特殊的问题，所以我的大脑开始进入恐慌模式。但是通过相信我对 JavaScript 的理解，我实际上当场就想出了一个可行的解决方案。

随着我对 JS 有了更多的经验，我喜欢定期重新审视这个问题，看看我是否能提出一个替代的解决方案。在这一点上，我已经想出了一些方法，并想比较每一种方法！

我确保我的所有解决方案在以下情况下都有效:

1.  给定的数组不包含给定的数字
2.  给定的数组包含给定数字的单个实例
3.  给定的数组包含给定数字的多个实例

### 原解*:

```
function checkByFilter(arr, num){
  let filteredArr = arr.filter(x => x == num);
  if(filteredArr[0] == num){
    return true;
  } else {
    return false;
  }
} 
```

我创建了一个新变量`filteredArr`，并将其设置为原始数组的过滤版本。我使用了`filter()`方法来查看数组`arr`中的每个值`x`，并且只返回等于`num`的值。这意味着`filteredArr`要么是一个空数组，要么是一个包含一个或多个`num`实例的数组。
有了过滤后的数组，我可以使用条件来测试它是否存在`num`。如果数组中的第一个值`filteredArr[0]`等于`num`，返回值为`true`。如果等于其他任何值(如果数组为空则为`undefined`，返回值为`false`。

* *评论者提醒我，条件实际上是不必要的，因为`===`已经返回了一个布尔值(谢谢大家！).下面是更新后的代码:*

```
function checkByFilter(arr, num){
  let filteredArr = arr.filter(x => x == num);
  return filteredArr[0] === num;
} 
```

### 备选方案 1:

```
function checkByLoop(arr, num){
  for(var x = 0; x < arr.length; x++){
    if(arr[x] === num){
      return true;
    }
  }
  return false;
} 
```

我没有过滤数组，而是使用了一个`for`循环来遍历每个元素，并将其与目标值`num`进行比较。如果不满足允许它返回`true`的条件，那么该函数将返回`false`。

### 备选方案 2*:

```
function checkByFind(arr, num){
  let foundArr = arr.find(x => x == num);
    if(foundArr == num){
      return true;
    } else {
      return false;
    }
} 
```

`find()`方法类似于`filter()`方法，但是它只返回一个值，而`filter()`会返回所有在给定数组中出现多次的`num`的实例。这个解决方案允许我比较一个单值变量和`num`，而不是一个数组。

* *与第一种解决方案一样，条件句实际上并不必要。更新的解决方案:*

```
function checkByFind(arr, num){
  let foundArr = arr.find(x => x == num);
  return foundArr === num;
} 
```

### 备选方案 3:

```
function checkByIndex(arr, num){
    if(arr.indexOf(num) < 0){
      return false;
    } else {
      return true;
    }
} 
```

这是最有效的解决方案，因为不需要将任何值保存到新变量中，也不需要循环。`indexOf()`方法检查目标值`num`在给定数组`arr`中的索引位置。如果它不在数组中，它将返回-1 的索引。因此，如果索引小于 0，条件用于返回`false`，如果索引等于或大于 0，则返回`true`。

对我来说，这篇文章代表了我早期 JavaScript 技能的演变。我喜欢为功能性问题寻找替代解决方案的挑战。将来我可能会在其他常见的代码挑战中做更多这样的事情。

感谢您的阅读！
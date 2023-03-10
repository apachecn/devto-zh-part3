# 分解常见算法问题的 JavaScript 解决方案(第 1 部分)

> 原文：<https://dev.to/emmabostian/breaking-down-javascript-solutions-to-common-algorithmic-questions-part-1-1228>

你有没有在技术面试上绞尽脑汁开发算法解决方案的经历？在这个简短的教程中，我们将分解三个顶级算法编码问题，从蛮力方法(一步一步，但不一定是高性能的)开始，过渡到一个更优化、更优雅的解决方案。

如果你找到了另一个解决方案，请在评论中发表！

# 反转一串

**任务**
给定一个字符串，返回它的反序。

**解决方案 1**
我们可以使用`string.substring()`方法获取`str`参数的每个字母，并将其附加到一个新字符串中。[子串方法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/substring)带有一个必需参数和一个可选参数。

第一个参数是希望子字符串开始的索引。这是*包含*，意思是如果你写`myString.substring(1)`，输出将包括第一个字符。

第二个(可选)参数是结束索引。该参数是*而不是*包含在内。这意味着您的子串将包含所有字符*，直到*这个索引加上该索引右边的所有剩余字符。

我们可以在强力方法中使用的另一个字符串方法是`string.charAt()`方法。 [charAt 方法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/charAt)接受一个参数:想要返回的字符的索引。

让我们编写两个强力算法来返回字符串的倒数。

```
// Method 1: Substring
function reverseString(str) {
    let reversedString = '';

   /* Loop through this process for each character in the str parameter
    In order to get the reverse, we'll initialize i to str.length
    Add each character, starting from the end, to the new string.
   */
    for (let i = str.length; i > 0; i--) {
        reversedString += str.substring(i, i-1);
    }
    return reversedString;
}

// Method 2: CharAt
function reverseString(str) {
    let reversedString = '';

   /* Loop through this process for each character in the str parameter
    In order to get the reverse, we'll initialize i to str.length - 1
    while i is greater than or equal to 0.
    Add each character, starting from the end, to the new string.

   */
    for (let i = str.length-1; i >= 0; i--) {
        reversedString += str.charAt(i);

    }
    return reversedString;
} 
```

**解决方案 2**
解决这个问题最快的内联方式之一是将字符串中的每个字符拆分成一个数组索引，对数组内的项进行反转，将每个索引中的项变成一个串接的字符串。

我们将使用以下方法:

*   `string.split()` [方法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/split)将每个字符拆分成一个数组索引。
*   `array.reverse()` [方法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reverse)将一个数组反转到位。
*   `array.join()` [方法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/join)将所有数组值连接成一个字符串。

您可以将这三个功能链接在一起，形成一个优雅的内联解决方案。

```
function reverseString(str) {
  return str.split('').reverse().join('');
} 
```

* * *

# 最长的字

**任务**
返回给定句子中最长单词的长度。

**解决方案 1**
第一次尝试，可以使用`string.split(' ')`方法将一个句子内的单个单词分解成数组索引。*这个教程不会考虑标点符号，但是你可以用一个正则表达式*来解决这个问题。

接下来，我们可以遍历数组的每个索引，并计算每个单词中的字母数。我们可以跟踪变量中最长的单词值。如果当前单词值大于当前保存的最大单词值，则替换它！然后，只返回包含最长单词的变量。

您可以使用 for-loop 或`array.forEach()`方法遍历数组。我更喜欢后者，但我已经把两者都包括在下面了。

```
// Solution with for-loop
function findLongestWordLength(str) {
  let maxVal = 0;

  const wordArr = str.split('  ');

  for(let i = 0; i < wordArr.length; i++) {
      let word = wordArr[i];
      if (word.length > maxVal) {
          maxVal = word.length;
      }
  }
  return maxVal;
}

// Solution with array.forEach method
function findLongestWordLength(str) {
  let maxVal = 0;

  const wordArr = str.split('  ');

  wordArr.forEach(word => {
      if (word.length > maxVal) {
          maxVal = word.length;
      }
  });
  return maxVal;
} 
```

**解决方案 2**
为了优化这个解决方案，我们仍然会使用`string.split()`方法将每个单词分隔成一个数组索引。

接下来，我们将使用`array.map()` [方法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)对每个数组索引中的值执行某种类型的表达式。这将返回一个全新的数组，所以我们将它保存到一个新的变量中。

对于数组中的每一项，返回字符串的长度并将其保存在一个名为`arrOfLengths`的新数组中。

最后，我们可以使用带有[扩展操作符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)的`Math.max(...spreadOperator)` [方法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/max)，以便返回句子中最长字符串的整数值。

```
function findLongestWordLength(str) {
  const arrOfWords = str.split('  ');
  const arrOfLengths = arrOfWords.map(item => item.length);

  return Math.max(...arrOfLengths);
} 
```

* * *

# 数组的最大子数组值

**任务**
从每个提供的子数组中返回一个由最大数组成的数组。为简单起见，所提供的数组将正好包含 4 个子数组。

```
[1,2,3,4]
[5,18,0,12]
[3,5,12,5]
[28,9,2,34]

Should return => [4,18,12,34] 
```

**解决方案 1**
对于第一次遍历，我们可以从一个嵌套的 for-loop 开始。

对于外部数组中的每一项，遍历它的子数组并找到最大值，然后将其推入一个新数组。

```
// For loop
function largestOfFour(arr) {
  let arrayOfMaxValues = [];
  for (let i = 0; i < arr.length; i++) {
      let subArr = arr[i];
      let maxSubArrVal = 0;
      for (let j = 0; j < subArr.length; j++) {
          let currentValue = subArr[j];
          if (currentValue > maxSubArrVal) {
            maxSubArrVal = currentValue;
          }
      }
      arrayOfMaxValues.push(maxSubArrVal);
  }
  return  arrayOfMaxValues;
}

// For each method
function largestOfFour(arr) {
  let arrayOfMaxValues = [];
  arr.forEach(subArr => {
     let maxSubArrVal = 0;
     subArr.forEach(item => {
        if (item > maxSubArrVal) {
            maxSubArrVal = item;
        }
     });
     arrayOfMaxValues.push(maxSubArrVal);
  });
  return  arrayOfMaxValues;
} 
```

**解决方案 2**
我们可以使用`Math.max(...spreadOperator)`方法和`array.map()`方法来循环外部数组中的每一项，返回子数组中的最大值，并直接返回新创建的数组。

```
function largestOfFour(arr) {
  return arr.map(subArr => Math.max(...subArr));
} 
```

* * *

我计划把这个变成一系列的帖子，所以如果你喜欢它，一定要跟着我更新！
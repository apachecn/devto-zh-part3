# 用 Javascript 实现我们自己的“split()”

> 原文：<https://dev.to/dhilipkmr/implementing-our-own-split-in-javascript-ii7>

## 我们要实现什么？

实现我们的自定义 split 函数`myCustomSplit`，它的行为方式与 javascript 中的`split()`相同。

### 输出:

```
const str = "val1&$val2&$val3";
const op = str.myCustomSplit('&$');
console.log(op);

// output
// ["val1", "val2", "val3"] 
```

请登录我的网站查看我所有的博客。

### 我们来分一下:

[![](img/3678c4f326b9a5158c71675565bbc152.png)](https://i.giphy.com/media/3og0IHyZMxZNkNOWT6/giphy.gif)T3】

```
String.prototype.myCustomSplit = function(splitVal) {
  const outputArr = [];
  const string = this;
  let nextVal = '';
  const splitlength = splitVal.length;
  let i = 0;
  // ...Some code
} 
```

*   `myCustomSplit` -自定义分割功能的名称。
*   `splitVal` -我们必须进行分割所基于的值。
*   `string` -执行 split 的字符串。
*   `outputArr` -将作为输出返回的数组。
*   `nextVal` -给出形成的中间字符串。
*   `splitlength`-`splitVal`的长度

```
while(i < string.length) {
  // When the current character is same as splitVal's first character
  if (string[i] === splitVal[0]) {
    let split_length_remaining = splitlength - 1;
    let split_length_passed = 1;
    let similarSplit = string[i];
    while(split_length_remaining) {
      if (string[i + split_length_passed] === splitVal[split_length_passed]) {
        similarSplit += string[i + split_length_passed];
        split_length_passed++;
        split_length_remaining--;
      } else {
        break;
      }
    }
    if (!split_length_remaining) {
      outputArr.push(nextVal);
      nextVal = '';
    } else  {
      nextVal += similarSplit;
    }
    i = i + split_length_passed;
  } else {    // When the current character is different from `splitVal` 's first character
    nextVal += string[i];
    i++;
  }
} 
```

## 解释:

*   循环整个字符串长度。
*   (否则的话)检查当前字符与`splitval`的第一个字符，如果它们是**不同的**与`nextVal`连接，并且将`i`加 1。
*   (如果情况)如果**当前字符**与`splitval`的第一个字符相同，那么我们进入**内部 while 循环**，这在拆分值大于单个字符时有帮助。

### 内参`if`案例:

*   `split_length_remaining`给出了我们需要迭代的`splitVal`的剩余长度。
*   `split_length_passed`被设置为 1，因为我们已经比较了`splitVal`的第一个字符。
*   在第一个值在内部 while 循环中匹配后，`similarSplit`帮助添加所有的值，因此如果`splitVal`的最后一个字符失败，那么我们可以将`similarSplit`的内容添加到`nextVal`变量中。
*   如果`split_length_remaining`不是`0`，我们没有完全比较所有的值。因此，我们继续寻找其他值，检查它们是否与输入字符串匹配。如果它们相等，我们增加`i`值并减少`split_length_remaining`。
*   如果`split_length_remaining`已经到达`0`，那么我们已经将`splitVal`中的所有值与字符串匹配，所以将内容推入`nextVal`并将其重置为**空字符串**。

```
 outputArr.push(nextVal);
 return outputArr; 
```

最后将`nextVal`中的内容全部推送到`outputArr`中并返回。

### 输出:

```
const x = "val1&$val2&$val3".myCustomSplit('&$');
console.log(x);
// output: ["val1", "val2", "val3"] 
```

在[代码笔](https://codepen.io/dhilipkmr/pen/KLKvdQ)处检查代码

[![](img/e295c6563ed112ee69f97eb8ca27e483.png)](https://i.giphy.com/media/ackDumdRl8fxm/giphy.gif)

关注我更多有趣的内容。

我的[网站](https://www.dhilipkmr.dev)、[博客](https://dev.to/dhilipkmr)和[推特](https://twitter.com/dhilipkmr_)

### 那都是乡亲们！
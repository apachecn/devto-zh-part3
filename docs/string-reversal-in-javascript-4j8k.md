# Javascript 中的字符串反转

> 原文：<https://dev.to/koolamusic/string-reversal-in-javascript-4j8k>

> # 30 天的 java 描述语言算法——D1

在这个 javascript 算法中，我将与大家分享用 javascript 反转字符串的不同方法。这是编码面试中的常见练习，入门级算法挑战，尽管不同语言中的实现可能不同，我们将了解如何在 javascript 中反转字符串，希望这对您有所帮助。

## 任务

给定一个字符串，返回一个字符顺序相反的新字符串

###### 例子

*   反转('苹果')=== 'leppa '
*   反向(' hello') === 'olleh '
*   反向('问候！') === '!斯格尼泰格

### 使用 Javascripts 内置数组原型

```
function reverse(str) {
  return str
    .split("")
    .reverse("")
    .join("");
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，我们使用了数组方法，`split`、`reverse`和`join`。如果我们执行了这个函数，我们的输出将类似于

```
reverse("hello") //here we reverse the string hello

function reverse(str) {
  return str
    .split("") // output here is an array ['h','e','l','l','o']
    .reverse("") // the output here is ['0','l','l','e','h']
    .join(""); // The array join method would join the array into a string "olleh"
} 
```

Enter fullscreen mode Exit fullscreen mode

### 使用减少回调

```
function reverse(str) {
  return str.split("").reduce((acc, curValue) => curValue + acc), ''
}; 
```

Enter fullscreen mode Exit fullscreen mode

reduce 函数接受一个累加器和一个值，这个函数将遍历数组中的每个元素，并根据我们传递给它的表达式生成一个输出，这里我们反向连接数组的元素，一种更清晰的方式是

```
function reverse(str) {
  let stringToArray = str.split("") // This expression creates a new array from our string
  return stringToArray
      .reduce( (acc, curValue) => curValue + acc), ''}; 
```

Enter fullscreen mode Exit fullscreen mode

### 使用*对*的语法

```
function reverse(str) {
  // we initially define reversed as an empty string in within the function
  let reversed = "";

  // The for of loop iterates over elements
  for (var character of str) {
    // var reversed = []
    // store the value into reversed recursively
    reversed = character + reversed;
  }
  // return the value of reveresed in our function
  return reversed;
} 
```

Enter fullscreen mode Exit fullscreen mode

for 循环可以遍历数组、字符串和 Javascript 结构，这些结构将`true`返回给`propertyIsEnumerable`参数。

[从“编码面试训练营:算法+数据结构”了解更多关于算法的信息](https://www.udemy.com/coding-interview-bootcamp-algorithms-and-data-structure/)
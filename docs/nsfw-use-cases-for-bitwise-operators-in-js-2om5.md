# NSFW:Js 中按位运算符的用例

> 原文：<https://dev.to/puritanic/nsfw-use-cases-for-bitwise-operators-in-js-2om5>

[Javascript 中的按位运算符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators)主要用于数值转换/计算，因为有时它们比它们的对等运算符`Math`或`parseInt`要快得多。你可以在这里看看一些基准[。](https://jsperf.com/math-floor-vs-math-round-vs-parseint/18)

重要的是要注意，正如 Mathias Bynens 指出的那样[，位运算只对可以表示为 32 位序列的数字有效。任何高于 2147483647 或低于-2147483648 的数字都不会如你所愿。这通常是可以接受的。](https://j11y.io/javascript/double-bitwise-not/#comment-29617)

但是，JavaScript 数字始终是 64 位二进制浮点值，遵循国际 IEEE 754 标准。因此，按位运算符的结果虽然是用 32 位整数数学计算的，但却以浮点形式存储。这之所以可行，是因为 32 位整数的范围非常适合 64 位浮点数。

但是你必须为速度的提高付出的代价是代码的可读性。自从我几年前开始编码以来，我可能使用过一两次按位操作符，因为我的大部分工作与计算没有太大关系。尽管如此，我对现实世界代码中的按位操作符的使用很感兴趣，并且我从互联网上收集了一些片段，以备不时之需😄。

* * *

以下是我收集的一些摘录:

##### `| 0`是一种简单快捷的将任何东西转换成整数的方法:

```
( 3|0 ) === 3;                 // it does not change integers
( 3.3|0 ) === 3;               // it casts off the fractional part in fractionalal numbers
( 3.8|0 ) === 3;               // it does not round, but exactly casts off the fractional part
( -3.3|0 ) === -3;             // including negative fractional numbers
( -3.8|0 ) === -3;             // which have Math.floor(-3.3) == Math.floor(-3.8) == -4
( "3"|0 ) === 3;               // strings with numbers are typecast to integers
( "3.8"|0 ) === 3;             // during this the fractional part is cast off too
( "-3.8"|0 ) === -3;           // including negative fractional numbers
( NaN|0 ) === 0;               // NaN is typecast to 0
( Infinity|0 ) === 0;          // the typecast to 0 occurs with the Infinity
( -Infinity|0 ) === 0;         // and with -Infinity
( null|0 ) === 0;              // and with null,
( (void 0)|0 ) === 0;          // and with undefined
( []|0 ) === 0;                // and with an empty array
( [3]|0 ) === 3;               // but an array with one number is typecast to number
( [-3.8]|0 ) === -3;           // including the cast off of the fractional part
( [" -3.8 "]|0 ) === -3;       // including the typecast of strings to numbers
( [-3.8, 22]|0 ) === 0         // but an Array with several numbers is typecast to 0
( {}|0 ) === 0;                // an empty object is typecast to 0
( {'2':'3'}|0 ) === 0;         // or a not empty object
( (function(){})|0 ) === 0;    // an empty function is typecast to 0 too
( (function(){ return 3;})|0 ) === 0; 
```

Enter fullscreen mode Exit fullscreen mode

##### 替换`Math.floor()`:

```
(~~n)                           
n|n
n&n

// Generate random RGB value:
var r = ~~(Math.random() * 255);

~~null;      // 0
~~undefined; // 0
~~0;         // 0
~~{};        // 0
~~[];        // 0
~~(1/0);     // 0
~~false;     // 0
~~true;      // 1
~~1.2543;    // 1
~~4.9;       // 4
~~(-2.999);  // -2

// An example
const n = Math.PI;   // 3.141592653589793

Math.floor(n);       // 3
parseInt(n, 10);     // 3
~~n; // 3 
n|n; // 3            // n|n and n&n always yield the same results as ~~n
n&n; // 3 
```

Enter fullscreen mode Exit fullscreen mode

应该注意的是，在最后三种选择中，`n|n` [似乎是最快的](https://jsperf.com/rounding-numbers-down)。

如果你知道你在处理积极的事情，那么`~~`的地板功能使它成为`Math.floor`的更好选择——它更快，占用的字符更少。不过，它的可读性不太好。

##### 解析十六进制值得到 RGB 颜色值:

```
var hex = 'ffaadd';
var rgb = parseInt(hex, 16); // rgb value is 16755421 in decimal = 111111111010101011011101 in binary = total 24 bits

var red   = (rgb >> 16) & 0xFF; // returns 255
var green = (rgb >> 8) & 0xFF;  // returns 170
var blue  = rgb & 0xFF;         // returns 221 

// How is it working:

// There are two bitwise operations as named SHIFTING and AND operations.
// SHIFTING is an operation where the bits are shifted toward a given direction by adding 0 (zero) bit for vacated bit fields.
// AND is an operation that is the same as multiplying in Math. For instance, if the 9th bit of the given first bit-set is 0
// and 9th bit of the given second bit-set is 1, the new value will be 0 because of 0 x 1 = 0 in math.

// 0xFF (000000000000000011111111 in binary) - used for to evaluate only last 8 bits of a given another bit-set by performing bitwise AND (&) operation. 
// The count of bits is 24 and the first 16 bits of 0xFF value consist of zero (0) value. Rest of bit-set consists of one (1) value.
console.log("0xFF \t\t\t\t: ", 0xFF) 

// 111111111010101011011101 -> bits of rgb variable
// 000000000000000011111111 -> 255 after (rgb >> 16) shifting operation
// 000000000000000011111111 -> 255 complement (changes the first 16 bits and does nothing for the last 8 bits)
// 000000000000000011111111 -> result bits after performing bitwise & operation
console.log("Red - (rgb >> 16) & 0xFF \t: ", (rgb >> 16) & 0xFF) // used for to evaluate the first 8 bits

// 111111111010101011011101 -> bits of rgb variable
// 000000001111111110101010 -> 65450 -> 'ffaa'
// 000000000000000011111111 -> 255 complement (changes the first 16 bits and does nothing for the last 8 bits)
// 000000000000000010101010 -> result bits after performing bitwise & operation
// calculation -> 000000001111111110101010 & 000000000000000011111111 = 000000000000000010101010 = 170 in decimal = 'aa' in hex-decimal
console.log("Green - (rgb >> 8) & 0xFF \t: ", (rgb >> 8) & 0xFF) // used for to evaluate the middle 8 bits 

// 111111111010101011011101 -> 'ffaadd'
// 000000000000000011111111 -> 255 complement (changes the first 16 bits and does nothing for the last 8 bits)
// 000000000000000011011101 -> result bits after performing bitwise & operation 
// calculation -> 111111111010101011011101 & 000000000000000011111111 = 221 in decimal = 'dd' in hex-decimal
console.log("Blue - rgb & 0xFF \t\t: ", rgb & 0xFF) // // used for to evaluate the last 8 bits.

console.log("It means that `FFAADD` hex-decimal value specifies the same color with rgb(255, 170, 221)") 
```

Enter fullscreen mode Exit fullscreen mode

##### `^`按位异或作为`I/O` toggler

```
// https://stackoverflow.com/a/22061240/7453363
function toggle(evt) {
  evt.target.IO ^= 1;                                    // Bitwise XOR as 1/0 toggler
  evt.target.textContent = evt.target.IO ? "ON" : "OFF"; // Unleash your ideas
} 
```

Enter fullscreen mode Exit fullscreen mode

##### 检查号码是否为奇数:

```
function isOdd(number) {
    return !!(number & 1);
}

isOdd(1); // true, 1 is odd 
```

Enter fullscreen mode Exit fullscreen mode

#### 检查 indexOf 是否返回-1:

```
var foo = 'abc';
!~foo.indexOf('bar'); // true 
```

Enter fullscreen mode Exit fullscreen mode

##### 翻转一个布尔值:

```
var foo = 1;
var bar = 0;

foo ^= 1 // 0
bar ^= 1 // 1 
```

Enter fullscreen mode Exit fullscreen mode

* * *

JavaScript 中的位操作符引入了奇怪的情况，其中`(12 & 9) === 8`和`(12 & 3) === 0`，如果你一开始不明白下面发生了什么(我认识的大多数人都不明白，包括我)，这看起来完全不合适。

在大多数情况下，性能差异是完全可以忽略的，尽管它们看起来很有吸引力。如果你真的需要挤出那些宝贵的微秒时间，你应该牺牲可读性来提高性能，如果你这样做了，请留下评论解释发生了什么，谁知道呢，也许有一天你会需要它😄。使用这些位技巧的另一个原因是让你的代码看起来比实际更复杂，这可能是一个愚蠢的原因。还有一些边缘情况需要注意，所以你不能仅仅用`~~`替换你当前所有的`Math.floor()`呼叫。事情很可能会失败。

## 参考文献

*   在 JavaScript 中，我应该在哪里使用按位运算符？
# ES2019/ES10 功能

> 原文：<https://dev.to/prabusubra/es2019-es10-features-5b14>

最近，TC39 委员会已经批准并在 ECMAScript 2019 标准中添加了一些功能，这些功能被移植到主要的 JavaScript 引擎，如 V8，SpiderMonkey…

### 完成特征:-

*   Array.prototype.{flat，flatmap}
*   String.prototype.{trimStart，trimEnd，matchAll*}
*   对象. fromEntries
*   Function.prototype.toString
*   Sysmbol.prototype.description
*   可选 catch 绑定
*   JSON 超集
*   格式良好的 JSON.stringify
*   比吉斯
*   globalThis*

** -在阶段 3*

### [T1】arrays . prototype . { flat，flatmap}](#arraysprototypeflat-flatmap)

增加了两个方法。

*   数组.原型.平面
*   Array.prototype.flatMap

数组.原型.平面:-

它最初被提议为 Array.prototype.flatten，递归地将数组展平到指定的深度，默认为 1。

```
 let data = [
    "alpha",
    "beta",
    ["gamma", "delta", ["epsilon", "zeta", ["eta", "theta", "iota", "kappa"]]]
  ];
  console.log(data.flat());
  //(5) ["alpha", "beta", "gamma", "delta", Array(3)]
  console.log(data.flat(2));
  //(7) ["alpha", "beta", "gamma", "delta", "epsilon", "zeta", Array(4)]
  console.log(data.flat(3));
  //(10) ["alpha", "beta", "gamma", "delta", "epsilon", "zeta", "eta", "theta", "iota", "kappa"]
  console.log(data.flat(Infinity));
  //(10) ["alpha", "beta", "gamma", "delta", "epsilon", "zeta", "eta", "theta", "iota", "kappa"] 
```

Array.prototype.flatMap:-

数组的组合展平和映射行为。将结果展平到单个数组中。

```
[1, 2, 3, 4, 5].map(data => [data * 2, data * 3]);
//[[2, 3], [4, 6], [6, 9], [8, 12], [10, 15]]

[1, 2, 3, 4, 5].map(data => [data * 2, data * 3]).flat();
//[2, 3, 4, 6, 6, 9, 8, 12, 10, 15];

[1, 2, 3, 4, 5].flatMap(data => [data * 2, data * 3]);
//[2, 3, 4, 6, 6, 9, 8, 12, 10, 15] 
```

### string . prototype . { trim start，trimEnd，matchAll*}:-

*   String.prototype.trimStart()
*   String.prototype.trimEnd()
*   String.prototype.matchAll()

```
 let data = " Hacker Rank ";
  console.log(data.trimStart()); //Hacker Rank
  console.log(data.trimEnd()); //Hacker Rank
  console.log(data.match("a")); //["a", index: 2, input: " Hacker Rank  ", groups: undefined]
  let iterator = data.matchAll("a");
  for (let i of iterator) console.log(i); 
  /*let result;
  do {
    result = iterator.next();
    console.log(result);
  } while (!result.done); */

//["a", index: 2, input: " Hacker Rank  ", groups: undefined]
//["a", index: 9, input: " Hacker Rank  ", groups: undefined] 
```

### function . prototype . tostring:-

*   如果函数是用 ECMAScript 代码编写，那么 toString 应该返回源代码。

```
 function multiply(a, b) {
    return a * b;
  }
  multiply.toString();
  /*"function multiply(a, b) {
    return a * b;
  }"*/
  let add = new Function("a", "b", "return a+b");
  add.toString();
  /*"function anonymous(a,b
  ) {
  return a+b
  }"*/ 
```

*   对于内置和绑定函数，它返回 NativeFunction 字符串。

```
 JSON.stringify.toString();
  //"function stringify() { [native code] }" 
```

#### 对象:-

*   一个新静态方法 Object.fromEntries 被添加到对象中。
*   具体来说，它将数组的数组(数组中有嵌套数组)转换为对象，让我们看看，它是如何工作的。

```
Object.fromEntries([["one", "alpha"], ["two", "beta"], ["three", "gamma"]]);
//{one: "alpha", two: "beta", three: "gamma"} 
```

###### 几个问题:-

1.  如果嵌套数组中有 2 个以上的元素呢？

```
 Object.fromEntries([
      ["one", "alpha", "two", "beta"],
      ["three", "gamma", "delta"]
    ]);
    //{one: "alpha", three: "gamma"} 
```

1.  如果数组中的数组包含对象呢？

```
 Object.fromEntries([
      [{ one: "alpha" }, { two: "beta" }],
      [{ three: "gamma" }, { four: "delta" }]
    ]);
    //{[object Object]: {four: "delta"}} 
```

### 符号:-

*   众所周知，Symbol 是建立在数据类型中的唯一标识符，我们可以为一个对象创建隐藏的属性。
*   添加了一个新的属性 Symbol.prototype.description 以从 Symbol 中获取描述。

```
const connectionUrl = Symbol("CONNECTION_URL");
console.log(onnectionUrl.description); //"CONNECTION_URL" 
```

*   示例:- Symbol.iterator，Symbol.asyncIterator，Symbol.match，Symbol.matchAll...

### 捕捉绑定:—

如果 catch 块中没有使用异常细节，开发人员可以愉快地删除 catch 绑定。这将删除未使用的和样板代码。

```
function add(a, b) {
  let result;
  try {
    if (typeof a === "number" && typeof b === "number") result = a + b;
    else result = "Not a valid number!";
  } catch {}
  return result;
} 
```

### BigInt:-

通过将 ***n 追加到整数*** 的末尾或者通过调用 ***构造函数*** 来创建 BigInt。

```
let bnum1 = 10n;
let bnum2 = BigInt(10); //10n
let bnum3 = BigInt(10.5);
//(d8):1: RangeError: The number 10.5 cannot be converted to a BigInt because it is not an integer.
let bnum4 = BigInt("10.5");
//(d8):1: SyntaxError: Cannot convert 10.5 to a BigInt. 
```

*   BigInt 不能和数字混合，但是可以和字符串串联。
*   不会用小数。
*   parseInt 返回数字。
*   JSON.stringify 不支持。
*   TypedArray BigInt64Array (-263 到 263-1) BigUint64Array (0 到 264-1)

### [T1】global this:](#globalthis)

*   从浏览器(窗口)、节点(全局)访问全局对象...
*   全局这将推断环境并返回全局对象。

```
var getGlobal = function() {
  if (typeof self !== "undefined") {
    return self;
  }
  if (typeof window !== "undefined") {
    return window;
  }
  if (typeof global !== "undefined") {
    return global;
  }
  throw new Error("unable to locate global object");
}; 
```

结论:-

我们知道，ECMAScript 标准被用于各种编程语言中，如微软的 JScript、Oracle Nashorn Engine，它严格地基于语言引擎来选择支持哪种标准。让我们关注 TC39 提案的最新更新。

感谢阅读！！！

请随意评论你的建议。
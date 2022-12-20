# ES2019 功能

> 原文：<https://dev.to/hemanth/es2019-features-1fja>

来自[h3manth.com](https://h3manth.com/new/blog/2019/es2019-features/)的横梁。

因此，是时候发表另一篇文章了，这次是 ES2019 功能。

不要浪费时间在一些占位符内容上，下面是功能和示例:

**数组#{flat，flatMap}**

```
[1, 2, 3].flatMap((x) => [x, x * 2]);
// => [1, 2, 2, 4, 3, 6]

[1, [2, [3]]].flat(Infinity);
// => [1, 2, 3] 
```

Enter fullscreen mode Exit fullscreen mode

**object . from entries**T2】

```
const iterableOfEntries = new Map([
    ['cat', 'dog'],
    ['life', 42]
]);
const obj = Object.fromEntries(iterableOfEntries);
console.log(obj); // { cat: "dog", life: 42 } 
```

Enter fullscreen mode Exit fullscreen mode

**String#{trimStart，trimEnd}**

```
" Hey JS!".trimStart(); // "Hey JS!"
    "Hey JS! ".trimEnd(); // "Hey JS!" 
```

Enter fullscreen mode Exit fullscreen mode

**符号#描述**

```
const symbol = Symbol('TC39');
console.log(symbol.description); // 'TC39'
console.log(symbol.hasOwnProperty('description')); // false 
```

Enter fullscreen mode Exit fullscreen mode

**尝试{ }捕捉{} //可选绑定**

```
try {
    throw new Error("End of life!");
} catch { // ✋
    console.log("^ no params for catch, you are dead anyway!");
} 
```

Enter fullscreen mode Exit fullscreen mode

**JSON⊂ecmascript**T2】

```
// extend ECMA-262 syntax into a superset of JSON.
const LS = "";
const PS = eval("'\u2029'"); 
```

Enter fullscreen mode Exit fullscreen mode

**周正`JSON.stringify`**T3】

```
JSON.stringify('\uD800');
// → '"\\ud800"' 
```

Enter fullscreen mode Exit fullscreen mode

**函数#toString**

```
function /* this is bar */ bar () {}

bar.toString(); // 'function /* this is bar */ bar () {}'

// ^ perviously this was not the case. 
```

Enter fullscreen mode Exit fullscreen mode

**数组#排序稳定性**

```
[
    { name: "Jan",     age: 20 },
    { name: "Jhon",    age: 20 },
    { name: "David",   age: 18 },
    { name: "Ram",     age: 18 },
    { name: "Sita",    age: 18 },
    { name: "Ravan",   age: 18 },
    { name: "Asura",   age: 12 },
    { name: "Milly",   age: 12 },
].sort((m, n) => m.age - n.age));

// People with the same age retain their order. 
```

Enter fullscreen mode Exit fullscreen mode

**不要错过:**

> 🎉ES2019 中的新 JavaScript 功能:
> 阵列#{flat，flat map }
> object . fromentries】
> 字符串{ trimstart，quarter }
> symbol # description
> try { } { }//optional binding
> 
> — Mathias Bynens ([@mathias](https://dev.to/mathias) ) [January 29, 2019](https://twitter.com/mathias/status/1090380500739506176?ref_src=twsrc%5Etfw)
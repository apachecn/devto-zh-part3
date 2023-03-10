# 漂亮的 JSON 输出

> 原文：<https://dev.to/samanthaming/pretty-json-output-2d8d>

[![Code Tidbit by SamanthaMing.com](img/a004b845166dd59d55f54e1445eed8b0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_mWVBJsL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/st6iqi2l6abnswo4xwqp.png)

厌倦了一行 JSON 输出，嗯不再！利用`JSON.stringify`内置漂亮打印。将第三个参数设置为您想要的间距水平👍砰，瞬间 GLAM✨

```
const protein = {steak: '🥩', bacon: '🥓'};

JSON.stringify(protein);
// {"steak":"🥩","bacon":"🥓"}

JSON.stringify(protein, null, 2);
/*
{
  "steak": "🥩",
  "bacon": "🥓"
}
*/ 
```

* * *

## 制表符间距😉

但是账单上的人说我们怎么样？？不要担心，你也可以通过`"\t"`来获得标签级间距😄

```
const protein = {steak: '🥩', bacon: '🥓'};

JSON.stringify(protein, null, "\t");

/*
{
    "steak": "🥩",
    "bacon": "🥓"
}
*/ 
```

* * *

## 理解“空间”的说法

`JSON.stringify`的第三个参数用于控制间距。它给了你漂亮的字符串输出。

它允许两种类型的参数:数字和字符串。

### a .数量

您可以使用 0 到 10 之间的任何数字作为缩进。

```
const protein = {steak: '🥩', bacon: '🥓'};

JSON.stringify(protein, null, 1);
/*
{
 "steak": "🥩",
 "bacon": "🥓"
}
*/ 
```

### b .字符串

或者，您可以使用字符串作为缩进。它最多允许 10 个字符。如果您尝试传递超过 10 个字符，它将只使用前 10 个字符。所以不要试图打败这个系统😝

```
const protein = {steak: '🥩', bacon: '🥓'};

JSON.stringify(protein, null, "I 💛");
/*
{
I 💛"steak": "🥩",
I 💛"bacon": "🥓"
}
*/ 
```

* * *

## 第二个参数是什么🤔

第二个参数也称为**替换器**参数。你可以用它来转换结果。

它允许两种类型的参数:数组和函数。

### a .阵列

当你通过一个`array`时，我想给你看一些真正有趣的东西。您可以使用它来挑选想要输出的键值对。

```
const protein = {
  steak: '🥩', 
  bacon: '🥓',
  pop: '🥤',
  tea: '🍵',
  shrimp: '🍤',
};

JSON.stringify(protein, ['steak', 'pop'], 2);
/*
{
  "steak": "🥩",
  "pop": "🥤"
}
*/ 
```

### b .功能

为每个项目调用**替换器**。所以也可以传入一个`function`。这意味着你可以循环遍历每一项，每一遍都用函数中定义的逻辑进行操作。

这里有一个例子，我跳过了值不是字符串的属性。换句话说，我只想显示值为数字的项目。

```
const protein = {
  steak: '🥩', 
  calorie: 271,
  bacon: '🥓',
  sodium: 58,
};

const replacer = function(key, value) {
  if(typeof value !== "string") {
    return value
  }
  return undefined;
}

JSON.stringify(protein, replacer, 2);
/*
{
  "calorie": 271,
  "sodium": 58
}
*/ 
```

* * *

## 资源

*   [MDN Web Docs-JSON . stringify](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify)
*   [w3schools - JSON.stringify](https://www.w3schools.com/js/js_json_stringify.asp)
*   [堆栈溢出:如何使用 JavaScript 美化 JSON？](https://stackoverflow.com/questions/4810841/how-can-i-pretty-print-json-using-javascript)

* * *

**感谢阅读❤**
问好！[insta gram](https://www.instagram.com/samanthaming/)|[Twitter](https://twitter.com/samantha_ming)|[脸书](https://www.facebook.com/hisamanthaming) | [媒体](https://medium.com/@samanthaming) | [博客](https://www.samanthaming.com/blog)
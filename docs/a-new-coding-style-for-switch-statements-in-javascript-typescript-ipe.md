# JavaScript/TypeScript 中 Switch 语句的新编码风格

> 原文：<https://dev.to/nebrius/a-new-coding-style-for-switch-statements-in-javascript-typescript-ipe>

我最近为 JavaScript 中的`switch`语句采用了一种新的编码风格(嗯，技术上来说是 TypeScript)，我真的很想听听你们对它的看法！

在讨论风格之前，让我解释一下我要解决的问题。switch 语句在不同的情况下有一些重复的代码并不罕见，但是在每种情况下都有变化，因此您很难对其进行抽象。让我们举一个用 ES5 写的例子(我保证这是有原因的)。

```
switch (body.type) {
  case 'isBasic':
    var entry = getBasicEntry(body.id);
    console.log('Processing basic entry "' + entry.name + '"');
    doBasicProcessing(entry);
    break;

  case 'isCustom':
    var entry = getCustomEntry(body.id);
    console.log('Processing custom entry "' + entry.name + '"');
    doCustomprocessing(entry);
    break;

  default:
    throw new Error('Unknown flag ' + myFlag);
} 
```

酷，这是一个基本的开关语句，它工作得很好。现在，让我们将其转换为 ES2015+。

```
switch (body.type) {
  case 'isBasic':
    const entry = getBasicEntry(body.id);
    console.log(`Processing basic entry "${entry.name}"`);
    doBasicProcessing(entry);
    break;

  case 'isCustom':
    const entry = getCustomEntry(body.id);
    console.log(`Processing custom entry "${entry.name}"`);
    doCustomprocessing(entry);
    break;

  default:
    throw new Error(`Unknown flag ${myFlag}`);
} 
```

啊哦，我们现在在运行这段代码时会得到一个`SyntaxError`异常！这里的问题是`const`变量名`entry`不能在同一个作用域声明两次。由于作用域规则的工作方式，整个`switch`语句是一个单一的作用域。

我们可以通过用不同的变量名命名每个`entry`来解决这个问题，但是老实说，我觉得这有点烦人。我们还可以为每种情况创建一个助手函数，但是对于这样短的 case 语句来说，这对我来说太多了。

这是我发现的，我已经开始在我所有的代码中使用它:

```
switch (body.type) {
  case 'isBasic': {
    const entry = getBasicEntry(body.id);
    console.log(`Processing basic entry "${entry.name}"`);
    doBasicProcessing(entry);
    break;
  }

  case 'isCustom': {
    const entry = getCustomEntry(body.id);
    console.log(`Processing custom entry "${entry.name}"`);
    doCustomprocessing(entry);
    break;
  }

  default: {
    throw new Error(`Unknown flag ${myFlag}`);
  }
} 
```

我将每个 case 语句包装在`{}`中，这为每个案例创建了一个新的范围。这解决了问题，我认为这是一种优雅。虽然这并不常见。

你们都是怎么想的？这看起来是个好方法吗？你有不同的方法吗？我很想在评论里听到！

## 更新:

感谢大家到目前为止的讨论，很棒！我最终创建了一个名为 [conditional-reduce](https://www.npmjs.com/package/conditional-reduce) 的新模块，它自动化了另一种我以前没有想到的方法:

```
const { reduce } = require('conditional-reduce');

console.log(reduce('dog', {
  dog: () => 'Dogs are great pets',
  cat: () => 'Cat\'s are also great'
})); // Prints "Dogs are great pets"

console.log(reduce('bird', {
  dog: () => 'Dogs are great pets',
  cat: () => 'Cat\'s are also great'
})); // Throws 'Invalid conditional value "bird"' exception 
```

```
const { curry } = require('conditional-reduce');

const dogReducer = curry({
  dog: () => 'Dogs are great pets',
  cat: () => 'Cat\'s are also great'
});

console.log(dogReducer('dog')); // Prints "Dogs are great pets"
console.log(dogReducer('bird')); // Throws 'Invalid conditional value "bird"' exception 
```

感谢 [@avalander](https://dev.to/avalander) 和 [@john_papa](https://dev.to/john_papa) 的讨论促成了这件事！
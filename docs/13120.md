# 为什么我更喜欢对象而不是开关语句

> 原文：<https://dev.to/duranenmanuel/why-i-prefer-objects-over-switch-statements-4emo>

最近(或者不是最近，取决于你何时阅读这篇文章)，我与一些队友就如何处理需要多重评估的条件进行了辩论，通常在这种情况下，人们喜欢使用 switch 语句或带有多个`else if`条件的巨大`if`。在本文中，我将关注第三种方法(我更喜欢的方法)，我们将利用对象进行快速查找。

## 切换语句

switch 语句允许我们对一个表达式求值，并根据传递的表达式的值做一些特殊的事情，通常当你学习写代码和算法时，你知道你可以专门用它进行多重求值，你开始使用它，它看起来很好，你很快意识到它给了你很大的自由，耶！但是要小心，巨大的自由伴随着巨大的责任。

让我们快速看一下典型的 switch 语句是什么样子的:

```
switch (expression) {
    case x: {
        /* Your code here */
        break;
    }
    case y: {
        /* Your code here */
        break;
    }
    default: {
        /* Your code here */
    }
} 
```

很好，现在有几件你可能不知道你需要注意的事情:

### break 关键字是可选的。

break 关键字允许我们在条件已经满足时停止块的执行。如果您没有在 switch 语句中添加关键字`break`,它不会抛出错误。意外丢失一个`break`关键字可能意味着执行你甚至不知道正在执行的代码，这也增加了我们的实现的不一致性、突变、内存泄漏和调试问题时的复杂性。让我们来看看这个问题的一个表现:

```
switch ('first') {
    case 'first': {
        console.log('first case');
    }
    case 'second': {
        console.log('second case');
    }
    case 'third': {
        console.log('third case');
        break;
    }
    default: {
        console.log('infinite');
    }
} 
```

如果您在控制台中执行这段代码，您会看到输出是

```
firt case
second case
third case 
```

switch 语句执行第二个和第三个 case 中的块，即使第一个 case 已经是正确的，然后它在第三个 case 块中找到关键字`break`并停止执行，控制台中没有警告或错误让您知道，这是期望的行为。

### 每个案例上的花括号是*而不是*强制的。

在 javascript 中，花括号表示代码的*块*，因为 *ECMAscript 2015* 我们可以使用像`const`或`let`这样的关键字来声明块范围的变量，这很好(但对于开关情况来说不太好)，因为花括号不是强制的，我们可能会因为变量的重复而出错，让我们看看当我们执行下面的代码时会发生什么:

```
switch ('second') {
    case 'first':
        let position = 'first';
        console.log(position);
        break;
    case 'second':
        let position = 'second';
        console.log(position);
        break;
    default:
        console.log('infinite');
} 
```

我们会得到:

```
Uncaught SyntaxError: Identifier 'position' has already been declared 
```

这将返回一个错误，因为变量`position`已经在第一个例子中被声明，并且因为它没有花括号，所以它被
提升，然后当第二个例子试图声明它的时候，它已经存在并且**繁荣**。

现在想象一下当使用带有不一致的`break`关键字和花括号的 switch 语句时会发生什么:

```
switch ('first') {
    case 'first':
        let position = 'first';
        console.log(position);
    case 'second':
        console.log(`second has access to ${position}`);
        position = 'second';
        console.log(position);
    default:
        console.log('infinite');
} 
```

这将在控制台记录以下内容:

```
first
second has access to first
second
infinite 
```

想象一下，由此可能引入的错误和突变的数量，这种可能性是无穷无尽的...不管怎样，switch 语句说够了，我们来这里讨论一种不同的方法，我们来这里讨论对象。

## 更安全查找的对象

对象查找速度很快，而且随着大小的增长速度也越来越快，它们还允许我们将数据表示为键值对，这对于条件执行来说非常有用。

### 处理字符串

让我们从一些简单的东西开始，比如 switch 示例，假设我们需要有条件地保存和返回一个字符串，使用我们可以做的对象:

```
const getPosition = position => {
    const positions = {
        first: 'first',
        second: 'second',
        third: 'third',
        default: 'infinite'
    };

    return positions[position] || positions.default;
};

const position = getPosition('first'); // Returns 'first'
const otherValue = getPosition('fourth'); // Returns 'infinite' 
```

这将完成相同的工作，如果您想进一步压缩这个实现，我们甚至可以利用箭头函数的更多优势:

```
const getPosition = position =>
    ({
        first: 'first',
        second: 'second',
        third: 'third'
    }[position] || 'infinite');

const positionValue = getPosition('first'); // Returns 'first'
const otherValue = getPosition('fourth'); // Returns 'infinite' 
```

这与前面的实现完全相同，我们用更少的代码行实现了更紧凑的解决方案。

现在让我们现实一点，不是所有我们写的条件都会返回简单的字符串，很多会返回布尔，执行函数等等。

### 使用布尔值

我喜欢以返回一致类型的值的方式来创建我的函数，但是，由于 javascript 是一种动态类型的语言，可能会有函数返回动态类型的情况，所以我将在这个示例中考虑这一点，并且如果没有找到键，我将创建一个返回**布尔值**、**未定义的**或**字符串**的函数。

```
const isNotOpenSource = language =>
    ({
        vscode: false,
        sublimetext: true,
        neovim: false,
        fakeEditor: undefined
    }[language] || 'unknown');

const sublimeState = isNotOpenSource('sublimetext'); // Returns true 
```

看起来很棒，对吧？，但是等等，好像我们有问题...如果我们调用带有参数`'vscode'`或`fakeEditor`的函数会发生什么？嗯，让我想想:

1.  它会在物体中寻找钥匙。
2.  它会看到 vscode 键的值是`false`。
3.  它会尝试返回`false`，但是由于`false || 'unknown'`是`unknown`，我们最终会返回一个不正确的值。

键`fakeEditor`也会有同样的问题。

哦不，好吧，别慌，让我们来解决这个问题:

```
const isNotOpenSource = editor => {
    const editors = {
        vscode: false,
        sublimetext: true,
        neovim: false,
        fakeEditor: undefined,
        default: 'unknown'
    };

    return editor in editors ? editors[editor] : editors.default;
};

const codeState = isNotOpenSource('vscode'); // Returns false
const fakeEditorState = isNotOpenSource('fakeEditor'); // Returns undefined
const sublimeState = isNotOpenSource('sublimetext'); // Returns true
const webstormState = isNotOpenSource('webstorm'); // Returns 'unknown' 
```

这解决了问题，但是...我要你问自己一件事:这真的是问题所在吗？我认为我们应该更担心为什么我们首先需要一个返回`boolean`、`undefined`或`string`的函数，这是一些严重的不一致，无论如何，这只是一个非常尖锐的情况下可能的解决方案。

### 用函数工作

让我们继续函数，我们经常发现自己需要根据参数执行函数，假设我们需要根据输入的类型解析一些输入值，如果解析器没有注册，我们只返回值:

```
const getParsedInputValue = type => {
    const emailParser = email => `email, ${email}`;
    const passwordParser = password => `password, ${password}`;
    const birthdateParser = date => `date , ${date}`;

    const parsers = {
        email: emailParser,
        password: passwordParser,
        birthdate: birthdateParser,
        default: value => value
    };

    return parsers[type] || parsers.default;
};

// We select the parser with the type and then passed the dynamic value to parse
const parsedEmail = getParsedInputValue('email')('myemail@gmail.com'); // Returns email, myemail@gmail.com
const parsedName = getParsedInputValue('name')('Enmanuel'); // Returns 'Enmanuel' 
```

如果我们有一个类似的函数返回另一个函数，但这次没有参数，我们可以改进代码，在第一个函数被调用时直接返回，类似于:

```
const getValue = type => {
    const email = () => 'myemail@gmail.com';
    const password = () => '12345';

    const parsers = {
        email,
        password,
        default: () => 'default'
    };

    return (parsers[type] || parsers.default)(); // we immediately invoke the function here
};

const emailValue = getValue('email'); // Returns myemail@gmail.com
const passwordValue = getValue('name'); // Returns default 
```

## 常用代码块

Switch 语句允许我们为多个条件定义公共代码块。

```
switch (editor) {
    case 'atom':
    case 'sublime':
    case 'vscode':
        return 'It is a code editor';
        break;
    case 'webstorm':
    case 'pycharm':
        return 'It is an IDE';
        break;
    default:
        return 'unknown';
} 
```

我们如何使用对象来处理这个问题呢？，我们可以用另一种方式来做:

```
const getEditorType = type => {
    const itsCodeEditor = () => 'It is a code editor';
    const itsIDE = () => 'It is an IDE';

    const editors = {
        atom: itsCodeEditor,
        sublime: itsCodeEditor,
        vscode: itsCodeEditor,
        webstorm: itsIDE,
        pycharm: itsIDE,
        default: () => 'unknown'
    };

    return (editors[type] || editors.default)();
};

const vscodeType = getEditorType('vscode'); // Returns 'It is a code editor' 
```

现在我们有了一种方法:

1.  更有条理。
2.  scalebetter。
3.  更容易维护。
4.  更容易测试。
5.  更安全，副作用和风险更小。

## 将事情考虑进去

正如所料，所有的方法都有失败的时候，这一个也不例外。

1.  因为我们使用对象，我们将在内存中占用一些临时空间来存储它们，当定义对象的范围不再可访问时，由于垃圾收集器，这些空间将被释放。

2.  当没有很多情况要评估时，Objects 方法可能没有 switch 语句快，这可能是因为我们创建了一个数据结构，然后在 switch 中访问一个键，我们只是检查值并返回。

## 结论

这篇文章并不打算改变你的编码风格或让你停止使用 switch 语句，它只是试图提高人们的意识，以便可以正确地使用它，并打开你的思维去探索新的替代方法，在这种情况下，我已经分享了我喜欢使用的方法，但还有更多，例如，你可能想看看一个名为[模式匹配](https://github.com/tc39/proposal-pattern-matching)的 ES6 提案，如果你不喜欢它，你可以继续探索。

好了，未来的发展，就这样，我希望你喜欢这篇文章，如果你喜欢，你可能也会喜欢这篇关于工厂模式的文章。另外，别忘了分享和订阅，你可以在[推特](https://twitter.com/duranenmanuel)上找到我，或者通过我的电子邮件[duranenmanuel@gmail.com](mailto:duranenmanuel@gmail.com)联系我，下期见。

阅读贴在[EnmaScript.com](https://enmascript.com/articles/2018/10/22/why-I-prefer-objects-over-switch-statements)的原文
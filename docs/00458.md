# TypeScript 简介(面向有经验的开发人员)

> 原文：<https://dev.to/walkingriver/introduction-to-typescript-for-experienced-developers-25hd>

在我的日常工作中，我的领导要求我向一群有经验的软件开发人员介绍 TypeScript、Angular 和 Node。这些人很多都是“资深”或更高的，有些比我有更多年的经验。尽管他们总共发展了 200 多年，他们还没有使用这些技术。这篇文章是我打算向他们展示的内容的第 1 部分:为有经验的开发人员介绍 TypeScript。

# 打字操场

为了帮助内化我们将要看到的概念，考虑打开一个浏览器到 http://www.typescriptlang.org/play/。这个网站让你在一个文本区输入 TypeScript，并立即看到相应的 JavaScript。它还在出现类型脚本错误时提供有用的反馈。我发现理解 TypeScript 翻译过程中发生的事情非常方便。

# 类型！

关于 TypeScript 要理解的第一件事是——令人惊讶的是——它有类型。它支持您所期望的基本类型，比如字符串和数字。您可以显式提供类型，如上面的示例所示。或者省略类型并提供一个值。如果您为变量提供一个值，那么 TypeScript 将自动推断类型。

```
// Explicit Type Declaration
let isDone: boolean = false;
let decimal: number = 6;
let hex: number = 0xf00d;
let binary: number = 0b1010;
let octal: number = 0o744;

// Implicit Type Declaration
let isDone = false;
let decimal = 6;
let hex = 0xf00d;
let binary = 0b1010;
let octal 0o744; 
```

Enter fullscreen mode Exit fullscreen mode

然而，不要被愚弄了。类型推断仍然意味着变量有类型；您根本不需要设置它。试图给一个字符串赋值仍然是错误的。

# 字符串

TypeScript 包括对字符串的支持。字符串常量可以使用单引号或双引号，但应该保持一致。许多团队喜欢其中的一种，并使用工具来加强这种偏好。

```
let color: string = 'red';
let fullName = "Bob Bobbington";
let age = 37; 
```

Enter fullscreen mode Exit fullscreen mode

第三种类型的引号是反引号，用于创建模板字符串。本例中的句子变量是一个三行字符串——是的，模板化的字符串可以包含换行符。对`${ }`中的表达式求值，该值替换整个`${ }`结构。看看 JavaScript 中模板字符串转换成什么是很有趣的:带有预期连接的老式字符串赋值。

```
let sentence = `Hello, my name is ${ fullName }.
I'll be ${ age + 1 } years old next month.`; 
```

Enter fullscreen mode Exit fullscreen mode

# 数组

TypeScript 支持数组。在这里，我们将 list 创建为一个数字数组，如类型后面的方括号所示。当然，这是不必要的，因为等号右边的值清楚地表明它是一个数字数组。大多数情况下，如果不同时提供值，您只需要显式设置类型。

```
let list: number[] = [1, 2, 3];
let list = [1, 2, 3]; 
```

Enter fullscreen mode Exit fullscreen mode

# 枚举

枚举(Enumerations)允许您提供一组人类可读的值，这些值代表变量可以包含的唯一合法值。在本例中，我定义了一个名为 Color 的枚举，它包含三个值:红色、绿色和蓝色。这种类型的常量或变量只能取这三个值中的一个。如您所见，要使用赋值右侧的值，您需要指定枚举名称，后跟一个点，然后是枚举值。

试图设置不存在的值是错误的。

```
enum Color {Red, Green, Blue}
const green: Color = Color.Green;

const purple = Color.Purple; // Error! 
```

Enter fullscreen mode Exit fullscreen mode

# 界面

接口作为纯 TypeScript 结构受到支持，不编译成任何类型的 JavaScript。继续在 TypeScript plaground 中输入接口定义。

不要让那件事欺骗你，让你认为它们没有用。

接口可用于强制传递给函数的数据的形状，或者在从对象文本初始化强类型对象时提供帮助。这里定义的接口由四个字符串值组成，并且要求任何定义为成员的对象都包含电子邮件地址、名字和姓氏。电话号码是可选的，由字段名称末尾的问号表示。

为数据对象使用接口将有助于 TypeScript 编译器帮助您在初始化对象时防止常见的拼写错误，而不必在并非绝对必要的地方构建完整的类。

```
interface Member {
  email: string;
  firstName: string;
  lastName: string;
  phone?: string; // The ? indicates this is optional
} 
```

Enter fullscreen mode Exit fullscreen mode

# 对象和数组文字

例如，假设我们想要初始化一个由`Member`对象组成的数组。将类型显式指定为`Member[]`将让 TypeScript 编译器知道检查提供的每个对象文字是否匹配`Member`接口定义。请注意，我没有提供电话号码，因为该字段在界面中被标记为可选。

```
const allMembers: Member[] = [
  {email: '1234@company.com', firstName: 'Mike', lastName: 'Smith'},
  {email: '2345@company.com', firstName: 'Bob', lastName: 'Johnson'},
  {email: '3456@company.com', firstName: 'Debbie', lastName: 'Jones' },
  {email: '4567@company.com', firstName: 'Carol', lastName: 'Brown'}
]; 
```

Enter fullscreen mode Exit fullscreen mode

如果`Member`被定义为一个类，我们将需要使用它的构造函数来定义新的`Member`对象，这将需要更多的代码，并且最终比这个方法可读性差很多。

# 类

TypeScript 还提供了具体的类。下面是同一个`Member`接口的一个假想的类实现。正如您所看到的，它包含了更多的代码，并且绝不比简单地使用接口更具可读性。

```
class MemberImpl implements Member {
  email: string;
  firstName: string;
  lastName: string;
  phone?: string;

  constructor(email: string,
              firstName: string,
              lastName: string,
              phone: string = '') {

    this.email = email;
    this.firstName = firstName;
    this.lastName = lastName;
    this.phone = phone;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我的一般建议是不要使用一个类，除非你有需要添加的代码。即使这样，我也认为在向数据对象添加代码之前应该三思。

诸如验证之类的代码在单独的实用程序类中完成。让你的数据对象保持干净，但是那是另外一个话题。

# 仿制药

像许多其他现代语言一样，TypeScript 支持泛型。这允许您创建操作相同的公共代码，而不管传递给它的是什么类型。在这里，我们展示了一个表示一堆对象的潜在接口，由三个函数组成:`pop`、`push`和`length`。

```
interface Stack<T> {
  pop(): T;
  push(item: T);
  length(): number;
} 
```

Enter fullscreen mode Exit fullscreen mode

注意在接口定义中包含单个`T`的尖括号。这将接口声明为泛型，用`T`表示编译时类型。使用`T`是一种常见的约定，但是它可以是任何未使用的标识符。函数`pop()`返回该类型的单个对象；`push()`接受相同类型的项目参数。`length()`函数返回一个数字。

下面你可以看到两个类型为`Stack`的变量的创建，每个变量传递一个不同的类型。正如您可能猜到的，您不能将错误类型的参数传递给它的任何函数。

```
let numbers: Stack<number>;
let names: Stack<string>;
names.push('Mike'); // OK
names.push(5); // Error 
```

Enter fullscreen mode Exit fullscreen mode

# 箭头功能

TypeScript 支持箭头函数，它可以用来代替您可能已经习惯的匿名函数。在这里显示的例子中，我们调用窗口的`setTimeout`函数延迟五秒钟，然后显示一个警告。

```
window.setTimeout(function() {
  alert('It has been five seconds!');
}, 5000);
window.setTimeout(() => {
  alert('It has been five seconds!');
}, 5000);

window.ondblclick = (ev: MouseEvent) => {
  // `this` refers to the class
  this.mouseX = ev.clientX;
  this.mouseY = ev.clientY;
}; 
```

Enter fullscreen mode Exit fullscreen mode

乍一看，你可能想知道这有什么大不了的。我们去掉了关键字`function`，添加了等号和大于号(就是箭头)。这总共节省了三个字符，为什么要这么麻烦呢？

在我看来，箭头函数最重要的方面是箭头右边的匿名函数没有重新定义变量`this`。如果您曾经被 JavaScript 在函数内部重新定义`this`所吸引，您将会欣赏这种行为。现在，您可以在类内部编写事件处理程序，并且仍然可以正确地引用类变量，只需通过在事件处理程序箭头函数内部使用`this`对象来引用它们。

# 承诺

TypeScript 支持承诺，所以不需要依赖外部库，比如 Bluebird。

承诺是保证某个功能将在未来某个未知时间完成。这方面的经典例子是 HTTP 调用。当您发出 HTTP 请求时，您正在等待远程服务器向您返回数据。如果您等待回复，您的代码就会停滞不前，不能做任何其他事情，包括响应用户交互。因此，许多异步函数返回一个承诺，而不是实际结果。当函数最终完成时，承诺被称为“解决”如果有错误，该承诺被称为“拒绝”您在代码中处理它的方式很简单，但是如果您以前没有见过的话，这就有点奇怪了。

```
function getMyIpAddress(): Promise<string> {
  return fetch('https://api.ipify.org/?format=json')
    .then(response => response.json())
    .catch(error => console.log(error));
}

getMyIpAddress()
  .then(ip => console.log(`Your IP address is: ${ip}`))
  .catch(error => console.log(error)); 
```

Enter fullscreen mode Exit fullscreen mode

这里我们看到一个函数的简单实现，它检索运行它的浏览器的面向公众的 IP 地址。请注意，我们在这里结合了多个概念。该函数返回类型为`string`的承诺。调用函数调用`fetch`，它也返回一个承诺。但是，它还对返回的承诺调用了另外两个函数。`then`函数接受一个在承诺本身被解析时被调用的函数。在这个例子中，我传递了一个 arrow 函数，该函数将响应转换为 json，并返回该值。我在这里使用了一个快捷方式，如果一个 arrow 函数只返回一个表达式的结果，你可以省略 return 关键字和分号。`then`函数本身返回一个承诺，我将其称为`catch`函数。`catch`也接受在出现错误时调用的函数。这里我提供了另一个箭头函数，它接受错误对象并记录下来。

本质上，这里发生的是请求提供的 URL。在未来的某个时刻，两件事中的一件会发生。

1.  要么 HTTP 调用成功，此时它被“解析”，然后传递给`then()`的函数被调用。
2.  或者 HTTP 调用失败，承诺被“拒绝”，调用传递给`catch()`的函数。

最后，还要注意`getMyIpAddress()`函数本身返回一个承诺，因此调用它看起来非常相似。这是一种常见的模式，您会看到在 TypeScript 中经常重复出现。

# 承诺-异步/等待

承诺的另一种模式是使用`async`和`await`。任何标有关键字`async`的函数都可以调用承诺上的`await`，这使得代码看起来更有命令性，并且更容易阅读。

在这个例子中，我用`async`和`await`重写了`getMyIpAddress()`和它的客户端。不再有`.then()`或`.catch()`。相反，该功能看起来和读起来完全像一个没有任何异步调用的函数。然而，这纯粹是一种错觉，如果您将代码粘贴到 TypeScript Playground 中，就可以看到这一点。

```
async function getMyIpAddress(): Promise<string> {
  const response = await fetch('https://api.ipify.org/?format=json')
  return response.json();
}

try {
  const ip = await getMyIpAddress();
  console.log(`Your IP address is: ${ip}`);
}
catch(error) {
  console.log(error);
} 
```

Enter fullscreen mode Exit fullscreen mode

当用`await`关键字调用`fetch`函数时，`getMyIpAddress`函数立即退出。当`fetch`解析成功时，promise 的结果被设置为`response`常量的值，然后继续执行下一行代码。如果 promise 拒绝，这个错误将作为一个异常被抛出，这个异常将被更高一级捕获。

同样，对`getMyIpAddress`的调用也是如此。执行在那一行停止(但是应用程序继续正常运行)。当`getMyIpAddress`返回的承诺被解析时，它的响应被设置为常量`ip`的值，并从该点开始执行。任何异常抛出都将在`catch`块中被捕获。

使用`async`和`await`是很棒的，因为你的代码看起来更传统，但是你永远不要忘记在魔法之下还有承诺。

# 有些人比另一些人更平等

什么时候价值观是平等的？答案可能会让你大吃一惊。虽然不是专门针对 TypeScript 的，但这个主题吸引了许多新的 JavaScript 和 TypeScript 开发人员。考虑下面的例子。

```
console.log('1' == 1); // true
console.log('' == 0); // true
console.log('1' == [1]); // true 
```

Enter fullscreen mode Exit fullscreen mode

如果你预期这些都是假的，那么这个解释是给你的。如果您在 TypeScript 操场上跟随，您会注意到它警告您不要做那些事情。

问题是 JavaScript 会强制从一种类型转换到另一种类型来进行比较，即使这不是您想要或期望的。解决办法是用`===`代替`==`，上面说不要用强制。因此，下面的每一行都返回`false`的期望值。

```
console.log('1' === 1); // false
console.log('' === 0); // false
console.log('1' === [1]); // false 
```

Enter fullscreen mode Exit fullscreen mode

建议大家使用`===`进行对比，大部分团队都会使用一个工具来保证。

# 那`var`呢？

您可能已经注意到，在我的任何例子中，我都没有使用`var`关键字。TypeScript 以及更高版本的 JavaScript 引入了两种声明变量(和常量)的新方法，分别是`let`和`const`。

需要注意的重要区别是，用`var`声明的对象是“函数范围的”，而用`let`声明的对象是“块范围的”。考虑这个例子。

```
for (var i=0; i < 100; i++) {
  if (i % 3 === 0) {
    console.log('FIZZ');
  } 
  if (i % 5 === 0) {
    console.log('BUZZ');
  }
  if (i % 5 && i % 3) {
    console.log(i)
  }
}

console.log(i); // 100 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，你可能认为`i`不合法，因为它被定义为`for`循环的一部分。但是因为`var`是函数作用域的，所以它被定义在它出现的函数中的任何地方，即使它被声明得非常深。

如果我们使用了`let`而不是`var`，生成的 JavaScript 将是相同的，但是 TypeScript 编译器会将在`console.log`中使用`i`标记为错误。建议在您的类型脚本代码中使用`let`而不是`var`。

另一个新关键字`const`，工作方式与`let`完全一样，定义的对象将是块范围的。然而，对于`const`，你必须提供一个值。此外，一旦声明，其值就永远不能更改。

```
const myName = 'Mike';
myName = 'Bob'; // Error - cannot redefine 
```

Enter fullscreen mode Exit fullscreen mode

此规则仅适用于命名对象，而不适用于其任何成员(在复杂对象或数组的情况下)。例如，您可以操作数组的成员。您还可以添加、更改或删除对象的属性。您不能重新分配它的值。

```
const mike: Member = { email: '1234@company.com', firstName: 'Mike', lastName: 'Smith' };
mike.email = 'mike';

// These are fine
const allMembers: Member[] = [];
allMembers.push(mike);

// Errors - may not redefine constants
allMembers = []; 
mike = { email: '2345@company.com', firstName: 'Bob', lastName: 'Johnson' }; 
```

Enter fullscreen mode Exit fullscreen mode

# 接下来呢？

目前就这些了，但是还有很多东西需要打字。欲了解更多信息，请访问[www.typescriptlang.org](http://www.typescriptlang.org)

# 反馈赞赏

你有什么意见或问题吗？我在这篇文章中犯了什么错误吗？在推特上告诉我。我是 [@walkingriver](https://twitter.com/walkingriver) 。

* * *

*交叉发布自 [WalkingRiver 博客](https://walkingriver.com/intro-to-typescript)T3】*
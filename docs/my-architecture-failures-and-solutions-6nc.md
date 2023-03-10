# 我的架构失败和解决方案

> 原文：<https://dev.to/whiteand/my-architecture-failures-and-solutions-6nc>

# 我的架构故障及解决方案

## 史前史

做前端开发一年了。在我的第一个项目中，有一个“敌人”后端。这在沟通建立的情况下，问题不大。

但在我们的案例中，情况并非如此。我们开发的代码依赖于后端发送给我们的特定结构和内容的特定数据。而后端在没有警告的情况下更改响应内容被认为是正常的。结果，我们花了几个小时试图找出网站的某个部分停止工作的原因。

我们意识到，在依赖后端发送给我们的数据之前，我们需要检查后端返回的内容。我们创建了一个任务来研究前端数据的验证。

这项研究是委托给我的。

我列了一个清单，列出了我想在工具中用来验证数据的内容。

最重要的选择点是以下项目:

*   验证的声明性描述(方案),它被转换成函数验证器，返回真/假(有效，无效)
*   准入门槛低；
*   验证数据与验证描述的相似性；
*   易于集成定制验证；
*   易于集成自定义错误消息。

结果，我通过查看前 5 名(ajv，joi，roi)找到了许多验证库...).都很好。但是在我看来，对于 5%的复杂案例的解决方案，他们注定了 95%的最常见的案例是相当冗长和繁琐的。

所以我想:为什么不开发一些适合我的东西呢？四个月后，我的验证库第七版[四重奏](https://www.npmjs.com/package/quartet)发布了。这是一个稳定的版本，经过全面测试，在 npm 上有 11k 的下载量。我们在一个为期三个月的活动中的三个项目中使用了它。

这三个月起到了非常有益的作用。[四重奏](https://www.npmjs.com/package/quartet)展现了它所有的优点。现在后端的数据没有任何问题。每当他们改变响应时，我们立即抛出一个错误。查找错误原因所花费的时间大大减少了。几乎没有数据错误了。

但是也有不利之处。

因此，我决定对它们进行分析，并发布一个新版本，更正开发过程中出现的所有错误。
关于这些架构错误及其解决方案将在下面讨论。

## 建筑耙

### “字符串”类型的模式语言

我将给出一个针对 person 对象的旧版本方案的例子。

```
const personSchema = {
  name: 'string',
  age: 'number',
  linkedin: ['string', 'null']
} 
```

Enter fullscreen mode Exit fullscreen mode

该方案验证具有三个属性的对象:name -必须是一个字符串，age -必须是一个数字，LinkedIn 上的帐户链接-必须是 null(如果没有帐户)或 string(如果有帐户)。

这个方案符合我对可读性、与验证数据的相似性的要求，我觉得学习写这类方案的入门门槛并不高。而且，这样的方案可以很容易地从 typescript 类型定义:
中编写出来

```
type Person = {
  name: string
  age: number
  linkedin: string | null
} 
```

Enter fullscreen mode Exit fullscreen mode

(正如我们所见，这些变化更多的是装饰性的)

当决定什么应该用于最频繁的验证时(例如，上面使用的那些)。我选择使用字符串，比如验证器名称。

但是字符串的问题是它们对于编译器或错误分析器是不可用的。它们的“number”字符串与“numder”没有太大区别。

### 解

我决定从 8.0.0 版本的 [quartet](https://www.npmjs.com/package/quartet) 中删除使用字符串作为模式中验证器的名称。

该方案现在看起来是这样的:

```
const personSchema = {
  name: v.string
  age: v.number,
  linkedin: [v.string, null]
} 
```

Enter fullscreen mode Exit fullscreen mode

这种变化有两大优势:

*   代码的编译器或静态分析器——将能够检测出方法名是否写错了。
*   字符串不再被用作该方案的一个元素。这意味着对于它们来说，可以在库中分配新的功能，这将在下面描述。

### 支持打字稿

总的来说，前七个版本是纯 JavaScript 开发的。当切换到 Typescript 项目时，有必要以某种方式对库进行调整。因此，编写了库的类型声明文件。

但是这是一个缺点——当添加功能时，或者当改变库的一些元素时，总是很容易忘记更新类型声明。

这种小麻烦也是有的

```
const checkPerson = v(personSchema) // (0)

// ...

const person: any = await axios.get('https://myapi.com/person/42')
if (!checkPerson(person)) { // (1)
  throw new TypeError('Invalid person response')
}
console.log(person.name) // (2) 
```

Enter fullscreen mode Exit fullscreen mode

当我们在第(0)行创建一个对象验证器时。我们希望在检查第(1)行后端的真实响应并处理错误后看到结果。在第(2)行中，`person`的类型为 Person。但这并没有发生。不幸的是，这样的检查不是类型保护。

### 解

我决定将整个 [quartet](https://www.npmjs.com/package/quartet) 库重写为 Typescript，以便编译器检查库的类型一致性。在这个过程中，我们添加了返回编译后的验证器的函数——一个类型参数，它将决定验证器是哪种类型的类型保护。

一个例子是这样的:

```
const checkPerson = v<Person>(personSchema) // (0)

// ...

const person: any = await axios.get('https://myapi.com/person/42')
if (!checkPerson(person)) {// (1)
  throw new TypeError('Invalid person response')
}
console.log(person.name) // (2) 
```

Enter fullscreen mode Exit fullscreen mode

现在在第(2)行上，`person`的类型是`Person`。

### 可读性

还有两种情况下代码读得不好:检查是否符合一组特定的值(检查 enum)和检查对象的其他属性。

最初有一个想法，在我看来是个好主意。我们将通过向我们的对象添加字段“sex”来演示它。旧版本的方案是这样的:

```
const personSchema = {
  name: 'string',
  age: 'number',
  linkedin: ['null', 'string'],
  sex: v.enum('male', 'female')
} 
```

Enter fullscreen mode Exit fullscreen mode

选项可读性很强。但和往常一样，一切都有点出乎意料。
在程序中声明枚举，例如:

```
enum Sex {
  Male = 'male',
  Female = 'female'
} 
```

Enter fullscreen mode Exit fullscreen mode

很自然，你想在计划中使用它。因此，如果其中一个值发生变化(例如，“男性”->“m”，“女性”->“f”)，验证方案也会发生变化。

因此，枚举验证几乎总是被记录为:

```
const personSchema = {
  name: 'string',
  age: 'number',
  linkedin: ['null', 'string'],
  sex: v.enum(...Object.values(Sex))
} 
```

Enter fullscreen mode Exit fullscreen mode

这看起来相当麻烦。

b)验证对象的其余属性

假设我们向我们的对象添加另一个特征——它可能有额外的字段，但所有这些字段都必须是到社交网络的链接——这意味着它们必须是`null`或者是一个字符串。

旧的方案应该是这样的:

```
 const personSchema = {
  name: 'string',
  age: 'number',
  linkedin: ['null', 'string'],
  sex: v.enum(...Object.values(Sex)),
  ... v.rest(['null', 'string']) // Rest props are string | null
} 
```

Enter fullscreen mode Exit fullscreen mode

这种描述方式概述了其余的属性-从那些已经列出。使用扩展操作符会使想理解这个方案的人感到困惑。

### 解

如上所述，字符串不再是验证方案的一部分。验证方案只有三种类型的 Javascript 值。对象-描述对象验证方案。要描述的数组-有效性的几个选项。函数(由库或自定义生成)-用于所有其他验证选项。

这一规定使我们有可能添加一些功能来增加方案的可读性。

实际上，如果我们想用字符串‘male’来比较值呢。除了值本身和' male '字符串之外，我们真的需要知道任何东西吗？

因此，决定添加基本类型的值作为模式的一个元素。因此，当您遇到方案中的原始值时，这意味着这是由该方案创建的验证器应该检查的有效值。让我给你举个例子:

如果我们需要检查数字是否等于 42，那么我们可以这样写:

```
const check42 = v(42)

check42(42) // => true
check42(41) // => false
check42(43) // => false
check42('42 ') // => false 
```

Enter fullscreen mode Exit fullscreen mode

让我们看看这是如何影响这个人的方案的(不考虑额外的属性):

```
const personSchema = {
  name: v.string,
  age: v.number,
  linkedin: [null, v.string], // null is primitive value
  sex: ['male', 'female'] // 'male', 'female' are primitive values
} 
```

Enter fullscreen mode Exit fullscreen mode

使用预定义的枚举，我们可以这样重写:

```
const personSchema = {
  name: v.string,
  age: v.number,
  linkedin: [null, v.string],
  sex: Object.values(Sex) // same as ['male', 'female']
} 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，通过使用 enum 方法并使用 spread-operator 将来自对象的有效值作为参数插入到该方法中，额外的礼仪性被删除了。

什么被认为是原始值:数字、字符串、字符、`true`、`false`、`null`和`undefined`。

也就是说，如果我们需要与它们比较值，我们只需使用这些值本身。验证库将创建一个验证器，该验证器将严格地将该值与模式中指定的值进行比较。

为了验证剩余属性，选择对对象的所有其他字段使用特殊属性:

```
const personSchema = {
  name: v.string,
  age: v.number,
  linkedin: [null, v.string],
  sex: Object.values(Sex),
  [v.rest]: [null, v.string]
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，该方案看起来更具可读性。更类似于 Typescript 中的定义。

### 验证器绑定到创建它的函数。

在旧版本中，错误解释不是验证器的一部分。它们在`v`函数中被折叠成一个数组。

以前，为了获得验证错误的解释，必须随身携带一个验证器(执行检查)和一个函数 v(获得无效性的解释)。它看起来像这样:

a)我们向模式添加解释。

```
const checkPerson = v({
  name: v('string', 'wrong name')
  age: v('number', 'wrong age'),
  linkedin: v(['null', 'string'], 'wrong linkedin'),
  sex: v(
    v.enum(...Object.values(Sex)),
    'wrong sex value'
  ),
  ... v.rest(
    v(
      ['null', 'string'],
      'wrong social networks link'
    )
  ) // Rest props are string | null
}) 
```

Enter fullscreen mode Exit fullscreen mode

对于模式的任何元素，您可以使用编译器函数 v 的第二个参数添加错误解释。

b)清除一系列解释。

在验证之前，有必要清除这个全局数组，其中记录了验证期间的所有解释。

```
v.clearContext() // same as v.explanations = [] 
```

Enter fullscreen mode Exit fullscreen mode

c)验证

```
const isPersonValid = checkPerson(person) 
```

Enter fullscreen mode Exit fullscreen mode

在这个检查过程中，如果发现了一个无效性，并且在创建方案的阶段——给出了一个解释，那么这个解释被放置在全局数组`v.explanation`中。

d)错误处理

```
if (!isPersonValid) {
  throw new TypeError('Invalid person response:' + v.explanation.join(';'))
} // ex. Throws ‘Invalid person response: wrong name; wrong age ’ 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，这是一个大问题。因为如果我们想要使用的验证器
不在它创建的地方，我们不仅需要传入参数，还要传入创建它的函数。因为在它里面有一个数组，解释将被折叠在其中。

### 解

这个问题是这样解决的:解释已经成为验证功能本身的一部分。从它的类型可以了解到什么:

```
type Validator = (value: any, explanations?: any []) => boolean 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果你需要一个错误的解释，你可以传递一个你想要添加解释的数组。

因此，验证器变成了一个独立的单元。还添加了一个方法，该方法可以将验证函数转换为一个函数，如果值有效，则该函数返回 null，如果值无效，则返回一个解释数组。

现在，带有解释的验证看起来像这样:

```
const checkPerson = v<Person>({
  name: v(v.string, 'wrong name'),
  age: v(v.number, 'wrong age'),
  linkedin: v([null, v.string], 'wrong linkedin')
  sex: v(Object.values(Sex), 'wrong sex')
  [v.rest]: v([null, v.string], 'wrong social network')
})

// ...
const explanations = []
if (!checkPerson(person, explanation)) {
  throw new TypeError('Wrong person:' + explanations.join(';'))
}

// OR

const getExplanation = v.explain(checkPerson)
const explanations = getExplanation(person)
if (explanations) {
  throw new TypeError('Wrong person:' + explanations.join(';'))
} 
```

Enter fullscreen mode Exit fullscreen mode

## 后记

我确定了三个先决条件，为此我必须重写所有内容:

*   希望人们在编写字符串时不会出错
*   使用全局变量(在本例中是 v.explanation 数组)
*   在开发过程中检查小例子——没有显示出在真正的大案例中使用时出现的问题。

但我很高兴我对这些问题进行了分析，发布的版本已经在我们的项目中使用。我希望它对我们的用处不亚于上一篇。

感谢大家的阅读，希望我的经验对你们有用。
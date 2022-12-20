# Typescript 的严格模式到底有多严格？

> 原文：<https://dev.to/briwa/how-strict-is-typescript-s-strict-mode-311a>

我开始用 Javascript 编写代码，但没有正确的输入。当我切换到 Typescript 时，我在没有打开严格模式的情况下迁移了我的代码，因为我知道类型重构太难处理了，我应该首先专注于通过单元测试。

即使没有严格模式，这也已经是一种范式的转变，因为与 Javascript 不同，你必须明确定义大多数东西。我以为我对我的类型已经够严格了。但是严格模式到底有多严格呢？

根据[文档](https://www.typescriptlang.org/docs/handbook/compiler-options.html)，当 Typescript strict 模式设置为 on 时，它将使用“strict”系列下的 strict 类型规则对项目中的所有文件验证您的代码。规则是:

*   `noImplicitAny`
*   `noImplicitThis`
*   `strictNullChecks`
*   `strictPropertyInitialization`
*   `strictBindCallApply`
*   `strictFunctionTypes`

这些是我在遇到这些规则时学到的一些经验。

## 1。`noImplicitAny`

该规则不允许变量或函数参数具有隐式`any`类型。考虑这个例子:

```
// Javascript/Typescript non-strict mode
function extractIds (list) {
  return list.map(member => member.id)
} 
```

看代码，`list`可以是*任何东西*。当然，从`.map`你会认为它是一个成员数组，`member`有一个名为`id`的属性，但是没有任何东西专门定义它。这就是为什么它在`strict`模式下是一个错误。

```
// Typescript strict mode
function extractIds (list) {
  //              ❌ ^^^^
  //                 Parameter 'list' implicitly has an 'any' type. ts(7006)
  return list.map(member => member.id)
  //           ❌ ^^^^^^
  //              Parameter 'member' implicitly has an 'any' type. ts(7006)
} 
```

固定版本将是:

```
// Typescript strict mode
interface Member {
  id: number
  name: string
}

function extractIds (list: Member[]) {
  return list.map(member => member.id)
} 
```

另一个你可能在野外看到的常见代码:

```
// Typescript strict mode
function onChangeCheckbox (e) {
  //                    ❌ ^
  //                       Parameter 'e' implicitly has an 'any' type. ts(7006)
  e.preventDefault()
  const value = e.target.checked
  validateCheckbox(value)
} 
```

您可以使用一些由 Typescript 本身定义的全局类型，在这种情况下，[特定于浏览器的类型](https://github.com/microsoft/TypeScript/blob/master/lib/lib.dom.d.ts) :

```
// Typescript strict mode
interface ChangeCheckboxEvent extends MouseEvent {
  target: HTMLInputElement
}

function onChangeCheckbox (e: ChangeCheckboxEvent) {
  e.preventDefault()
  const value = e.target.checked
  validateCheckbox(value)
} 
```

请注意，如果您导入没有类型定义的库，它也会抛出一个错误，因为这意味着导入的库有一个`any`类型。

```
// Typescript strict mode
import { Vector } from 'sylvester'
//                  ❌ ^^^^^^^^^^^
//                     Could not find a declaration file for module 'sylvester'.
//                     '/foo/node_modules/sylvester/lib/node-sylvester/index.js' implicitly has an 'any' type.
//                     Try `npm install @types/sylvester` if it exists
//                     or add a new declaration (.d.ts) file containing `declare module 'sylvester';` ts(7016) 
```

这可能是您项目中的一个瓶颈，因为如果没有类型定义，您可能不得不自己编写类型定义，但是定义所有的类型应该是正确的，至少在严格模式下是这样。

## 2。`noImplicitThis`

该规则不允许隐式定义`this`上下文。考虑这个例子:

```
// Javascript/Typescript non-strict mode
function uppercaseLabel () {
  return this.label.toUpperCase()
}

const config = {
  label: 'foo-config',
  uppercaseLabel
}

config.uppercaseLabel()
// FOO-CONFIG 
```

对于一直在编写 Javascript 的人来说，众所周知,`this`指的是`config`对象，所以`this.label`只是在检索`config.label`，这就是这段代码工作的原因。然而，在函数上引用`this`可能会产生歧义。

```
// Typescript strict mode
function uppercaseLabel () {
  return this.label.toUpperCase()
  //  ❌ ^^^^
  //     'this' implicitly has type 'any' because it does not have a type annotation. ts(2683)
} 
```

如果我们单独运行`uppercaseLabel`，它将抛出一个错误，因为`this`上下文不再位于`config`上，因此出现错误是因为`label`未定义。

解决这个问题的一个方法是避免在没有上下文的函数上使用`this`:

```
// Typescript strict mode
const config = {
  label: 'foo-config',
  uppercaseLabel () {
    return this.label.toUpperCase()
  }
} 
```

Typescript 甚至不会抱怨这一点，因为所有类型都被正确地推断出来。或者，更好的是，编写接口，这样所有类型现在都是定义的，而不是推断的。

```
// Typescript strict mode
interface MyConfig {
  label: string
  uppercaseLabel: (params: void) => string
}

const config: MyConfig = {
  label: 'foo-config',
  uppercaseLabel () {
    return this.label.toUpperCase()
  }
} 
```

## 3。`strictNullChecks`

该规则验证了值返回 null 或 undefined 的可能性。考虑这个例子:

```
// Javascript/Typescript non-strict mode
function getArticleMetaById (articles: Article[], id: string) {
  const article = articles.find(article => article.id === id)
  return article.meta
} 
```

现在，当然我会先在浏览器中检查代码，如果它能工作的话(事实也确实如此)。然而，在严格模式下，Typescript 会提醒我，当响应中没有一个 id 与给定的 id 匹配时，有可能 [`.find`会返回未定义的](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/find)。

```
// Typescript strict mode
function getArticleMetaById (articles: Article[], id: string) {
  const article = articles.find(article => article.id === id)
  return article.meta
  //  ❌ ^^^^^^^
  //     Object is possibly 'undefined'. ts(2532)
} 
```

这实际上拓宽了我的代码规范，所以现在我也必须实际处理错误情况，这应该在一开始就完成。

```
// Typescript strict mode
function getArticleMetaById (articles: Article[], id: string) {
  const article = articles.find(article => article.id === id)
  if (typeof article === 'undefined') {
    throw new Error(`Could not find an article with id: ${id}.`)
  }

  return article.meta
} 
```

## 4。`strictPropertyInitialization`

此规则验证类中要在构造函数内部初始化的属性，或者在构造之前已经定义的属性。考虑这个例子:

```
// Javascript
class Student {
  constructor (grade, lessons) {
    this.grade = grade
    this.lessons = lessons.filter(lesson => lesson.grade <= grade)
  }

  setRedoLessons (lessons) {
    this.redoLessons = lessons
  }
} 
```

使用 Typescript，可以正确定义所有类实例属性。

```
// Typescript non-strict mode
interface Lesson {
  title: string
  grade: number
}

class Student {
  private grade: number
  private lessons: Lesson[]
  private redoLessons: Lesson[]
  private greetingType: string
  constructor (grade: number, lessons: Lesson[]) {
    this.grade = grade
    this.lessons = lessons.filter(lesson => lesson.grade <= grade)
  }

  setRedoLessons (lessons: Lesson[]) {
    this.redoLessons = lessons
  }
} 
```

然而，在这一点上，你不能判断是否有一个属性没有在构造函数或者某个方法中定义。我不知道你是否注意到了，但是在之前的代码中，我偷偷放入了一个符合这种标准的属性。

```
// Typescript non-strict mode
interface Lesson {
  title: string
  grade: number
}

class Student {
  private grade: number
  private lessons: Lesson[]
  private redoLessons: Lesson[]
  private greetingType: string // 👀 This is undefined, not used and there's no error!
  constructor (grade: number, lessons: Lesson[]) {
    this.grade = grade
    this.lessons = lessons.filter(lesson => lesson.grade <= grade)
  }

  setRedoLessons (lessons: Lesson[]) {
    this.redoLessons = lessons
  }
} 
```

在严格模式下，它实际上会对所有未在构造函数中定义的未定义属性抛出错误。

```
// Typescript strict mode
interface Lesson {
  title: string
  grade: number
}

class Student {
  private grade: number
  private lessons: Lesson[]
  private redoLessons: Lesson[]
  //   ❌ ^^^^^^^^^^^
  //      Property 'redoLessons' has no initializer and is not definitely assigned in the constructor. ts(2564)
  private greetingType: string
  //   ❌ ^^^^^^^^^^^^
  //      Property 'greetingType' has no initializer and is not definitely assigned in the constructor. ts(2564)
  constructor (grade: number, lessons: Lesson[]) {
    this.grade = grade
    this.lessons = lessons.filter(lesson => lesson.grade <= grade)
  }

  setRedoLessons (lessons: Lesson[]) {
    this.redoLessons = lessons
  }
} 
```

这有助于您检查代码，并查看属性是否确实在构造函数之外的地方使用。如果是的话，你可以在上面加上一个`!`，然后简单地删除那些不是的。

```
// Typescript strict mode
interface Lesson {
  title: string
  grade: number
}

class Student {
  private grade: number
  private lessons: Lesson[]
  private redoLessons!: Lesson[]
  constructor (grade: number, lessons: Lesson[]) {
    this.grade = grade
    this.lessons = lessons.filter(lesson => lesson.grade <= grade)
  }

  setRedoLessons (lessons: Lesson[]) {
    this.redoLessons = lessons
  }
} 
```

然而，我建议要么将它设置为默认值，如果它没有在构造函数中定义，这是一个好的做法，否则它将永远是`undefined`，直到它被设置(除非这是故意的)。

## 5。`strictBindCallApply`

该规则验证函数中定义的`bind`、`call`或`apply`的用法。考虑这个例子:

```
// Typescript without strict mode
function sum (num1: number, num2: number) {
  return num1 + num2
}

sum.apply(null, [1, 2])
// 3 
```

可能犯了一个错误，认为`sum`可以接受两个以上的参数。当代码运行时，在您的环境(也许是浏览器)中，Typescript *和*不会抛出错误。

```
// Typescript non-strict mode
function sum (num1: number, num2: number) {
  return num1 + num2
}

sum.apply(null, [1, 2, 3])
// Still 3...? 
```

知道这是否是一个错误的唯一方法是手动测试代码或者在单元测试中测试代码。在严格模式下，你甚至可以在那之前就发现这一点:

```
// Typescript strict mode
function sum (num1: number, num2: number) {
  return num1 + num2
}

sum.apply(null, [1, 2, 3])
//           ❌ ^^^^^^^^^
//              Argument of type '[number, number, number]' is not assignable to parameter of type '[number, number]'.
//                Types of property 'length' are incompatible.
//                  Type '3' is not assignable to type '2'. ts(2345) 
```

那么这可能是重新考虑`sum`功能设计的好时机。

```
// Typescript strict mode
function sum (...args: number[]) {
  return args.reduce<number>((total, num) => total + num, 0)
}

sum.apply(null, [1, 2, 3])
// 6 
```

## ⑥。`strictFunctionTypes`

不幸的是，到目前为止，我还没有在我的代码中找到这个规则的用例，所以我不能对此发表太多评论。您可以随时在文档中查看 [`strictFunctionTypes`](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-6.html#strict-function-types) 的发布说明。如果有人有用例要分享，请告诉我！

* * *

如果你想把打字限制提高到一个不同的水平，我推荐使用 [`tslint`](https://palantir.github.io/tslint/) ，虽然我会说一些规则是基于偏好的，但有很多是有用的。或者，通过选择一个标准，如 [`gslint`](https://github.com/google/gts) 或 [`tslint-config-standard`](https://github.com/blakeembrey/tslint-config-standard) ，来避免自行车脱落。

希望这篇文章对你有用！感谢阅读。

* * *

封面图片由在 [Unsplash](https://unsplash.com/search/photos/rules?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上[标记的粗呢](https://unsplash.com/@2mduffel?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)组成。
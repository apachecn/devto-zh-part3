# 编写干净且可伸缩的 JavaScript 的 12 个技巧

> 原文：<https://dev.to/bnevilleoneill/12-tips-for-writing-clean-and-scalable-javascript-19p4>

[![](img/cb33b0a02bad34e9be2d455f51430ff4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6qDKoztp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AfubvatX332LA_AnVJ6z8ew.jpeg)

JavaScript 起源于早期的网络。它最初是一种脚本语言，现在已经发展成为一种完全成熟的编程语言，支持服务器端执行。

现代 web 应用程序非常依赖 JavaScript，尤其是单页面应用程序(SPAs)。随着 React、AngularJS 和 Vue.js 等新兴框架的出现，web 应用程序主要是用 JavaScript 构建的。

扩展这些应用程序——前端和后端一样——可能相当棘手。一个平庸的设置，你最终会遇到限制，迷失在混乱的海洋中。我想分享几个小技巧，帮助你以有效的方式编写干净的代码。

本文面向任何技能水平的 JavaScript 开发人员。然而，至少具备中级 JavaScript 知识的开发人员将从这些技巧中获益最多。

### 1。隔离您的代码

为了保持代码库的整洁和可读性，我能推荐的最重要的事情是将特定的逻辑块(通常是函数)按主题分开。如果你写一个函数，函数应该默认只有一个目的，不应该一次做多件事。

此外，您应该避免引起副作用，这意味着在大多数情况下，您不应该更改在函数之外声明的任何内容。您将数据接收到带参数的函数中；其他一切都不应该被访问。如果你想从函数中得到什么，返回新的值。

[![](img/18c623c22aecb029a54b76acf9d59712.png)](https://logrocket.com/signup/)

### 2。模块化

当然，如果这些函数以相似的方式使用或做相似的事情，您可以将多个函数分组到一个模块(和/或类，如果您愿意的话)中。例如，如果你有许多不同的计算要做，把它们分成可以链接的孤立的步骤(函数)。但是，这些函数都可以在一个文件(模块)中声明。下面是 JavaScript 中的例子:

```
function add(a, b) {
    return a + b   
}

function subtract(a, b) {
    return a - b   
}

module.exports = {
    add,
    subtract
}

const { add, subtract } = require('./calculations')

console.log(subtract(5, add(3, 2)) 
```

如果您正在编写前端 JavaScript，一定要对最重要的项目使用默认导出，对次要项目使用命名导出。

### 3。首选多个参数而不是单个对象参数

当声明一个函数时，你应该总是优先选择多个参数，而不是一个需要一个对象的参数:

```
// GOOD
function displayUser(firstName, lastName, age) {
    console.log(`This is ${firstName}  ${lastName}. She is ${age} years old.`)
}

// BAD
function displayUser(user) {
    console.log(`This is ${user.firstName}  ${user.lastName}. She is ${user.age} years old.`)
} 
```

这背后的原因是，当您查看函数声明的第一行时，您就确切地知道需要传递给函数什么。

尽管函数的大小应该受到限制——只做一项工作——但函数的大小可能会变得更大。在函数体中扫描需要传递的变量(嵌套在对象中)会花费你更多的时间。有时，使用整个对象并将其传递给函数似乎更容易，但要扩展应用程序，这种设置肯定会有所帮助。

在某种程度上，声明特定的参数是没有意义的。对我来说，就是四五个函数参数以上。如果你的函数变得那么大，你应该使用对象参数。

这里的主要原因是参数需要以特定的顺序传递。如果有可选参数，那么**需要**传递 undefined 或 null。使用对象参数，您可以简单地传递整个对象，其中顺序和未定义的值无关紧要。

### 4。解构

析构是 ES6 引入的一个很好的工具。它允许你从一个对象中获取特定的字段，并立即将其赋给一个变量。您可以将它用于任何类型的对象或模块。

```
// EXAMPLE FOR MODULES
const { add, subtract } = require('./calculations') 
```

只导入文件中需要使用的函数，而不是整个模块，然后从中访问特定的函数，这样做是有意义的。类似地，当你决定一定需要一个对象作为函数参数时，也要使用析构。这仍然会让您大致了解函数内部需要什么:

```
function logCountry({name, code, language, currency, population, continent}) {
    let msg = `The official language of ${name} `
    if(code) msg += `(${code}) `
    msg += `is ${language}. ${population} inhabitants pay in ${currency}.`
    if(contintent) msg += ` The country is located in ${continent}`
}

logCountry({
    name: 'Germany',
    code: 'DE',
    language 'german',
    currency: 'Euro',
    population: '82 Million',
})

logCountry({
    name: 'China',
    language 'mandarin',
    currency: 'Renminbi',
    population: '1.4 Billion',
    continent: 'Asia',
}) 
```

如您所见，我仍然知道我需要传递给函数什么——即使它被包装在一个对象中。要解决知道需要什么的问题，请看下一个技巧！

(顺便说一下，这也适用于 React 功能组件。)

### 5。使用默认值

析构甚至基本函数参数的默认值都非常有用。首先，它们给了你一个例子，告诉你可以传递给函数什么值。其次，您可以指出哪些值是必需的，哪些不是。使用前面的例子，该函数的完整设置如下所示:

```
function logCountry({
    name = 'United States', 
    code, 
    language = 'English', 
    currency = 'USD', 
    population = '327 Million', 
    continent,
}) {
    let msg = `The official language of ${name} `
    if(code) msg += `(${code}) `
    msg += `is ${language}. ${population} inhabitants pay in ${currency}.`
    if(contintent) msg += ` The country is located in ${continent}`
}

logCountry({
    name: 'Germany',
    code: 'DE',
    language 'german',
    currency: 'Euro',
    population: '82 Million',
})

logCountry({
    name: 'China',
    language 'mandarin',
    currency: 'Renminbi',
    population: '1.4 Billion',
    continent: 'Asia',
}) 
```

显然，有时您可能不想使用默认值，而是在没有传递值时抛出一个错误。然而，这通常是一个方便的技巧。

### 6。数据稀缺

前面的提示让我们得出一个结论:不要传递你不需要的数据。在这里，再次，这可能意味着更多的工作时，设置您的功能。然而，从长远来看，它肯定会给你一个可读性更好的代码库。确切地知道在特定地点使用哪些值是非常宝贵的。

### 7。线条和缩进限制

我见过大文件——非常大的文件。事实上，超过 3000 行代码。在这些文件中寻找大块的逻辑是非常困难的。

因此，您应该将文件大小限制在一定的行数内。我倾向于将我的文件保持在 100 行代码以下。有时，很难拆分文件，它们会增长到 200–300 行，在极少数情况下，会增长到 400 行。

超过这个阈值，文件就会变得非常混乱，很难维护。请随意创建新的模块和文件夹。您的项目应该看起来像一个森林，由树(模块部分)和分支(模块组和模块文件)组成。避免试图模仿阿尔卑斯山，在封闭的区域堆积代码。

相比之下，您的实际文件应该看起来像夏尔，在这里和那里有一些小山(小层次的缩进)，但一切都相对平坦。尽量把缩进量保持在四级以下。

也许对这些提示启用 eslint-rules 是有帮助的！

### 8。使用更漂亮的

在团队中工作需要清晰的风格指南和格式。ESLint 提供了一个庞大的规则集，您可以根据自己的需要进行定制。还有 eslint - fix，它纠正了一些错误，但不是全部。

相反，我推荐使用[更漂亮的](https://prettier.io/)来格式化你的代码。这样，开发人员就不必担心代码格式，而只需编写高质量的代码。外观将是一致的，并且格式是自动的。

### 9。使用有意义的变量名

理想情况下，变量应该根据其内容来命名。这里有一些指导方针，可以帮助你声明有意义的变量名。

#### 功能

函数通常执行某种动作。为了解释这一点，人类使用动词——例如，转换或显示。最好用动词开头来命名函数，例如 convertCurrency 或 displayUserName。

#### 数组

这些通常包含一个项目列表；因此，在变量名后面附加一个 s。例如:

```
const students = ['Eddie', 'Julia', 'Nathan', 'Theresa'] 
```

#### 布尔

简单地从是或必须接近自然语言开始。你会问这样的问题，“那个人是老师吗？”→“是”或“否”。类似地:

```
const isTeacher = true // OR false 
```

#### 数组函数

forEach、map、reduce、filter 等。是处理数组和执行一些动作的优秀的原生 JavaScript 函数。我看到很多人只是将 el 或 element 作为参数传递给回调函数。虽然这很简单快捷，但是您也应该根据它们的价值来命名它们。例如:

```
const cities = ['Berlin', 'San Francisco', 'Tel Aviv', 'Seoul']
cities.forEach(function(city) {
...
}) 
```

#### id

通常，您必须跟踪特定数据集和对象的 id。当 id 是嵌套的时，简单地将其保留为 id。在这里，我喜欢在将对象返回到前端之前，将 MongoDB _id 映射到简单的 id。从对象中提取 id 时，预先考虑对象的类型。例如:

```
const studentId = student.id
// OR
const { id: studentId } = student // destructuring with renaming 
```

该规则的一个例外是模型中的 MongoDB 引用。这里，简单地以引用的模型命名字段。这将在填充参考文档时保持事情清晰:

```
const StudentSchema = new Schema({
    teacher: {
        type: Schema.Types.ObjectId,
        ref: 'Teacher',
        required: true,
    },
    name: String,
    ...
}) 
```

### 10。尽可能使用异步/等待

就可读性而言，回调是最糟糕的——尤其是嵌套的时候。承诺是一个很好的改进，但是在我看来，async/await 具有最好的可读性。即使对于初学者，或者来自其他语言的人，这也会有很大帮助。然而，确保你理解它背后的概念，不要无意识地到处使用它。

### 11。模块导入顺序

正如我们在技巧 1 和 2 中看到的，将逻辑保持在正确的位置是可维护性的关键。同样，如何导入不同的模块可以减少文件中的混乱。当导入不同的模块时，我遵循一个简单的结构:

```
// 3rd party packages
import React from 'react'
import styled from 'styled-components'

// Stores
import Store from '~/Store'

// reusable components
import Button from '~/components/Button'

// utility functions
import { add, subtract } from '~/utils/calculate'

// submodules
import Intro from './Intro'
import Selector from './Selector' 
```

因为有更多类型的导入，所以我在这里使用 React 组件作为例子。您应该能够根据您的具体用例进行调整。

### 12。摆脱控制台

console.log 是一种很好的调试方式——非常简单、快速，并且完成了工作。显然，有更复杂的工具，但我认为每个开发人员仍然使用它。如果你忘记清理日志，你的主机最终会变得一团糟。然后，您实际上希望将日志保存在代码库中；例如，警告和错误。

为了解决这个问题，出于调试原因，您仍然可以使用 console.log，但是对于持久日志，请使用类似于 [loglevel](https://www.npmjs.com/package/loglevel) 或 [winston](https://www.npmjs.com/package/winston) 的库。此外，您可以使用 ESLint 对控制台语句发出警告。这样，您可以轻松地在全球范围内寻找控制台...并删除这些语句。

遵循这些指导方针确实帮助我保持了代码库的整洁和可伸缩性。有没有你觉得特别有用的小技巧？让我们在评论中知道你将在你的编码工作流程中包括什么，并请分享你用来帮助代码结构的任何其他技巧！

*关于作者:Lukas Gisder-Dubé作为 CTO 共同创立并领导了一家初创公司 1.5 年，建立了技术团队和架构。离开初创公司后，他在*[*【iron hack】*](https://medium.com/u/1ff093a3da32)*担任首席讲师，教授编程，现在正在柏林建立一家初创公司机构&咨询公司。查看*[*Dube . io*](https://dube.io)*了解更多。*

*就在最近，杜贝的团队推出了* [*Assetizr*](https://assetizr.com) *，可以在一瞬间调整大小、优化、裁剪各种图像。去吧* [*看看*](https://assetizr.com) *，免费的！*

[![](img/78120db85d6cd728e97a307f0cd1af12.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LKSocVJb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/200/1%2Ap-l0Cee1IHvX0RQkVTOceQ.png)

* * *

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

帖子[写干净的可伸缩的 JavaScript 的 12 个技巧](https://blog.logrocket.com/12-tips-for-writing-clean-and-scalable-javascript-3ffe30abfe20/)首先出现在[的博客](https://blog.logrocket.com)上。
# 用 date-fns 格式化和修改 JavaScript 日期

> 原文：<https://dev.to/marinamosti/-formatting-and-modifying-javascript-dates-with-date-fns-3df2>

JavaScript 中的日期操作似乎是一件令人望而生畏的事情。原生的 [`Date()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date) 对象对新手来说不是很友好，它需要大量的工作来完成格式化这样的简单任务。

谈到用于日期管理的 JavaScript 库，目前有两个大玩家。[时刻](https://momentjs.com/)和[日期——FNS](https://date-fns.org/)。

# 为什么是 date-fns？

两个库都完成了工作，它们允许你抓取一个`Date`对象并格式化，增加时间，减少时间，等等。然而，这两者之间有一个明显的核心区别，这可能是你未来项目的一个卖点。

Date-fns 是**模块化的**,这意味着您只导入您需要的东西，这防止了您最终打包的文件中的大量膨胀。这看起来没什么大不了的，但是导入整个 MomentJS 库只是为了有一个简单的格式化函数可能不是您想要的。

它也是**不可变的**，这意味着对于每个函数，你总是会得到一个*新的* `Date`对象。这使得调试变得轻而易举。

**文档**超级清晰简单，每个功能都有清晰的使用示例。你可以在这里查看:[日期-fns 文档](https://date-fns.org/docs/Getting-Started)。

# 入门

让我们直接将日期-fns 导入到一个初始项目中。

```
# If you use NPM
npm install date-fns

# If you use YARN
yarn add date-fns 
```

**注意:**如果你正在阅读这篇文章，并且不想使用包管理器，你可以用`<script src="http://cdn.date-fns.org/VERSION/date_fns.min.js"></script>`导入整个库，但是你会错过导入你想要/需要的模块。

另外，请记住，当通过`<script>`或从像`CodeSandbox`这样的在线编辑器使用 **date-fns** 时，不是像本文中那样直接调用函数，而是必须通过`dateFns`对象调用它。
例如:`dateFns.addDays()`

一旦将库添加到项目中，就可以开始导入 JavaScript 文件并开始导入函数了！

# 格式化

在处理 JavaScript 日期时，格式化是必不可少的，所以我们将首先解决它。

通过调用:
导入文件中的`format`函数

```
import { format } from 'date-fns'; 
```

或者如果你正在做一个 NodeJS 项目:

```
const { format } = require('date-fns'); 
```

**提示:**如果你还不知道，导入括号`{ format }`之间的位允许我们只将**`format`函数导入到你的文件中，所以你实际上只引入了这个文件将使用的函数！**

 **让我们创建一个新的日期:`const newYears = new Date(2019, 1, 1);`，这将给我们一个常量`newYears`，本地 JS 日期为 2019 年 1 月 1 日。

现在让我们将这个日期格式化为 MM/DD/YYYY 格式。

```
const newYears = new Date('2019/01/01');
const formattedDate = format(newYears, 'MM/DD/YYYY');
console.log(formattedDate); 
```

如果您运行这个并检查您的控制台输出，您将看到您正在获得格式化的字符串。`01/01/2019`

*等等，就这样？是的！日期-fns 使格式化超级简单！*

如果你想查看不同的格式选项，查看格式函数的官方文档以获得完整的参考。

# 添加区域设置

好了，格式化是一项非常简单的任务。但是 **date-fns** 也支持本地化格式化日期输出的简单方法。

让我们使用前面的例子，将其更改为法语地区，使用完整的月份名称格式。我们只需要`require`locale，并将其传递给 format 选项。对于一个选项对象，它需要一个可选的第三个参数。

```
const newYears = new Date('2019/01/01');
const frenchLocale = require('date-fns/locale/fr');

const formattedDate = format(newYears, 'MMMM DD, YYYY', { locale: frenchLocale });
console.log(formattedDate); 
```

太好了！现在的输出是`janvier 01, 2019`

# 加减

现在我们知道了如何格式化日期，让我们继续实际操作我们的初始日期。

在`date-fns`中，我们对每种操作类型都有一个函数:

*   [增加天数](https://date-fns.org/v1.30.1/docs/addDays)
*   [添加周数](https://date-fns.org/v1.30.1/docs/addWeeks)
*   [添加月份](https://date-fns.org/v1.30.1/docs/addMonths)
*   [子天数](https://date-fns.org/v1.30.1/docs/subDays)
*   [超重低音](https://date-fns.org/v1.30.1/docs/subWeeks)
*   [子月](https://date-fns.org/v1.30.1/docs/subMonths)

开始看出规律了吗？有相当多的加/减函数要使用。请务必阅读文档，查看您可以使用的所有功能！

让我们抓住之前的例子，并添加一年，这样我们就可以从 2020 年开始。

首先，开始将`addYears`函数添加到我们的导入语句中。

```
import { format, addYears } from 'date-fns'; 
```

现在我们已经可以访问这个函数了，我们可以给我们的基准日期添加一年，然后格式化输出。

```
let newYears = new Date('2019/01/01');
newYears = addYears(newYears, 1);

const formattedDate = format(newYears, 'MMMM DD, YYYY');
console.log(formattedDate); 
```

现在的输出应该是`January 01, 2020`

# 计算两个日期之间的天数

再来一杯吗？这个怎么样，我们计算一年的第一天和圣诞节之间的天数？

先加上`date-fns`函数`differenceInDays` :

```
import { format, addYears, differenceInDays } from 'date-fns'; 
```

我们现在只需要创建两个日期，并将它们传递给函数:

```
const firstDay = new Date('2019/01/01');
const christmas = new Date('2019/12/24');

const daysBetween = differenceInDays(christmas, firstDay);
console.log(daysBetween); // Outputs => 357 
```

# 结论

Date-fns 可能看起来令人生畏，但实际上是一种处理所有日期计算的超级简单和轻量级的方法！

**Bonus pro 提示:**需要现场测试其中一个 date-fns 功能？在浏览文档时启动你的开发工具，你可以通过`dateFns`对象在现场使用所有的功能。感谢[马林达 M.](https://twitter.com/BeerAndBlank) 指出这一点！**
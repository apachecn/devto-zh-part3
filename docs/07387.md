# NodeJS 日志记录变得简单

> 原文：<https://dev.to/itnext/nodejs-logging-made-easy-1f3k>

> 我现在有了一个闪亮的新博客。阅读这篇文章，了解 https://blog.goncharov.page/nodejs-logging-made-easy 的最新动态

对于您想要记录的每一个服务方法，您写了多少次`logger.info('ServiceName.methodName.')`和`logger.info('ServiceName.methodName -> done.')`？你希望它是自动化的，并在你的整个应用程序中保持相同的签名吗？如果是这样的话，我们非常相似，我们遭受同样的痛苦太多次了，现在我们终于可以尝试解决它。一起。女士们先生们，让我来介绍...[阶级记录者](https://github.com/aigoncharov/class-logger)！

## [的“为什么”](https://github.com/aigoncharov/class-logger)

工程师往往是完美主义者。完美主义者到了极点。我们喜欢简洁的抽象。我们喜欢干净的代码。我们在别人甚至看不懂的人工语言中看到了美。我们喜欢制造小型数字世界，按照我们设定的规则生活。我们喜欢所有这些，可能是因为我们非常懒。不，我们不怕工作，但是我们讨厌做任何可以自动化的工作。

只写了几千行日志代码，我们通常会想出某些模式，标准化我们想要记录的内容。然而，我们仍然必须手动应用这些模式。所以[类记录器](https://github.com/aigoncharov/class-logger)的核心思想是提供一种声明性的、高度可配置的标准化方法来记录类方法执行前后的消息。

## 快速启动

让我们立即投入运行，看看实际的代码是什么样的。

```
import { LogClass, Log } from 'class-logger'

@LogClass()
class ServiceCats {
  @Log()
  eat(food: string) {
    return 'purr'
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

该服务将记录三次:

*   创建时将一个参数列表传递给构造函数。
*   在执行`eat`之前有一个参数列表。
*   在执行完`eat`后，会显示一系列参数和结果。

用文字编码:

```
// Logs before the actual call to the constructor
// `ServiceCats.construct. Args: [].`
const serviceCats = new ServiceCats()

// Logs before the actual call to `eat`
// `ServiceCats.eat. Args: [milk].`
serviceCats.eat('milk')
// Logs after the actual call to `eat`
// `ServiceCats.eat -> done. Args: [milk]. Res: purr.` 
```

Enter fullscreen mode Exit fullscreen mode

[现场演示](https://stackblitz.com/edit/class-logger-demo-cats-quick-start)。

我们还能记录什么？以下是事件的完整列表:

*   班级建设前。
*   同步和异步之前的静态和非静态方法和功能属性。
*   经过同步和异步静态和非静态方法和功能属性。
*   同步和异步静态和非静态方法和函数属性的错误。

> 功能属性是分配给属性的箭头功能(`class ServiceCats { private meow = () => null }`)。

## 根据我们的需要调整它

到目前为止还不错，但我们已经承诺“可定制”，对不对？那么我们该如何调整它呢？

[类记录器](https://github.com/aigoncharov/class-logger)提供三层分层配置:

*   全球的
*   班级
*   方法

在每一次方法调用时，它们都被评估并从上到下合并在一起。有一些默认的全局配置，所以你可以不用任何配置就可以使用这个库。

### 全局配置

这是应用程序范围的配置。可以用`setConfig`调用进行设置。

```
import { setConfig } from 'class-logger'

setConfig({
  log: console.info,
}) 
```

Enter fullscreen mode Exit fullscreen mode

### 类配置

它对你的类的每个方法都有影响。它可以覆盖全局配置。

```
import { LogClass } from 'class-logger'

setConfig({
  log: console.info,
})

@LogClass({
  // It overrides global config for this service
  log: console.debug,
})
class ServiceCats {} 
```

Enter fullscreen mode Exit fullscreen mode

### 方法配置

它只影响方法本身。重写类配置，因此也重写全局配置。

```
import { LogClass } from 'class-logger'

setConfig({
  log: console.info,
})

@LogClass({
  // It overrides global config for this service
  log: console.debug,
})
class ServiceCats {
  private energy = 100

  @Log({
    // It overrides class config for this method only
    log: console.warn,
  })
  eat(food: string) {
    return 'purr'
  }

  // This method stil uses `console.debug` provided by class config
  sleep() {
    this.energy += 100
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

[现场演示](https://stackblitz.com/edit/class-logger-demo-hierarchical-config)

### 配置选项

好吧，我们已经学会了如何改变默认设置，但它不会伤害到覆盖有什么配置，是吧？

> 在这里你可以找到很多有用的配置覆盖。
> 
> [这里是 config 对象接口的链接，以防你的打字稿比英语说得好:](https://github.com/aigoncharov/class-logger#configuration-object)

配置对象具有以下属性:

#### 日志

这是一个实际记录最终格式化消息的函数。它用于记录这些事件:

*   班级建设前。
*   同步和异步之前的静态和非静态方法和功能属性。
*   经过同步和异步静态和非静态方法和功能属性。

默认:`console.log`

#### 日志错误

这是一个实际记录最终格式化错误消息的函数。它用来记录这唯一的事件:

*   同步和异步静态和非静态方法和函数属性的错误。

默认:`console.error`

#### 格式化程序

它是一个有两种方法的对象:`start`和`end`。它将日志数据格式化为最终的字符串。

`start`格式化这些事件的消息:

*   班级建设前。
*   同步和异步之前的静态和非静态方法和功能属性。

`end`格式化这些事件的消息:

*   经过同步和异步静态和非静态方法和功能属性。
*   同步和异步静态和非静态方法和函数属性的错误。

默认:`new ClassLoggerFormatterService()`

#### 包括

消息中应包含的内容的配置。

##### args

它可以是布尔值，也可以是对象。

如果是布尔值，它设置是否包含参数列表(还记得那个`Args: [milk]`？)分为两部分，开始(构造前和方法调用前)和结束(方法调用后，错误方法调用)，消息。

如果是对象，应该有两个布尔属性:`start`和`end`。`start`包括/排除开始消息的参数列表，`end`对结束消息也是如此。

默认:`true`

##### 构造

设置是否记录类构造的布尔标志。

默认:`true`

##### 结果

另一个布尔标志设置是否包括方法调用的返回值或由它抛出的错误。还记得`Res: purr`吗？如果您将此标志设置为`false`，将不会有`Res: purr`。

默认:`true`

##### 典型性

再次强调，要么是布尔值，要么是对象。如果你启用它，你的类实例的字符串表示将被添加到日志中。换句话说，如果您的类实例有一些属性，它们将被转换成 JSON 字符串并添加到日志消息中。

并非所有属性都将被添加。[类记录器](https://github.com/aigoncharov/class-logger)遵循以下逻辑:

*   获取实例的自身(非原型)属性。
    *   为什么？当你的原型动态变化时，这种情况很少见，因此记录它几乎没有任何意义。
*   删除任何具有`function`类型的文件。
    *   为什么？大多数时候，`function`属性只是不可变的箭头函数，用来代替常规的类方法来保存`this`上下文。用这些函数的字符串体来膨胀日志没有多大意义。
*   丢弃任何不是普通对象的对象。
    *   什么对象是素色的？如果一个对象的原型严格等于`Object.prototype`，那么`ClassLoggerFormatterService`认为该对象是一个普通对象。
    *   为什么？通常我们包含其他类的实例作为属性(注入它们作为依赖)。如果我们包含这些依赖项的字符串版本，我们的日志会变得非常庞大。
*   剩下的纤维。

```
class ServiceA {}

@LogClass({
  include: {
    classInstance: true,
  },
})
class Test {
  private serviceA = new ServiceA()
  private prop1 = 42
  private prop2 = { test: 42 }
  private method1 = () => null

  @Log()
  public method2() {
    return 42
  }
}

// Logs to the console before the class' construction:
// 'Test.construct. Args: []. Class instance: {"prop1":42,"prop2":{"test":42}}.'
const test = new Test()

// Logs to the console before the method call:
// 'Test.method2\. Args: []. Class instance: {"prop1":42,"prop2":{"test":42}}.'
test.method2()
// Logs to the console after the method call:
// 'Test.method2 -> done. Args: []. Class instance: {"prop1":42,"prop2":{"test":42}}. Res: 42.' 
```

Enter fullscreen mode Exit fullscreen mode

默认:`false`

## 控制格式

那么，如果你喜欢整体的想法，但希望你的信息看起来不同呢？您可以通过自己的自定义格式化程序来完全控制格式化。

您可以从头开始编写自己的格式化程序。完全同意。然而，我们不打算在这里讨论这个选项(如果您真的对此感兴趣，请阅读自述文件的[“格式化”部分](https://github.com/aigoncharov/class-logger#formatting))。

最快也可能是最简单的方法是继承一个内置的默认格式化程序- `ClassLoggerFormatterService`。

有这些受保护的方法，作为最终消息的构建块:

*   `base`
    *   返回带有方法名的类名。例子:`ServiceCats.eat`。
*   `operation`
    *   根据方法是成功执行还是错误执行，返回`-> done`或`-> error`。
*   `args`
    *   返回参数的字符串列表。例子:`. Args: [milk]`。它对引擎盖下的物体使用[快速安全纤维](https://github.com/davidmarkclements/fast-safe-stringify)。
*   `classInstance`
    *   返回一个字符串化的类实例。例子:`. Class instance: {"prop1":42,"prop2":{"test":42}}`。如果您选择包含类实例，但是它不可用(静态方法和类构造就是这样)，它返回`N/A`。
*   `result`
    *   返回执行的字符串化结果(即使它是一个错误)。使用 [fast-safe-stringify](https://github.com/davidmarkclements/fast-safe-stringify) 来序列化对象。字符串化错误将由以下属性组成:
    *   用(`error.constructor.name`)创建错误的类(函数)的名称。
    *   错误代码(`error.code`)。
    *   错误信息(`error.message`)。
    *   错误名称(`error.name`)。
    *   堆栈跟踪(`error.stack`)。
*   `final`
    *   返回`.`。就`.`。

`start`消息包括:

*   `base`
*   `args`
*   `classInstance`
*   `final`

`end`消息包括:

*   `base`
*   `operation`
*   `args`
*   `classInstance`
*   `result`
*   `final`

您可以覆盖这些构造块方法中的任何一个。让我们看看如何添加时间戳。我不是说我们应该。皮诺、[温斯顿](https://github.com/winstonjs/winston)和其他许多伐木工能够自己添加时间戳。所以这个例子纯粹是教育性的。

```
import {
  ClassLoggerFormatterService,
  IClassLoggerFormatterStartData,
  setConfig,
} from 'class-logger'

class ClassLoggerTimestampFormatterService extends ClassLoggerFormatterService {
  protected base(data: IClassLoggerFormatterStartData) {
    const baseSuper = super.base(data)
    const timestamp = Date.now()
    const baseWithTimestamp = `${timestamp}:${baseSuper}`
    return baseWithTimestamp
  }
}

setConfig({
  formatter: new ClassLoggerTimestampFormatterService(),
}) 
```

Enter fullscreen mode Exit fullscreen mode

[现场演示](https://stackblitz.com/edit/class-logger-demo-custom-formatter-add-timestamp)

## 结论

在你决定使用这个库之前，请不要忘记遵循[安装步骤](https://github.com/aigoncharov/class-logger#installation)并熟悉[要求](https://github.com/aigoncharov/class-logger#requirements)。

希望你已经找到了对你的项目有用的东西。请随时将您的反馈传达给我！我非常感谢任何批评和问题。
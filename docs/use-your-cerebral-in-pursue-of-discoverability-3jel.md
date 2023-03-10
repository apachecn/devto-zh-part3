# 用你的大脑去追求发现

> 原文：<https://dev.to/raimeyuu/use-your-cerebral-in-pursue-of-discoverability-3jel>

大脑使得编写真正的声明性和可读的代码成为可能。你可以用人类的口语和语法表达很多东西。但是大脑代码仍然会遭受高度的隐含性。在这篇短文中，我们将试图找到一个体面的解决 API 可发现性问题的方法(在大脑的意义上)。

如果你是大脑的新手，或者你没有看过我以前的文章，以下是列表:

*   [开动脑筋](https://medium.com/@raimeyuu/use-your-cerebral-625fa950ae74)
*   使用你的大脑——从命令式到陈述式
*   使用你的大脑——匹配你的模式！
*   [用你的大脑写一个游戏](https://medium.com/@dp.bomb/use-your-cerebral-writing-a-game-72b4307ac34d)

*注意*:所有的推理和代码都将围绕 brain 和 TypeScript 的使用。

# 上下文

你在为一家聊天公司工作，这家公司通过一个叫做“聊天”的平台将人们聚集在一起。您正在显示有多少人回答了特定用户的问题。

# 无聊的技术玩意儿...

假设我们的一个团队成员准备了一个大脑提供者，可以用来与 REST API 通信。他说有一个方便的操作器可以得到所有的答案，你只需要传递问题 id。

您立即打开了序列，当用户怀着极大的好奇心检查给出了多少答案时，该序列将被触发。然后，您导入提到的操作符并开始键入...

```
 // ... somewhere in the middle of the Cerebral's sequence ...
    getAnswers(props.questionId), {
      // What to put here...?
    } 
```

# 第一号问题

作为一个正派的程序员，你知道与 REST API 的通信是有效的操作，也可能失败。所以你可以有把握地假设有两条可能的路径(按照大脑的命名法)。你精力充沛地继续打字...

```
 // ... somewhere in the middle of the Cerebral's sequence ...
    getAnswers(props.questionId), {
        success: [],
        error: []
    } 
```

你问了你的队友关于路径的问题——只是为了确认一下(仔细检查一下是好的，对吗？).他证实——在使用操作符之后，需要将一个对象与 to 属性放在一起:一个用于成功路径，另一个用于失败路径。

你保存了文件，HMR 做了工作，你看...

# 运行时的噩梦

什么？例外？嗯，您仔细检查了操作符的用法是否正确，但是您找到了这段代码的作者...他说他忘记提到他使用“成功”和“失败”作为路径名。好的，那很好。没有团队惯例，为什么没有“失败”？

# 第二个问题

立刻，在得到双重确认:来自现实和来自你的队友，你改正了你可爱的代码:

```
 // ... somewhere in the middle of the Cerebral's sequence ...
    getAnswers(props.questionId), {
        success: [],
        failure: []
    } 
```

你使用神圣的 CTRL+S 组合键，HMR 默默地重新加载应用程序。没有运行时错误。你甚至和一个队友进行了简短的交谈，你让他相信没有明显的“失败”路径。你们都决定重构他的代码，并将路径嵌入到操作符的签名中。经过 20 分钟的重构，添加测试(当然！)你用这样的用法登陆:

```
 // ... somewhere in the middle of the Cerebral's sequence ...
    getAnswers(props.questionId, {
        success: [],
        failure: []
    }), 
```

现在，未来的任何人(包括来自未来的你自己)都不可能使用不正确的路径——它们被编码在操作符的签名中，TypeScript 编译器会委婉地提醒你这一点。太棒了。

# 第三个问题

但是等等。可能每条路径对大脑的`props`都有不同的贡献。“成功”路径是否在`props.response`下公开 HTTP 响应？当请求失败时,“失败”路径是否在`props.error`下添加 API 调用失败的原因和细节？我们的队友制定了一些新的约定，只有通过使用才能发现，这种可能性有多大？

可以说，让大脑调试器值班解决了大部分/所有提到的问题。当然，这种方法确实有效，但问题是——我们能做得更好吗？

# 操作员/操作可发现性问题

在 brain 中，你编写所有的序列、动作、操作符，就好像你描述了需要做什么，这很棒。在精神上，你必须记住执行上下文——通过`props`实际上可以得到什么。大脑调试器在这方面有所帮助，但有时在你让&运行并在调试器中可见之前，你需要玩一点设计来感受最佳方法。因为我个人喜欢大脑——哲学、概念等等——除了推理“常规”代码，你还必须推理执行上下文(`props`)

事实上，在 JS 中，可发现性甚至更难，因为 JS。当然，由于类型的原因，TypeScript 使它变得更容易，但这只对“常规”代码有帮助。我们能增加使用路径分支代码的操作符的可发现性吗？让我们看看典型运算符的结构。

# 我们的英雄- `getAnswers`

正如您可能已经看到的，在智能重构之后，这个操作符接受一个定义可用路径的标签和对象。通常，这种对象将路径名映射到下一个动作/序列。示例:

```
 // ... somewhere in the middle of the Cerebral's sequence ...
    getAnswers(props.questionId, {
        success: [
            showSuccessNotification,
            set(state.user.question.answers, props.result.response.answers),
            highlightTopRatedAnswer(state.user.question.answers)
        ],
        failure: [
            showFailureNotification(props.result.error.reason),
            sendError(props.result.error)
        ]
    }), 
```

所以“成功”路径暴露了`props.result.response`路径，“失败”路径暴露了`props.result.error`路径。在序列文件的顶部，您可能会看到这样的内容:

```
import { props as p } from "app.cerebral"

type ApiResult = {
    response: {
        answers: string[]
    },
    error: {
        reason: string,
        details: string
    }
}
const props = p as {
    questionId: Guid,
    result: ApiResult
} 
```

它给出了在一个序列中(事实上在不同的时刻)什么是/将是可用的一些概念！).我们可以尝试追踪那些特定属性在`props`可用的时刻，尤其是在与操作人员一起工作的时候。

# 一个人统治所有人的工具

操作员在里面会是什么样子？

```
import { sequence } from "cerebral"

const requestAnswersFor = (questionId: Tag) => ({ resolve, theChatAPI, path }) => 
    theChatAPI
        .getAnswers(resolve.value(questionId))
        .then((response) => path.success({ result: { response } })
        .catch((error) => path.failure({ result: { error } })

type Paths = {
    success: Action | Sequence,
    failure: Action | Sequence
}

const getAnswers = (questionId: Tag, paths: Paths) => sequence("get answers for given question", [
    requestAnswersFor(questionId), {
        success: paths.success,
        failure: paths.failure
    }
]) 
```

如您所见，这个操作符是一个将给定参数映射到序列的常规函数。在内部，它以典型的大脑语法对代码进行分支。对于每条路径，我们如何捕捉`props`中暴露的不同事物？

一个功能。就是这样。但是在哪里？怎么会？先说类型！

```
type ApiResult<T> = {
    result: T
}

type SuccessPathProps = {
    response: {
        answers: string[]
    }    
}

type FailurePathProps = {
    error: {
        reason: string,
        details: string
    }
}
type SuccessPath = (props: ApiResult<SuccessPathProps>) => Action | Sequence
type FailurePath = (props: ApiResult<FailurePathProps>) => Action | Sequence 
```

所以我们声明了一个助手`ApiResult<T>`类型。我们还让每条路径都有自己的“输出”道具类型。最后，我们使用前面提到的函数方法来捕获`props`中可用的不同对象。现在我们改变`Paths`的类型:

```
type Paths = {
    success: SuccessPath,
    failure: FailurePath
} 
```

现在需要改变`getAnswers`操作符，因为 TypeScript 委婉地提醒我们存在类型不匹配:

```
const failureProps = p as ApiResult<FailurePathProps>
const successProps = p as ApiResult<SuccessPathProps>

const getAnswers = (questionId: Tag, paths: Paths) => sequence("get answers for given question", [
    requestAnswersFor(questionId), {
        success: paths.success(successProps),
        failure: paths.failure(failureProps)
    }
]) 
```

所以我们不只是使用每个路径，例如`paths.success`，而是调用它，因为现在它是一个接受“局部”道具并返回动作或序列的函数。最后一波重构是`getAnswers`操作符的使用:

```
 // ... somewhere in the middle of the Cerebral's sequence ...
    getAnswers(props.questionId, {
        success: (successProps) => [
            showSuccessNotification,
            set(state.user.question.answers, successProps.result.response.answers),
            highlightTopRatedAnswer(state.user.question.answers)
        ],
        failure: (failureProps) => [
            showFailureNotification(failureProps.result.error.reason),
            sendError(failureProps.result.error)
        ]
    }), 
```

在这个层面上，重构过程归结为将序列转化为返回它们的函数。不错吧。

# 运行时异常反击！

常规的 CTRL+S 快捷键和 HMR 魔法...`YOU HAVE NOT ENABLED THE BABEL-PLUGIN-CEREBRAL`。等等，什么？！

大脑 5(版本 5)使用代理浏览器功能来实现流畅的标签使用(例如`props.result.response`)。事实上，它利用`babel-plugin-cerebral`将代理转换成模板标签文字(例如`props'result.response'`)。事实证明，如果没有《大脑》作者[克里斯蒂安·阿尔佛尼](https://twitter.com/christianalfoni)的帮助，我是不会解决这个问题的。Christian 在开源方面做得非常出色，总是在有任何疑问时提供帮助。你可以给他买一杯咖啡来表达你对他的支持。

这里的问题是代理属性的动态使用。通常当我们在大脑代码中使用代理时，我们只是访问一个“静态”使用的属性。这就是`babel-plugin-cerebral`的目标——代理的静态使用——它可以处理转换。当我们把一个`successProps`传递给一个`paths.success`函数，然后调用者访问它的属性时，插件不知道如何处理。所以这是它不起作用的根本原因。

# 勇敢的新世界

来自 [Christian](https://twitter.com/christianalfoni) 的解决方案是将代理的所有属性转移到一个对象的新实例中。代理属性的延迟使用是为了在闭包中捕获它。

```
type InputProps<T> = { [K in keyof T]: (() => T[K]) | InputProps<T[K]> }

function createOwnProps<T>(target: InputProps<T>) {
    function convert<T>(obj: InputProps<T>) {
        const newObj: any = {}
        for (let key in obj) {
            if (typeof obj[key] === "function") {
                Object.defineProperty(newObj, key, { get: obj[key] as any })
            } else {
                newObj[key] = convert(obj[key] as any)
            }

            return newObj
        }
    }
    return convert(target) as T
} 
```

看起来很吓人，其实里面的逻辑很简单。它将属性从给定的目标转移到新的对象，将目标的属性公开为 getter。现在，我们应该能够将我们的运算符实现转换为以下内容:

```
const failureProps = p as ApiResult<FailurePathProps>
const successProps = p as ApiResult<SuccessPathProps>

const wrappedSuccessProps = {
    result: {
        response: { answers: () => failureProps.result.response.answers }
    }
}

const wrappedFailureProps = {
    result: {
        error: { reason: () => failureProps.result.error.reason }
    }
}

const getAnswers = (questionId: Tag, paths: Paths) => sequence("get answers for given question", [
    requestAnswersFor(questionId), {
        success: paths.success(convertOwnProps(wrappedSuccessProps)),
        failure: paths.failure(convertOwnProps(wrappedFailureProps))
    }
]) 
```

唷。现在它看起来令人生畏。不幸的是，当考虑嵌套属性时，会涉及到一些样板文件。这里我们有两层嵌套(不包括`props`中的属性`result`)。在`props`中拥有更大更丰富的对象会导致代码很难阅读。因此，保持“local”(或“own”)`props`从一个小的操作符中暴露出来，并且嵌套一层是相当好的。

# 结论

我们发现了探索 API 可发现性的非常有趣的方法。完成实现的过程看起来有点吓人，但事实上我们能够在整个应用程序中重用`convertOwnProps`。在这种特殊情况下，运营商的 API 可读性和运营商的内部实现之间的权衡。值得注意的是，这不是一个银弹，而是一种探索大脑语法表达能力和浏览器功能的方式。为了平衡这种方法的使用，使用这种模式的开发人员需要问一些问题:

*   使用这种运算符的频率如何？
*   我们真的需要那种程度的可发现性吗？
*   为了传达运营商的意图和可能性而增加实施的复杂性真的有意义吗？

希望你喜欢挖掘大脑的整个过程。回头见！
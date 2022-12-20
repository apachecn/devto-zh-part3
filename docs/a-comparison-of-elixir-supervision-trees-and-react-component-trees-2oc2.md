# 药剂监督树和反应组分树的比较

> 原文：<https://dev.to/jackmarchant/a-comparison-of-elixir-supervision-trees-and-react-component-trees-2oc2>

[Elixir](https://elixir-lang.org/) 中的监督树与使用 React 的开发人员如何看待组件树有许多相似之处。在本文中，我将尝试描述两者之间的并行概念——如果您使用过 React 并且对函数式编程感兴趣，它可能会提示您看一看 Elixir。

在我们开始之前，你需要知道[监督树](https://elixir-lang.org/getting-started/mix-otp/supervisor-and-application.html#our-first-supervisor)不一定是从 Elixir 语言的发展中诞生的概念，而是被称为 [OTP](https://learnyousomeerlang.com/what-is-otp) (开放电信平台)的概念的一部分，由 [Erlang](https://www.erlang.org/) 语言的创造者创造。

希望我还没有失去你...看一下这张真实树的[图来刷新](https://americanheritagetrees.org/wp-content/uploads/2016/10/Forest.png)，然后再回来。

### 隔离失败

OTP 中的主要构件之一是隔离进程，以便它们独立地动作(和失败)。当一个新的进程在 Elixir 中产生时，通常用一个[监控器](https://hexdocs.pm/elixir/Supervisor.html)来监控它，这样如果发生错误，原因可以被记录下来或者发送到错误报告服务。在 React 中，我们可以在 React 组件树的概念模型中找到类似的情况，即(父)组件呈现其子组件之一，它可以用`componentDidCatch`捕捉错误，并类似地记录或发送错误报告。

### 消息/数据流

在 React 组件树中，数据流是单向的，从父节点到子节点(ren)。父组件也可以将函数作为道具传递，这将使子组件能够响应父组件。然后，父级可以通过设置新的状态来处理这个回调，因此，它可以重新呈现其子级。在 Elixir 监督树中，子进程可以链接到父进程，允许在发生某些事情时向父进程发送消息，例如，当进程完成它正在做的事情时。一个常见的场景可能是，一个流程可以产生一个[任务](https://hexdocs.pm/elixir/Task.html)，该任务在完成后可以(取决于它是如何产生的)向父流程发回一条消息，以便对其进行适当的处理。

### 具有树形结构的 Guaruntees

当我们考虑 UI 时，树结构是有意义的，这样我们可以预测地控制数据流经应用程序的方式，允许我们对组件做出某些保证。你可能听说过 React 被描述为“容易推理”。

Elixir 监督树也利用树结构来保证可用性和隔离性——作为 OTP 的一部分的关键概念。监督树隔离每个节点和一组节点，这样它既可以在出错时轻松恢复(重启进程-隔离故障)，又可以保持树中的其余节点不受系统故障的影响。你可以把它想象成一棵树上的树枝——当一棵树上的一根树枝死去时，它可以被剪掉，而树的其余部分会试图重新长出树枝。

类似地，在 React 组件树中，正如我前面提到的，可以用`componentDidCatch`生命周期方法捕获错误——并且有一天在树中的不同点用[钩子](https://reactjs.org/blog/2019/02/06/react-v16.8.0.html#whats-next)来阻止整个页面崩溃，使整个页面不可用。相反，树中只有一个分支或一组组件不能正确呈现，或者显示错误状态，但它使应用程序的其余部分继续工作，就像什么也没发生一样。

如果您仍然不知道为什么要在 Elixir 中使用监督树，或者它怎么可能与 UI 库相关联——对不起，这是我所知道的全部。

### 阅读更多仙丹文章

更多关于长生不老药的文章，请访问[jackmarchant.com](https://jackmarchant.com)
# 应用驱动的 API 设计

> 原文：<https://dev.to/smizell/application-driven-api-design-3o54>

在设计 API 时，采取设计优先的方法来确保构建正确的 API 是很重要的。但是，我认为第一步不应该是写一个 OpenAPI 或者 API 蓝图文档。我觉得应该是设计一个工作应用。

为了采用这种应用程序驱动的 API 设计方法，API 设计人员应该通过创建一个应用程序来开始 API 设计过程，该应用程序允许他们探索领域并尝试设计，而无需考虑数据存储、web 交互或架构。它产生了一个更好的设计，是初学者迈出的更好的一步。

# 这种方法的一个例子

假设我们被要求设计一个管理任务的 API。我们暂时保持简单。我们会说需求是提供一种管理有描述的任务的方法，以及一种将它们标记为完成的方法。我们需要根据任务的状态来筛选任务。我们不是从 API 描述开始，而是先用 Python 写一个小应用程序——任何语言都可以。

```
from dataclasses import dataclass

task_storage = []

@dataclass
class Task:
    description: str
    is_complete: bool = False

def mark_task_complete(task):
    task.is_done = True

def mark_task_incomplete(task):
    task.is_complete = False

def store_task(task):
    task_storage.append(task)

def all_tasks():
    return task_storage

def completed_tasks():
    return [task for task in task_storage if task.is_complete]

def incomplete_tasks():
    return [task for task in task_storage if not task.is_complete] 
```

Enter fullscreen mode Exit fullscreen mode

在这一点上，我们可以进入 [REPL](https://en.wikipedia.org/wiki/Read%E2%80%93eval%E2%80%93print_loop) 探索设计。感觉对吗？我们的词汇正确吗？我们是否遗漏了一些功能？通过这个，我们将看到哪些操作是安全的，哪些操作改变了状态，哪些是不安全的。这些都是可以推动 API 设计的有用知识。

还要看看我们在这一步没有做的所有事情。我们没有安装模拟服务器、JSON 模式验证器或文档工具。我们没有强迫自己在 URL 和 HTTP 的环境中思考。我们可以将这些细节推迟到以后，因为虽然它们很重要，但相对于应用程序设计来说，它们是次要的。

# 这对初学者有何影响

我们上面的代码足够简单，可以和完全不熟悉编程的人交流。我们将能够与初学者讨论问题和领域，而不必从与构建 HTTP API 相关的细节开始。一旦我们对我们的应用程序有了好的感觉，我们就可以开始问这样的问题:我们如何在 web 上持久存储数据或提供可用的功能。这是一个比 URL 和 HTTP 请求好得多的起点。

我有一种预感，有经验的 API 设计者在编写 API 定义时会在脑海中进行应用程序设计。他们已经经历了这个过程，并且使用了足够多的 API，知道什么有效，什么无效。然而，我发现首先编写应用程序来驱动 API 设计有很多好处。

# 这不是什么新鲜事

这种编写代码探索领域的过程并不新鲜。有一个名为[领域建模功能化](https://fsharpforfunandprofit.com/ddd/)的精彩演示，演示者讲述了他如何使用 F#和静态类型来定义领域。他总结道，“设计就是代码，代码就是设计。”他的观点是，你可以在代码中捕获领域，并使用 F#周围的工具来探索设计。

如果我们的第一步是定义和探索领域，我们可以对 API 设计做同样的事情。如果我们能在代码中做到这一点，我们就可以在考虑 API 细节之前对我们的应用程序进行一次测试。
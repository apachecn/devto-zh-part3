# 前端存储，状态在 elixir 后端。

> 原文：<https://dev.to/nerdslabs/frontend-store-with-the-state-on-the-elixir-backend-551e>

我想提出一个库，将前端存储移动到酏后端，命名为 [Stex](https://github.com/nerdslabs/stex/) 。整个通信通过 WebSocket 进行。

**重要提示:** Stex 正在积极开发中。在这里汇报问题和发送建议[。](https://github.com/nerdslabs/stex/issues/new)

目前，存储的整个状态在每次突变时都被发送，发送不同的状态是有计划的。

## 基本用法

### 安装

在`mix.exs` :
的 deps 中添加 **stex**

```
defp deps do
  [
    {:stex, git: "https://github.com/nerdslabs/stex"},
  ]
end 
```

您还需要将 **stex** 添加到`package.json`依赖项:

```
{
  "stex": "file:../deps/stex",
} 
```

### 添加 stex websocket 处理程序

您需要将处理程序`Stex.Socket.Handler`添加到牛仔调度中。

**凤凰:**
基于[凤凰向导](https://hexdocs.pm/phoenix/Phoenix.Endpoint.Cowboy2Adapter.html)
的例子

```
config :exampleapp, ExampleApp.Endpoint,
  http: [
    dispatch: [
      {:_,
       [
         {"/stex", Stex.Socket.Handler, []},
         {:_, Phoenix.Endpoint.Cowboy2Handler, {ExampleApp.Endpoint, []}}
       ]}
    ]
  ] 
```

**牛仔:**

```
:cowboy_router.compile([
    {:_, [
      # ...
      {"/stex", Stex.Socket.Handler, []},
      # ...
    ]}
  ]) 
```

### 创建商店

要创建一个商店，你需要创建一个带有`init/2`的新的 elixir 模块，它在页面加载时被调用，每次连接 websocket 时，它生成 session_id 并将其作为第一个参数传递，参数来自 Javascript 商店声明。接下来，您可以声明`mutation/5`，其中第一个参数是突变名称，第二个是传递给突变的数据，接下来的两个参数与`init/2`中的相同，最后一个是存储的当前状态。

```
defmodule ExampleApp.Store.Counter do
  use Stex.Store

  def init(session_id, params) do
    0
  end

  def mutation("increase", _data, _session_id, _params, state) do
    state = state + 1

    {:ok, state}
  end

  def mutation("decrease", _data, _session_id, _params, state) do
    state = state - 1

    {:ok, state}
  end

  def mutation("set", [number], _session_id, _params, state) do
    {:ok, number}
  end
end 
```

### 连接商店

您必须将新创建的存储与前端连接，以便能够同步状态:`params`在存储`init/2`中作为第二个参数传递，在`mutation/5`中作为第三个参数传递。您可以通过将 option `subscribe`和 function 作为一个值来订阅存储状态内部的更改。

```
import Stex from 'stex'

const store = new Stex({
  store: 'ExampleApp.Store.Counter',
  params: {},
  subscribe: () => {
    const state = store.state
  }
}) 
```

### 变异储存

您可以使用 store 实例:
从 javascript 中改变 store

```
store.mutate("increase")
store.mutate("set", 10) 
```

或者直接从仙丹:

```
Stex.mutate(session_id, store, "increase")
Stex.mutate(session_id, store, "set", [10]) 
```

### 订阅店铺状态变化

您可以使用函数 subscribe:
在 javascript 中订阅存储状态变化

```
store.subscribe(() => {
  const state = store.state
}) 
```

**更多信息请访问 [Github](https://github.com/nerdslabs/stex/) 。**
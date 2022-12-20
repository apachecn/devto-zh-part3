# 铸造🍃:处理 HTTP 请求

> 原文：<https://dev.to/gdotdesign/mint-handling-http-requests-2ep3>

*这是展示 Mint 特性的系列文章中的第四篇，你可以在这里找到之前的文章:*

*   *[薄荷🍃:入门](https://dev.to/gdotdesign/getting-started-with-mint-3k2o)T3】*
*   *[薄荷🍃:组件](https://dev.to/gdotdesign/components-in-mint-4a4n)*
*   *[薄荷🍃:组件的事件和状态](https://dev.to/gdotdesign/mint-events-and-state-of-components-3j3a)*

在这篇文章中，我将向你展示如何向一个 API 发出 HTTP 请求:)

## 代码

这是从[星球大战 API](https://swapi.co) 获取行星并显示在表格中的完整源代码。

```
record Planet {
  population : String,
  gravity : String,
  climate : String,
  name : String,
}

record Data {
  results : Array(Planet),
  count : Number,
}

enum Status {
  Initial
  Loading
  Error(String)
  Ok(Data)  
}

store StarWars {
  state status : Status = Status::Initial

  fun load : Promise(Never, Void) {
    sequence {
      next { status = Status::Loading }

      response =
        "https://swapi.co/api/planets"
        |> Http.get()
        |> Http.send()

      object = 
        response.body
        |> Json.parse()
        |> Maybe.toResult("")

      decodedResults = 
        decode object as Data

      next { status = Status::Ok(decodedResults) }
    } catch Http.ErrorResponse => error {
      next { status = Status::Error("Something went wrong with the request.") }
    } catch Object.Error => error {
      next { status = Status::Error("The data is not what is expected.") }
    } catch String => error {
      next { status = Status::Error("Invalid JSON data.") }
    }
  }
}

routes {
  * {
    StarWars.load() 
  }
}

component Main {
  connect StarWars exposing { status } 

  fun render : Html {
    case (status) {
      Status::Initial => <div></div>
      Status::Loading => <div>"Loading..."</div>
      Status::Error message => <div><{ message }></div>
      Status::Ok data =>
        <table>
          <tr>
            <th>"Name"</th>
            <th>"Climate"</th>
            <th>"Gravity"</th>
            <th>"Population"</th>
          </tr>

          for (planet of data.results) {
            <tr>
              <td><{ planet.name }></td>
              <td><{ planet.climate }></td>
              <td><{ planet.gravity }></td>
              <td><{ planet.population }></td>
            </tr>
          }
      </table>
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我现在一个一个地给你解释。

## 建模数据

在任何类型化的编程语言中，数据的结构必须以某种方式定义:

```
record Planet {
  population : String,
  gravity : String,
  climate : String,
  name : String,
}

record Data {
  results : Array(Planet),
  count : Number,
}

enum Status {
  Initial
  Loading
  Error(String)
  Ok(Data)  
} 
```

Enter fullscreen mode Exit fullscreen mode

在 Mint 中，有两种定义数据的结构:

*   `record` -用固定的命名键/值对定义一个对象
*   `enum` -定义了一个 [ADT](https://en.wikipedia.org/wiki/Algebraic_data_type) -一个代表一组固定可能性的类型

在我们的例子中，`Planet`和`Data`定义了来自 API 的数据，`Status`定义了请求的可能状态。

## 定义状态

在 Mint 中，全局状态存储在一个**存储库**(此处插入[尼古拉斯·凯奇迷因](https://vignette.wikia.nocookie.net/criminal-case-grimsborouimg/0/05/You_Don%27t_Say_Meme.png))中，这个存储库是全局可访问的，基本上就像一个与状态相关的组件一样工作。(`state`和`next`关键字[出自上一篇文章](https://dev.to(/gdotdesign/mint-events-and-state-of-components-3j3a)) )

```
store StarWars {
  state status : Status = Status::Initial

  fun load : Promise(Never, Void) {
    ...
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 处理请求

HTTP 请求的处理是在一个`sequence`块中完成的，该块按照顺序异步运行其中的每个表达式**([Cage 再一次](https://vignette.wikia.nocookie.net/criminal-case-grimsborouimg/0/05/You_Don%27t_Say_Meme.png))。**

 **这意味着它将**等待所有的承诺** `Promise(error, value)`并对变量中的`value`进行拆箱以备后用，或者引发在`catch`块中处理的`error`。

```
sequence {
  next { status = Status::Loading }

  response =
    "https://swapi.co/api/planets"
    |> Http.get()
    |> Http.send()

  object = 
    response.body
    |> Json.parse()
    |> Maybe.toResult("")

  decodedResults = 
    decode object as Data

  next { status = Status::Ok(decodedResults) }
} catch Http.ErrorResponse => error {
  next { status = Status::Error("Something went wrong with the request.") }
} catch Object.Error => error {
  next { status = Status::Error("The data is not what is expected.") }
} catch String => error {
  next { status = Status::Error("Invalid JSON data.") }
} 
```

Enter fullscreen mode Exit fullscreen mode

`Http`模块包含生成`Http.get(url : String)`和发送`Http.send(request : Http.Request)` HTTP 请求的函数。

下一步是将`JSON`内容解析成一个`Object`，然后将`decode`解析成我们之前定义的类型，然后根据发生的情况将`status`设置为`Status::Ok`或`Status::Error`。

## 路由

Mint 有一个处理路线的内置系统，将在另一篇文章中介绍。

在我们的例子中，我们定义了处理所有未处理的路由的`*`路由，在该路由中，我们只加载数据，这实际上意味着页面何时被加载:

```
routes {
  * {
    StarWars.load() 
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 显示数据

最后一部分是显示我们将在`Main`组件中执行的数据:

```
component Main {
  connect StarWars exposing { status } 

  fun render : Html {
    case (status) {
      Status::Initial => <div></div>
      Status::Loading => <div>"Loading..."</div>
      Status::Error message => <div><{ message }></div>
      Status::Ok data =>
        <table>
          <tr>
            <th>"Name"</th>
            <th>"Climate"</th>
            <th>"Gravity"</th>
            <th>"Population"</th>
          </tr>

          for (planet of data.results) {
            <tr>
              <td><{ planet.name }></td>
              <td><{ planet.climate }></td>
              <td><{ planet.gravity }></td>
              <td><{ planet.population }></td>
            </tr>
          }
      </table>
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

为了从存储中获取数据，首先我们需要使用关键字`connect`和状态`expose`将组件连接到它，这样就可以在组件的范围内使用它。

将一个组件连接到一个存储库，这样当存储库中的数据改变时，组件可以重新呈现。

然后基于`status`我们渲染不同的内容:

*   我们什么也不展示
*   我们显示一条装载信息
*   `Status::Error message` -我们显示错误信息
*   `Status::Ok data` -我们显示数据

好了，谢谢你的阅读🙏：

* * *

如果你想了解更多关于 Mint 的知识，请查看[指南](https://guide.mint-lang.com)📖

在下一部分，我将展示如何用 CSS 来[样式化元素😉在那里见👋](https://dev.to/gdotdesign/mint-styling-elements-295o)**
# 跳过框架:用 Hyper 构建一个简单的 Rust API

> 原文：<https://dev.to/deciduously/skip-the-framework-build-a-simple-rust-api-with-hyper-4jf5>

## [T1】简介](#intro)

在这篇文章中，我将使用 [hyper](https://github.com/hyperium/hyper) HTTP 库创建一个小的 web API。该应用是大卫·威克斯在他的帖子中介绍的 [todo-mvp](https://github.com/gypsydave5/todo-mvp) 的实现:

[![gypsydave5](img/ae3081c50d4c953e1125e67f5f086a97.png)](/gypsydave5) [## Todo-MVP:或者“为什么你不应该使用 Web 框架”——报复

### 大卫·威克斯 11 月 26 日 184 分钟阅读

#showdev #webdev #beginners #frameworks](/gypsydave5/todo-mvp-or-why-you-shouldnt-use-a-web-framework---the-revenge-261l)

编辑:我为 Rust 2021 重新实现了这个。你或许应该读一下的帖子[。](https://dev.to/deciduously/oops-i-did-it-againi-made-a-rust-web-api-and-it-was-not-that-difficult-3kk8)

项目的规定之一是每个实现都应该避免“框架”,只坚持库。框架是一个模糊的术语，不一定总是容易描述，所以我根据经验判断，如果板条箱文档将自己称为框架，它就不适合使用。这极大地缩小了可用工具的范围，但是事实证明`hyper`是构建这样一个没有太多复杂性的应用程序所需要的。

Hyper 是一个较低级别的 HTTP 实现。它提供了客户端和服务器类型，并公开了构建它的底层异步运行时。我们还会带来一些其他的板条箱，但仍然没有一个功能齐全的框架。

## 设置

你需要获得一个稳定的防锈工具链。如果需要，请参见 [rustup](https://rustup.rs/) 。安装完成后，旋转一个新的可执行项目:

```
$ cargo new simple-todo
$ cd simple-todo
$ cargo run
   Compiling simple-todo v0.1.0 (/home/ben/code/simple-todo)
    Finished dev [unoptimized + debuginfo] target(s) in 1.30s
     Running `target/debug/simple-todo`
Hello, world! 
```

Enter fullscreen mode Exit fullscreen mode

在您喜欢的编辑器中打开您的新`simple-todo`目录。在深入研究代码之前，让我们定义一下依赖关系。让你的`Cargo.toml`看起来像这样:

```
[package]
name = "simple-todo"
version = "0.1.0"
authors = ["You <you@yourcoolsite.com>"]
edition = "2018"

[dependencies]
futures = "0.1"
hyper = "0.12"
lazy_static = "1.3"
log = "0.4"
pretty_env_logger = "0.3"
serde = "1.0"
serde_derive = "1.0"
tera = "0.11"

[dependencies.uuid]
features = ["serde", "v4"]
version = "0.7" 
```

Enter fullscreen mode Exit fullscreen mode

除了`hyper`，我们还使用了几个额外的辅助板条箱。简而言之，`futures`提供零成本的异步编程原语，`lazy_static`将让我们定义需要运行时初始化的`static`(如`Vec::new()`)，`log`和`pretty_env_logger`提供日志记录，`serde`和`serde_derive`用于序列化，`tera`从类似 Jinja 的模板文件执行 HTML 模板化，`Uuid`提供 uuids！这些板条箱为我们提供了基本的建筑材料。

这是一个小程序，将完全在`main.rs`中定义。打开文件，从`cargo new`模板中删除`println!`语句，转而加速日志记录:

## 输入类型

```
fn main() {
    pretty_env_logger::init();
} 
```

Enter fullscreen mode Exit fullscreen mode

注意，在 Rust 2018 中我们可以省略`extern crate`声明，除非我们需要导入一个宏。

在设置服务器之前，我们需要一个地址来绑定。在这个演示中，我们将对它进行硬编码。在 init:
的正下方添加这一行

```
let addr = "127.0.0.1:3000".parse().unwrap(); 
```

Enter fullscreen mode Exit fullscreen mode

这个`parse()`方法将返回一个 [`std::net::SocketAddr`](https://doc.rust-lang.org/std/net/enum.SocketAddr.html) 。

接下来，我们需要在文件的顶部引入一些导入:

```
use futures::{future, Future, Stream};
use hyper::{
    client::HttpConnector, rt, service::service_fn, Body, Client, Request,
    Response, Server
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以结束`main()` :

```
 rt::run(future::lazy(move || {
        // create a Client for all Services
        let client = Client::new();

        // define a service containing the router function
        let new_service = move || {
            // Move a clone of Client into the service_fn
            let client = client.clone();
            service_fn(move |req| router(req, &client))
        };

        // Define the server - this is what the future_lazy() we're building will resolve to
        let server = Server::bind(&addr)
            .serve(new_service)
            .map_err(|e| eprintln!("Server error: {}", e));

        println!("Listening on http://{}", addr);
        server
    })); 
```

Enter fullscreen mode Exit fullscreen mode

这并不完全是类型检查——为了编译它，你可以为我们在`service_fn`调用中引用的`router`函数添加下面的存根:

```
fn router(req: Request<Body>, _client: &Client<HttpConnector>) -> Box<Future<Item = Response<Body>, Error = Box<dyn std::error::Error + Send + Sync>> + Send> {
    unimplemented!()
} 
```

Enter fullscreen mode Exit fullscreen mode

这都是一个小牛肉，让我们打开它。这整个花絮存在于对`rt:run()`的调用中。这里`rt`代表 runtime，指的是默认的 Tokio 运行时。我们的程序将立即开始运行并进入这个异步环境。

在内部，我们调用`future::lazy`，它接受一个闭包并返回一个将解析它的`Future`。定义的其余部分在这个闭包中，有几个步骤。我们构建了一个超级`Client`，能够发出 HTTP 请求。

下一步是创建一个`Service`。这是一个表示请求到响应的异步函数的特征——这正是我们的 web 服务器需要处理的！我们不用手工实现这个特征，我们只需要自己定义这个函数(在本例中，它是`router()`)，并使用`service_fn`助手将这个函数转换成`Service`。然后我们需要做的就是创建`Server`本身，它绑定到我们提供的地址，并让它服务于这个服务。

那就是*差不多吧*。现在我们的工作只是定义响应，不管有没有框架，这都是你的工作！

## 路由器

不过，首先来看看那个`router()`签名。恶心吧。在你的导入下创建几个类型别名:

```
type GenericError = Box<dyn std::error::Error + Send + Sync>;
type ResponseFuture = Box<Future<Item = Response<Body>, Error = GenericError> + Send>;

fn router(req: Request<Body>, _client: &Client<HttpConnector>) -> ResponseFuture {
    unimplemented!()
} 
```

Enter fullscreen mode Exit fullscreen mode

任何时候我们想要给一个连接一个响应，它必须以一个用`Box`包裹的`Future`包裹的`Response`的形式给出——这绝对是一个好主意，可以使它更容易键入！现在我们可以开始定义路线了。在开始之前，将`Body`、`Method`和`StatusCode`添加到`hyper`导入列表中。

我们可以利用 Rust 模式匹配来正确调度响应:

```
match (req.method(), req.uri().path()) {
    (&Method::GET, "/") | (&Method::GET, "index.html") => unimplemented!(),
    _ => four_oh_four(),
    } 
```

Enter fullscreen mode Exit fullscreen mode

我们同时匹配方法和路径——对“/”的 POST 请求不会匹配这个分支。我们可以根据应用程序的需要添加任意多的匹配分支，任何没有相应分支的传入请求都将得到`four_oh_four()`响应:

```
static NOTFOUND: &[u8] = b"Oops! Not Found";

fn four_oh_four() -> ResponseFuture {
    let body = Body::from(NOTFOUND);
    Box::new(future::ok(
        Response::builder()
            .status(StatusCode::NOT_FOUND)
            .body(body)
            .unwrap(),
    ))
} 
```

Enter fullscreen mode Exit fullscreen mode

正如所料，这个函数返回一个`ResponseFuture`。对于 404 页面，我们只使用这个静态值作为主体。`future::ok`返回一个立即解决的未来，我们使用构建器模式来构建一个`Response`。为了最大程度的正确，为像`StatusCode`这样的东西设置了`hyper`枚举！

## HTML

为了构建一个索引页面，我们将使用 [tera](https://github.com/Keats/tera) ，它提供了类似 Jinja2 的 HTML 模板。我们将需要一个宏，它将被设置为静态的，所以我们需要一些声明:

```
#[macro_use]
extern crate lazy_static;
#[macro_use]
extern crate tera;

// ...

use tera::{Context, Tera}; 
```

Enter fullscreen mode Exit fullscreen mode

`todo-mvp`项目要求每个实现使用相同的模板。这篇文章不是关于 Jinja2 或 HTML 的，所以我将指导你在这里下载[并保存到`simple-todo/templates/index.html`。你还需要保存](https://github.com/gypsydave5/todo-mvp/blob/master/todos/rust/templates/index.html) [`todo.css`](https://github.com/gypsydave5/todo-mvp/blob/master/todos/rust/src/resource/todo.css) 到`simple-todo/src/resource/todo.css`。

Tera 非常容易使用。添加以下代码片段:

```
lazy_static! {
    pub static ref TERA: Tera = compile_templates!("templates/**/*");
} 
```

Enter fullscreen mode Exit fullscreen mode

瞧，模板。现在我们可以写`index()` :

```
fn index() -> ResponseFuture {
    let mut ctx = Context::new();
    let body = Body::from(TERA.render("index.html", &ctx).unwrap().to_string());
    Box::new(future::ok(Response::new(body)))
} 
```

Enter fullscreen mode Exit fullscreen mode

要将数据注入 Tera 模板，需要将数据放入`tera::Context`中，并将模板路径和上下文传递给`render()`。然后我们把结果字符串放在一个`ResponseFuture`中！不要忘记更新`router()`中的匹配臂来调用该功能，而不是`unimplemented!()`。

## 状态

但是，有一个问题——我们实际上没有将任何数据放入上下文中！如果你运行这个程序，它会在加载这个模板时崩溃，抱怨在上下文中找不到`todos`和`todosLen`。这是一个令人难以置信的有效投诉，他们不在那里。

在像这样的异步应用程序中保持状态跟踪*可能*是一个复杂的问题，但这是 Rust。我们有 [`std::sync`](https://doc.rust-lang.org/std/sync/) 可以玩！具体来说，我们将使用 [`Arc`](https://doc.rust-lang.org/std/sync/struct.Arc.html) 和 [`RwLock`](https://doc.rust-lang.org/std/sync/struct.RwLock.html) 的组合来跨线程安全地存储我们的 todos，而无需真正考虑它。

一、导入附加物:

```
#[macro_use]
extern crate serde_derive;

// ...

use std::sync::{Arc, RwLock};
use uuid::Uuid; 
```

Enter fullscreen mode Exit fullscreen mode

现在，Todo 类型:

```
#[derive(Debug, Serialize)]
pub struct Todo {
    done: bool,
    name: String,
    id: Uuid,
}

impl Todo {
    fn new(name: &str) -> Self {
        Self {
            done: false,
            name: String::from(name),
            id: Uuid::new_v4(),
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

`new_v4()`方法将为任何新的`Todo`随机生成一个唯一的标识符。还要为所有待办事项列表添加一个新的类型别名:

```
type Todos = Arc<RwLock<Vec<Todo>>>; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以在`lazy_static!`块中实例化它:

```
lazy_static! {
    pub static ref TERA: Tera = compile_templates!("templates/**/*");
    pub static ref TODOS: Todos = Arc::new(RwLock::new(Vec::new()));
} 
```

Enter fullscreen mode Exit fullscreen mode

我们需要一些辅助函数来操作这个列表:

```
fn add_todo(t: Todo) {
    let todos = Arc::clone(&TODOS);
    let mut lock = todos.write().unwrap();
    lock.push(t);
}

fn remove_todo(id: Uuid) {
    let todos = Arc::clone(&TODOS);
    let mut lock = todos.write().unwrap();
    // find the index
    let mut idx = lock.len();
    for (i, todo) in lock.iter().enumerate() {
        if todo.id == id {
            idx = i;
        }
    }
    // remove that element if found
    if idx < lock.len() {
        lock.remove(idx);
    }
}

fn toggle_todo(id: Uuid) {
    let todos = Arc::clone(&TODOS);
    let mut lock = todos.write().unwrap();
    for todo in &mut *lock {
        if todo.id == id {
            todo.done = !todo.done;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

当您调用`Arc::clone()`时，它创建一个指向相同数据的新指针，增加其引用计数。然后我们在底层的`RwLock`上获得一个写锁，之后我们可以安全地操作内部的`Vec`。使用这些助手，我们的路由处理程序可以以一种保证线程安全的方式安全地操纵状态。最后，我们可以在定义了`ctx` :
之后，在`index()`中构建上下文

```
let todos = Arc::clone(&TODOS);
let lock = todos.read().unwrap();
ctx.insert("todos", &*lock);
ctx.insert("todosLen", &(*lock).len()); 
```

Enter fullscreen mode Exit fullscreen mode

## 经手人

现在运行应用程序，将浏览器指向`localhost:3000`应该会显示给定的 HTML (sans 样式表)。

应用程序的其余部分很容易。我们只需要填写其余的处理程序。例如，要加载丢失的样式表，需要一个新的匹配臂:

```
(&Method::GET, "/static/todo.css") => stylesheet(), 
```

Enter fullscreen mode Exit fullscreen mode

以及构建响应的函数:

```
fn stylesheet() -> ResponseFuture {
    let body = Body::from(include_str!("resource/todo.css"));
    Box::new(future::ok(
        Response::builder()
            .status(StatusCode::OK)
            .header(header::CONTENT_TYPE, "text/css")
            .body(body)
            .unwrap(),
    ))
} 
```

Enter fullscreen mode Exit fullscreen mode

里面没什么奇怪的！每个待办事项列表操作也有一个端点:

```
(&Method::POST, "/done") => toggle_todo_handler(req),
(&Method::POST, "/not-done") => toggle_todo_handler(req),
(&Method::POST, "/delete") => remove_todo_handler(req),
(&Method::POST, "/") => add_todo_handler(req), 
```

Enter fullscreen mode Exit fullscreen mode

这些处理程序都采用相同的格式:

```
fn add_todo_handler(req: Request<Body>) -> ResponseFuture {
    Box::new(
        req.into_body()
            .concat2() // concatenate all the chunks in the body
            .from_err() // like try! for Result, but for Futures
            .and_then(|whole_body| {
                let str_body = String::from_utf8(whole_body.to_vec()).unwrap();
                let words: Vec<&str> = str_body.split('=').collect();
                add_todo(Todo::new(words[1]));
                redirect_home()
            }),
    )
} 
```

Enter fullscreen mode Exit fullscreen mode

这个有点复杂。我们需要阅读请求，然后采取行动。在这种情况下，存储在`str_body`中的请求体看起来类似于`item=TodoName`。还有更健壮的解决方案，但是我只是在`=`上进行拆分，并在结果上调用`add_todo`函数来将其添加到列表中。然后我们重定向到 home，每次我们回到 home 的时候`index()`都会被调用，无论当前应用程序的状态如何，它都会重建 HTML！`toggle`和`remove`处理程序几乎是等价的，只是调用了正确的函数。

重定向也不奇怪:

```
fn redirect_home() -> ResponseFuture {
    Box::new(future::ok(
        Response::builder()
            .status(StatusCode::SEE_OTHER)
            .header(header::LOCATION, "/")
            .body(Body::from(""))
            .unwrap(),
    ))
} 
```

Enter fullscreen mode Exit fullscreen mode

这看起来就像你在任何工具包中写的东西。该应用程序还包括一些 SVG:

```
 (&Method::GET, path_str) => image(path_str), 
```

Enter fullscreen mode Exit fullscreen mode

```
fn image(path_str: &str) -> ResponseFuture {
    let path_buf = PathBuf::from(path_str);
    let file_name = path_buf.file_name().unwrap().to_str().unwrap();
    let ext = path_buf.extension().unwrap().to_str().unwrap();

    match ext {
        "svg" => {
            // build the response
            let body = {
                let xml = match file_name {
                    "check.svg" => include_str!("resource/check.svg"),
                    "plus.svg" => include_str!("resource/plus.svg"),
                    "trashcan.svg" => include_str!("resource/trashcan.svg"),
                    "x.svg" => include_str!("resource/x.svg"),
                    _ => "",
                };
                Body::from(xml)
            };
            Box::new(future::ok(
                Response::builder()
                    .status(StatusCode::OK)
                    .header(header::CONTENT_TYPE, "image/svg+xml")
                    .body(body)
                    .unwrap(),
            ))
        }
        _ => four_oh_four(),
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这就是全部的玉米卷饼。要添加更多的路线，您只需向`router()`添加一个新的匹配臂，并编写一个返回`ResponseFuture`的函数。这是一个坚实的、高性能的基础，可以很容易地以各种方式扩展，因为您不受制于任何特定的预定模式。总而言之，使用普通的`hyper`而不是更高级的框架来编写服务器，对于简单的用例来说，并没有减少太多的人类工程学，并且减少了应用程序的大量开销。我目前最喜欢的框架是`actix-web`，但是二进制文件有多大，冷编译要花多长时间，这几乎是荒谬的。当最终目标足够简单时，为什么不使用简单的工具呢？

完整的实现可以在[这里](https://github.com/gypsydave5/todo-mvp/tree/master/todos/rust/src)找到。
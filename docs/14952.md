# 网络应用程序信任+火箭

> 原文：<https://dev.to/mattdark/web-apps-con-rust--rocket-10af>

据其网站介绍， **[【火箭】](https://rocket.rs/)** 是一个拉斯特网络框架，使得编写快速安全的 web 应用程序变得简单，而又不牺牲灵活性、可用性和强类型。

最新版本为 12 月 6 日发布的 0.4，即拉斯特 2018 发布之日，确保了与最新版本的语言兼容。火箭和拉斯特夜猫子一起工作。

## 配置

*   如未安装，则以 **rustup** 夜间安装拉斯特。

```
$ rustup install nightly 
```

Enter fullscreen mode Exit fullscreen mode

*   以**职称**创建新项目。

```
$ cargo new hello_rocket 
```

Enter fullscreen mode Exit fullscreen mode

*   将 Nightly 指定给创建的项目。

```
$ cd hello_rocket
$ rustup override set nightly 
```

Enter fullscreen mode Exit fullscreen mode

## 目录结构

火箭开发项目的目录结构如下:

*   科学研究委员会
*   静电
*   模板
*   Cargo.toml

静态是保存图像、 **JavaScript** 文件和样式表( **CSS** 的目录。

HTML 模板保存在**templates**目录中。

必须创建这些目录。

## 模板

火箭支援两种模板系统，**[【tera】](https://github.com/Keats/tera)**，灵感来自**[【jinja 2】](http://jinja.pocoo.org/)**和[【django 模板语言】](https://docs.djangoproject.com/en/1.9/topics/templates/)

## 世界！

□用火箭创造第一个榜样的时刻！

### Cargo.toml

将火箭作为依赖项添加到文件**加载。toml** 。

```
[package]
name = "hello_rocket"
version = "0.1.0"
authors = ["mattdark"]
edition = '2018'

[dependencies]
rocket = "0.4" 
```

Enter fullscreen mode Exit fullscreen mode

### src/main.rs

编辑文件 **main.rs** 中的 **src** ，并放置显示文本**的代码，世界！**在浏览器中。

```
#![feature(proc_macro_hygiene, decl_macro)]
#[macro_use] extern crate rocket;

#[get("/")]
fn index() -> &'static str {
    "¡Hola, mundo!"
}

fn main() {
    rocket::ignite().mount("/", routes![index]).launch();
} 
```

Enter fullscreen mode Exit fullscreen mode

`#![feature]`行指示拉斯特将使用 Nightly 提供的编译器功能。

使用“`#[macro_use] extern crate rocket;`”将火箭尾部及其所有宏导入命名空间。

声明函数`index`将在浏览器中显示文本，并在主函数`main`中启动应用服务器。

### 启动申请

执行以下命令以启动应用程序服务器。

```
$ cargo run 
```

Enter fullscreen mode Exit fullscreen mode

以上命令根据文件**charge . toml**中的信息，负责下载项目依赖项，执行编译，生成相应的二进制文件，并启动服务器。

如果没有错误，终端将显示以下内容。

```
🔧 Configured for development.
    => address: localhost
    => port: 8000
    => log: normal
    => workers: 4
    => secret key: generated
    => limits: forms = 32KiB
    => keep-alive: 5s
    => tls: disabled
🛰  Mounting /:
    => GET / (index)
🚀 Rocket has launched from http://localhost:8000 
```

Enter fullscreen mode Exit fullscreen mode

从浏览器的地址栏中可以访问“[”http://localhost:8000](http://localhost:8000)，并应显示以下内容。
[![Hola mundo](img/2698e54e29504789fd5fd0b4c5e146cc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JjzirVpo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1rsyolclpvihu1gxub5m.png)

## 路由

```
#[get("/world")] {            // <- route attribute
fn world() -> &'static str {  // <- request handler
    "¡Hola, mundo!"
} 
```

Enter fullscreen mode Exit fullscreen mode

上述代码中声明的功能`world`属于请求处理程序，并与路由`/world`相关联，后者将接收类型为`GET`的请求。

## 挂载

宣告路径之后，请指定要接收的要求类型，并宣告处理要求的角色，如下所示。

```
fn main() {
    rocket::ignite.mount("/hello", routes![world]).launch();
} 
```

Enter fullscreen mode Exit fullscreen mode

以上创造了具有`world`功能的火箭的新实例，并在`/hello`方向组装了`world`路径。`GET`至`/hello/world`的请求将转呈`world`功能。应用服务器以功能`launch`启动。

服务器启动后，从地址栏访问[http://localhost:8000/hello/world](http://localhost:8000/hello/world)，并显示以下文本。
[![Hola mundo](img/2698e54e29504789fd5fd0b4c5e146cc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JjzirVpo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1rsyolclpvihu1gxub5m.png)

`hello`和`index`路径未声明，因此如果尝试访问 [http://localhost:8000](http://localhost:8000) 和[http://localhost:8000/hello](http://localhost:8000/hello)，将显示以下内容。
[![404](img/64acd1cc42fc9e7fac288944ace8b3ba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ILF3pFpP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r4njclu34u8di2eysekr.png)

如有必要，这些路径及其相应的处理程序应在代码中声明，并使用宏“`routes`”进行装配。

## 梅托多斯

Rocket 支持的方法如下:

*   得到
*   放
*   邮政
*   删除
*   头
*   修补
*   选择

## 动态方向

```
...
use rocket::http::RawStr;

#[get("/hello/<name>")]
fn hello(name: &RawStr) -> String {
    format("¡Hola, {}!", name.as_str())
} 
```

Enter fullscreen mode Exit fullscreen mode

一条路径可以接收参数，如上面的代码所示，其中路径“`hello`”接收参数“`<name>`”，该参数存储在相应的请求处理程序“`hello`”变量中，然后作为要在浏览器中显示的文本的一部分进行打印。

&RawStr 是一种火箭数据类型，因此必须告诉拉斯特将与线路`use rocket::http::RawStr`一起使用。

浏览“`/hello/Mario`”时，浏览器中将显示以下内容。
[![Hola Mario](img/9cfbebc8feb0167465698da2124cb98a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hp17DpVo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k8y1b9sqh8dn5kgui68i.png)

## 静态文件

在火箭的目录结构中， **src** 目录是保存图像、 **JavaScript** 文件和样式表( **CSS** 的位置。

```
...
use std::path::{Path, PathBuf};
use rocket::response::NamedFile;
...
#[get("/<file..>")]
fn files(file: PathBuf) -> Option<NamedFile> {
     NamedFile::open(Path::new("static/").join(file)).ok()
}

fn main() {
    rocket::ignite().mount("/", routes![index, files]).launch();
} 
```

Enter fullscreen mode Exit fullscreen mode

任何请求`GET`打开发送到`/`的文件，将在参数`<file..>`中接收，并由功能`files`处理。

`NamedFile::open(Path::new("static/").join(file)).ok()`行用于告诉拉斯特静态文件所在的目录。

使用新声明的路径，必须将该路径添加到宏“`routes`”才能正确安装。

## 模板

火箭支持**【tera】**和**【handle bars】**作为模板系统。

### Cargo.toml

在文件**加载。toml** 中添加以下内容，告诉他将使用**。如果使用 **Tera** ，则仅用`tera`代替`handlebars`。** 

```
...
handlebars = "1.1.0"

[dependencies.rocket_contrib]
version = "0.4"
features = ["handlebars_templates"] 
```

Enter fullscreen mode Exit fullscreen mode

### src/main.rs

一旦指明使用任何模板系统所需的依赖关系，文件 **main.rs** 将被修改为 **src** 。

```
...
extern crate rocket_contrib;
use rocket_contrib::templates::{template, handlebars};

#[get("/")]
fn index() -> Template {
    Template::render("index")
}
...
fn main() {
    rocket::ignite().mount("/", routes![index, files])
    .attach(Template::fairing())
    .launch();
} 
```

Enter fullscreen mode Exit fullscreen mode

首先进口**【rocket _ contib】**及其所有宏，行`extern crate rocket_contrib`。下面是将与`use rocket_contrib::templates::{template, handlebars};`行一起使用的模板系统。

导入模板时，使用函数“`render`”并指定函数返回的数据类型，在本例中为“`Template`”。

为了确保模板中间件(fairing，在 Rocket 中称为 fairing)附加到应用程序中，在主要功能中添加了行“`.attach(Template::fairing())`”。

模板应放在**【模板】**目录中，名称应与`Template::render("index")`中所示的名称相匹配。从上述代码中推断出模板名称为`index.html.hbs`。

在本文中，我概述了如何使用 Rocket 创建 web 应用程序。我希望这些信息有用。在下一篇文章中，我将展示一个使用此框架开发的应用程序示例。**
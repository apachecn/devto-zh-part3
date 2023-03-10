# 实用 Rust Web 开发- API Rest

> 原文：<https://dev.to/werner/practical-rust-web-development-api-rest-29g1>

更新:根据这个[问题](https://github.com/diesel-rs/diesel/issues/399) async 不能和 Diesel 一起工作，所以，来自`web::get`的方法`to_async`可能不能像预期的那样工作，它会工作但是不是你想要的方式，所以，老实说你可以把它改成`to`。

这是展示如何使用 Rust 进行 web 开发的一系列博客文章中的第一篇，我试图尽可能实际地使用已经为这项工作选择的工具。

我将从基础开始，直到我们可以创建基本的 API Rest 端点，从一个虚构的 web 商店中列出、创建、编辑和删除产品。

我会一步一步来，不过，在了解一点关于 Rust 语言之前，查看一下 Rust 的书是个好主意。

我们需要做的第一件事是安装 Rust，我们可以去[https://www.rust-lang.org/tools/install](https://www.rust-lang.org/tools/install)按照说明操作，因为我用的是 linux，示例代码取自那个操作系统，不过你可以在 Windows 或 Mac 上试试。

在终端中执行下一步，并按照指令执行:`curl https://sh.rustup.rs -sSf | sh`

你可以通过运行`rustc -V`来验证 Rust 是否正确安装，它会显示你安装的 rustc 版本。

接下来我们要做的是创建一个新项目，我们可以称之为 mystore，在终端窗口中运行下一个:`cargo new mystore --bin`。

如果一切正常，我们将能够看到一个以 mystore 命名的文件夹，我们可以看到 Rust 项目的基本结构:

[![tree-mystore](img/51bad46d07fe5c27831bc1a6d68a8586.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3_KryHlG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/56Gjg3R/tree-mystore.jpg)

接下来我们需要一个 web 框架，我们将使用 actix-web，这是一个基于 actix 的高级框架，actor 框架。在`cargo.toml` :
中添加下一行代码

```
[dependencies]
actix = "0.8"
actix-web = "1.0.0-beta" 
```

现在，当您执行`cargo build`时，箱子将被安装，项目将被编译。

我们将从 hello world 示例开始，在`src/main.rs` :
中添加下一行代码

```
extern crate actix_web;
use actix_web::{HttpServer, App, web, HttpRequest, HttpResponse};

// Here is the handler, 
// we are returning a json response with an ok status 
// that contains the text Hello World
fn index(_req: HttpRequest) -> HttpResponse  {
    HttpResponse::Ok().json("Hello world!")
}

fn main() {
    // We are creating an Application instance and 
    // register the request handler with a route and a resource 
    // that creates a specific path, then the application instance 
    // can be used with HttpServer to listen for incoming connections.
    HttpServer::new(|| App::new().service(
             web::resource("/").route(web::get().to_async(index))))
        .bind("127.0.0.1:8088")
        .unwrap()
        .run();
} 
```

在一个终端中执行`cargo run`，然后进入 [http://localhost:8088/](http://localhost:8088/) 查看结果，如果你能看到 Hello world 这个文本！在浏览器中，一切都按预期运行。

现在，我们将选择数据库驱动程序，在这种情况下将是[柴油](http://diesel.rs/)，我们在`Cargo.toml` :
中添加一个依赖项

```
[dependencies]
diesel = { version = "1.0.0", features = ["postgres"] }
dotenv = "0.9.0" 
```

如果我们执行`cargo build`，箱子将被安装，项目将被编译。

最好也安装 cli 工具，`cargo install diesel_cli`。

如果你在安装 diesel_cli 时遇到问题，可能是因为缺少数据库驱动程序，所以，确保包含它们，如果你使用 Ubuntu，你可能需要安装`postgresql-server-dev-all`。

执行 bash 中的下一个命令:

```
$ echo DATABASE_URL=postgres://postgres:@localhost/mystore > .env 
```

现在，diesel 已经为设置数据库做好了一切准备，运行`diesel setup`创建数据库。如果你在配置 postgres 时遇到问题，看看这个[指南](https://www.cyberciti.biz/faq/postgresql-remote-access-or-connection)。

让我们创建一个表来处理产品:

```
diesel migration generate create_products 
```

Diesel CLI 将创建两个迁移文件，`up.sql`和`down.sql`。

`up.sql` :

```
CREATE TABLE products (
  id SERIAL PRIMARY KEY,
  name VARCHAR NOT NULL,
  stock FLOAT NOT NULL,
  price INTEGER --representing cents
) 
```

`down.sql` :

```
DROP TABLE products 
```

应用迁移:

```
diesel migration run 
```

我们将在`main.rs`中加载我们需要的库:

`src/main.rs` :

```
#[macro_use]
extern crate diesel;
extern crate dotenv; 
```

接下来，我们创建一个文件来处理数据库连接，我们将其命名为 db_connection.rb，并将其保存在 src 中。

`src/db_connection.rs` :

```
use diesel::prelude::*;
use diesel::pg::PgConnection;
use dotenv::dotenv;
use std::env;

pub fn establish_connection() -> PgConnection {
    dotenv().ok(); // This will load our .env file.

    // Load the DATABASE_URL env variable into database_url, in case of error
    // it will through a message "DATABASE_URL must be set"
    let database_url = env::var("DATABASE_URL")
        .expect("DATABASE_URL must be set");

    // Load the configuration in a postgres connection, 
    // the ampersand(&) means we're taking a reference for the variable. 
    // The function you need to call will tell you if you have to pass a
    // reference or a value, borrow it or not.
    PgConnection::establish(&database_url)
        .expect(&format!("Error connecting to {}", database_url))
} 
```

接下来，我们将创建我们的第一个资源。产品清单。

我们需要的第一件事是几个结构，一个用于创建资源，另一个用于获取资源，在本例中是产品。

我们可以将它们保存在一个名为 models 的文件夹中，但是在此之前，我们需要一种方法来加载我们的文件，我们在`main.rs`中添加下面几行:

`src/main.rs` :

```
pub mod schema;
pub mod models;
pub mod db_connection; 
```

我们需要在 models 文件夹中创建一个文件，名为`mod.rs`:

`src/models/mod.rs` :

```
pub mod product; 
```

`src/models/product.rs` :

```
use crate::schema::products;

#[derive(Queryable)]
pub struct Product {
    pub id: i32,
    pub name: String,
    pub stock: f64,
    pub price: Option<i32> // For a value that can be null, 
                           // in Rust is an Option type that 
                           // will be None when the db value is null
}

#[derive(Insertable)]
#[table_name="products"]
pub struct NewProduct {
    pub name: Option<String>,
    pub stock: Option<f64>,
    pub price: Option<i32>
} 
```

因此，让我们添加一些代码来获取产品列表，我们将创建一个新的结构来处理名为 ProductList 的产品列表，并添加一个函数列表来从数据库中获取产品，将下一个块添加到`models/product.rs` :

```
// This will tell the compiler that the struct will be serialized and 
// deserialized, we need to install serde to make it work.
#[derive(Serialize, Deserialize)] 
pub struct ProductList(pub Vec<Product>);

impl ProductList {
    pub fn list() -> Self {
        // These four statements can be placed in the top, or here, your call.
        use diesel::RunQueryDsl;
        use diesel::QueryDsl;
        use crate::schema::products::dsl::*;
        use crate::db_connection::establish_connection;

        let connection = establish_connection();

        let result = 
            products
                .limit(10)
                .load::<Product>(&connection)
                .expect("Error loading products");

        // We return a value by leaving it without a comma
        ProductList(result)
    }
} 
```

我这样做是为了让我们可以自由地向那个结构添加任何特征，我们不能对向量这样做，因为我们不拥有它，ProductList 在 Rust 中使用了 newtype 模式。

现在，我们只需要一个句柄来响应对产品列表的请求，我们将使用`serde`将数据序列化为 json 响应。

我们需要编辑`Cargo.toml`、`main.rs`和`models/product.rs`:

`Cargo.toml` :

```
serde = "1.0"
serde_derive = "1.0"
serde_json = "1.0" 
```

`main.rs` :

```
pub mod handlers; // This goes to the top to load the next handlers module 

extern crate serde;
extern crate serde_json;
#[macro_use] 
extern crate serde_derive; 
```

`src/models/product.rs` :

```
#[derive(Queryable, Serialize, Deserialize)]
pub struct Product {
    pub id: i32,
    pub name: String,
    pub stock: f64,
    pub price: Option<i32>
} 
```

在`src/handlers` :
中添加一个名为`mod.rs`的文件

```
pub mod products; 
```

我们可以在 handlers 文件夹中创建一个名为`products.rs`的文件:

`src/handlers/products.rs` :

```
use actix_web::{HttpRequest, HttpResponse };

use crate::models::product::ProductList;

// This is calling the list method on ProductList and 
// serializing it to a json response
pub fn index(_req: HttpRequest) -> HttpResponse {
    HttpResponse::Ok().json(ProductList::list())
} 
```

我们需要在`main.rs`中为我们的服务器添加索引处理程序，以拥有 Rest API 的第一部分，更新文件，使其看起来像这样:

`src/main.rs` :

```
pub mod schema;
pub mod db_connection;
pub mod models;
pub mod handlers;

#[macro_use]
extern crate diesel;
extern crate dotenv;
extern crate serde;
extern crate serde_json;
#[macro_use] 
extern crate serde_derive;

extern crate actix;
extern crate actix_web;
extern crate futures;
use actix_web::{App, HttpServer, web};

fn main() {
    let sys = actix::System::new("mystore");

    HttpServer::new(
    || App::new()
        .service(
            web::resource("/products")
                .route(web::get().to_async(handlers::products::index))
        ))
    .bind("127.0.0.1:8088").unwrap()
    .start();

    println!("Started http server: 127.0.0.1:8088");
    let _ = sys.run();
} 
```

让我们添加一些数据，看看在终端运行时会是什么样子:

```
psql -U postgres -d mystore -c "INSERT INTO products(name, stock, price) VALUES ('shoes', 10.0, 100); INSERT INTO products(name, stock, price) VALUES ('hats', 5.0, 50);" 
```

然后执行:

```
cargo run 
```

最后转到[http://localhost:8088/products](http://localhost:8088/products)。

如果一切正常，您应该在 json 值中看到几个产品。

## 创建产品

将`Deserialize` trait 添加到`NewProduct` struct 和一个函数来创建产品:

```
#[derive(Insertable, Deserialize)]
#[table_name="products"]
pub struct NewProduct {
    pub name: String,
    pub stock: f64,
    pub price: Option<i32>
}

impl NewProduct {

    // Take a look at the method definition, I'm borrowing self, 
    // just for fun remove the & after writing the handler and 
    // take a look at the error, to make it work we would need to use into_inner (https://actix.rs/api/actix-web/stable/actix_web/struct.Json.html#method.into_inner)
    // which points to the inner value of the Json request.
    pub fn create(&self) -> Result<Product, diesel::result::Error> {
        use diesel::RunQueryDsl;
        use crate::db_connection::establish_connection;

        let connection = establish_connection();
        diesel::insert_into(products::table)
            .values(self)
            .get_result(&connection)
    }
} 
```

然后添加一个处理程序来创建产品:

```
use crate::models::product::NewProduct;
use actix_web::web;

pub fn create(new_product: web::Json<NewProduct>) -> Result<HttpResponse, HttpResponse> {

    // we call the method create from NewProduct and map an ok status response when
    // everything works, but map the error from diesel error 
    // to an internal server error when something fails.
    new_product
        .create()
        .map(|product| HttpResponse::Ok().json(product))
        .map_err(|e| {
            HttpResponse::InternalServerError().json(e.to_string())
        })
} 
```

最后添加相应的路由并启动服务器:

`src/main.rs` :

```
 HttpServer::new(
    || App::new()
        .service(
            web::resource("/products")
                .route(web::get().to_async(handlers::products::index))
                .route(web::post().to_async(handlers::products::create))
        ))
    .bind("127.0.0.1:8088").unwrap()
    .start(); 
```

```
cargo run 
```

我们可以创造一种新产品:

```
curl http://127.0.0.1:8088/products \                                                                                                
        -H "Content-Type: application/json" \
        -d '{"name": "socks", "stock": 7, "price": 2}' 
```

## 展示一个产品

`src/models/product.rs` :

```
impl Product {
    pub fn find(id: &i32) -> Result<Product, diesel::result::Error> {
        use diesel::QueryDsl;
        use diesel::RunQueryDsl;
        use crate::db_connection::establish_connection;

        let connection = establish_connection();

        products::table.find(id).first(&connection)
    }
} 
```

`src/handlers/products.rs` :

```
 use crate::models::product::Product;

pub fn show(id: web::Path<i32>) -> Result<HttpResponse, HttpResponse> {
    Product::find(&id)
        .map(|product| HttpResponse::Ok().json(product))
        .map_err(|e| {
            HttpResponse::InternalServerError().json(e.to_string())
        })
} 
```

`src/main.rs` :

```
 HttpServer::new(
    || App::new()
        .service(
            web::resource("/products")
                .route(web::get().to_async(handlers::products::index))
                .route(web::post().to_async(handlers::products::create))
        )
        .service(
            web::resource("/products/{id}")
                .route(web::get().to_async(handlers::products::show))
        )
    )
    .bind("127.0.0.1:8088").unwrap()
    .start(); 
```

```
cargo run 
```

如果一切正常，您应该会在[http://127 . 0 . 0 . 1:8088/products/1](http://127.0.0.1:8088/products/1)中看到一只鞋

## 删除一个产品

向产品模型添加新方法:

`src/models/product.rs` :

```
impl Product {
    pub fn find(id: &i32) -> Result<Product, diesel::result::Error> {
        use diesel::QueryDsl;
        use diesel::RunQueryDsl;
        use crate::db_connection::establish_connection;

        let connection = establish_connection();

        products::table.find(id).first(&connection)
    }

    pub fn destroy(id: &i32) -> Result<(), diesel::result::Error> {
        use diesel::QueryDsl;
        use diesel::RunQueryDsl;
        use crate::schema::products::dsl;
        use crate::db_connection::establish_connection;

        let connection = establish_connection();

        // Take a look at the question mark at the end, 
        // it's a syntax sugar that allows you to match 
        // the return type to the one in the method signature return, 
        // as long as it is the same error type, it works for Result and Option.
        diesel::delete(dsl::products.find(id)).execute(&connection)?;
        Ok(())
    }
} 
```

`src/handlers/products.rs` :

```
 pub fn destroy(id: web::Path<i32>) -> Result<HttpResponse, HttpResponse> {
    Product::destroy(&id)
        .map(|_| HttpResponse::Ok().json(()))
        .map_err(|e| {
            HttpResponse::InternalServerError().json(e.to_string())
        })
} 
```

`src/main.rs` :

```
 HttpServer::new(
    || App::new()
        .service(
            web::resource("/products")
                .route(web::get().to_async(handlers::products::index))
                .route(web::post().to_async(handlers::products::create))
        )
        .service(
            web::resource("/products/{id}")
                .route(web::get().to_async(handlers::products::show))
                .route(web::delete().to_async(handlers::products::destroy))
        )
    )
    .bind("127.0.0.1:8088").unwrap()
    .start(); 
```

```
cargo run 
```

我们来删除一只鞋:

```
curl -X DELETE http://127.0.0.1:8088/products/1 \
        -H "Content-Type: application/json" 
```

你应该不会在[http://127 . 0 . 0 . 1:8088/products](http://127.0.0.1:8088/products)中看到鞋子

## 更新产品

将 AsChangeset 特征添加到 NewProduct 中，这样就可以将该结构直接传递给 update，否则就需要指定要更新的每个字段。

`src/models/product.rs` :

```
#[derive(Insertable, Deserialize, AsChangeset)]
#[table_name="products"]
pub struct NewProduct {
    pub name: Option<String>,
    pub stock: Option<f64>,
    pub price: Option<i32>
}

impl Product {
    pub fn find(id: &i32) -> Result<Product, diesel::result::Error> {
        use diesel::QueryDsl;
        use diesel::RunQueryDsl;
        use crate::db_connection::establish_connection;

        let connection = establish_connection();

        products::table.find(id).first(&connection)
    }

    pub fn destroy(id: &i32) -> Result<(), diesel::result::Error> {
        use diesel::QueryDsl;
        use diesel::RunQueryDsl;
        use crate::schema::products::dsl;
        use crate::db_connection::establish_connection;

        let connection = establish_connection();

        diesel::delete(dsl::products.find(id)).execute(&connection)?;
        Ok(())
    }

    pub fn update(id: &i32, new_product: &NewProduct) -> Result<(), diesel::result::Error> {
        use diesel::QueryDsl;
        use diesel::RunQueryDsl;
        use crate::schema::products::dsl;
        use crate::db_connection::establish_connection;

        let connection = establish_connection();

        diesel::update(dsl::products.find(id))
            .set(new_product)
            .execute(&connection)?;
        Ok(())
    }
} 
```

`src/handlers/product.rs` :

```
pub fn update(id: web::Path<i32>, new_product: web::Json<NewProduct>) -> Result<HttpResponse, HttpResponse> {
    Product::update(&id, &new_product)
        .map(|_| HttpResponse::Ok().json(()))
        .map_err(|e| {
            HttpResponse::InternalServerError().json(e.to_string())
        })
} 
```

`src/main.rs` :

```
 HttpServer::new(
    || App::new()
        .service(
            web::resource("/products")
                .route(web::get().to_async(handlers::products::index))
                .route(web::post().to_async(handlers::products::create))
        )
        .service(
            web::resource("/products/{id}")
                .route(web::get().to_async(handlers::products::show))
                .route(web::delete().to_async(handlers::products::destroy))
                .route(web::patch().to_async(handlers::products::update))
        )
    )
    .bind("127.0.0.1:8088").unwrap()
    .start(); 
```

```
cargo run 
```

现在，让我们给产品添加库存:

```
curl -X PATCH http://127.0.0.1:8088/products/3 \
        -H "Content-Type: application/json" \
        -d '{"stock": 8}' 
```

你现在应该有 8 只袜子:[http://127 . 0 . 0 . 1:8088/products/3](http://127.0.0.1:8088/products/3)。

在这里看看完整的源代码[。](https://github.com/werner/practical-rust-web/tree/v1)

Rust 不是最简单的编程语言，但是它的好处克服了这些问题，Rust 允许你长期编写高性能和高效的应用程序。
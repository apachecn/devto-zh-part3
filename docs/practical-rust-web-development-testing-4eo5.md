# 实用锈网开发-测试

> 原文：<https://dev.to/werner/practical-rust-web-development-testing-4eo5>

测试是应用程序最重要的部分之一，mystore 也不例外。Actix web 提供了一些我们可以用来执行单元和集成测试的工具。

我将讨论集成测试，因为在我看来，集成测试更有用，它们通过模拟用户行为从输入到输出。我们应该尽可能地避免嘲笑，因为这样我们可以确定我们的代码将在类似的环境中工作。

我们将从一个新文件开始存储我们的数据库配置，我们可能会将它用于其他事情。

`tests/common/db_connection.rs` :

```
use diesel::pg::PgConnection;
use diesel::r2d2::{ Pool, ConnectionManager, PoolError };

pub type PgPool = Pool<ConnectionManager<PgConnection>>;

fn init_pool(database_url: &str) -> Result<PgPool, PoolError> {
    let manager = ConnectionManager::<PgConnection>::new(database_url);
    Pool::builder().build(manager)
}

pub fn establish_connection() -> PgPool {
    init_pool(dotenv!("DATABASE_URL_TEST")).expect("Failed to create pool")
} 
```

`tests/common/mod.rs` :

```
pub mod db_connection; 
```

我们需要一个数据库只是为了测试，所以在。env 文件添加一个新变量。

`src/.env` :

```
DATABASE_URL_TEST=postgres://postgres:@localhost/mystore_test 
```

让我们创建我们的测试数据库:

```
$ sudo su - postgres
$ createdb mystore_test 
```

运行迁移:

```
DATABASE_URL=postgres://postgres:@localhost/mystore_test  diesel migration run 
```

为了运行我们的集成测试，我们需要一个新文件`lib.rs`，这是因为 Rust 将集成测试视为一个单独的箱子，我们需要一种方法来共享我们在测试中创建的代码。

编辑`Cargo.toml`文件，用一个新的部分表示我们将使用的库的名称，并用一个依赖部分表示我们的测试:

`src/Cargo.toml` :

```
[lib]
name = "mystore_lib"
path = "src/lib.rs"

[dev-dependencies]
bytes = "0.4"
actix-http-test = "0.2.0" 
```

`src/lib.rs` :

```
#[macro_use]
extern crate diesel;
extern crate dotenv;
extern crate serde;
extern crate serde_json;
#[macro_use] 
extern crate serde_derive;

extern crate actix;
extern crate actix_web;
extern crate bcrypt;
extern crate jsonwebtoken as jwt;
extern crate csrf_token;

#[macro_use]
extern crate dotenv_codegen;

#[macro_use] extern crate log;
extern crate env_logger;

extern crate actix_http;

pub mod schema;
pub mod db_connection;
pub mod models;
pub mod handlers;
pub mod errors;
pub mod utils; 
```

我将一些行从`main.rs`移到了`lib.rs`，现在我们可以在集成测试中使用相同的代码。现在我们可以测试我们的产品端点，让我们创建一个名为`product_test.rs`的新文件。

`tests/product_test.rs` :

```
#[macro_use]
extern crate dotenv_codegen;

mod common;

mod test{
    use actix_http::HttpService;
    use actix_http_test::TestServer;
    use actix_web::{http, App, web};

    use std::str;
    use crate::common::db_connection::establish_connection;

    #[test]
    fn test() {
        let mut srv = TestServer::new(|| 
            HttpService::new(
                App::new()
                    .data(establish_connection())
                    .service(
                        web::resource("/products")
                            .route(web::get().to(::mystore_lib::handlers::products::index))
                    )
            )
        );

        let request = srv.get("/products");
        let mut response = srv.block_on(request.send()).unwrap();
        assert!(response.status().is_success());

        assert_eq!(
            response.headers().get(http::header::CONTENT_TYPE).unwrap(),
            "application/json"
        );

        let bytes = srv.block_on(response.body()).unwrap();
        let body = str::from_utf8(&bytes).unwrap();
        assert_eq!(body, "[]");
    }
} 
```

让我们使用`TestServer`为我们的测试建立一个简单的服务器，然后我们可以断言我们的响应是成功的，并给出我们预期的结果。

如果我们用`cargo test`运行测试，它们将会失败，因为我们仍然需要一种方法来认证用户。

让我们添加几个函数，一个用于创建用户，另一个用于登录。

`tests/product_test.rs` :

```
 fn create_user() {
        use diesel::RunQueryDsl;
        use ::mystore_lib::schema::users;
        use ::mystore_lib::models::user::{ NewUser, User };
        use chrono::Local;

        let connection = establish_connection();
        let pg_pool = connection.get().unwrap();

        diesel::delete(users::table).execute(&pg_pool).unwrap();

        diesel::insert_into(users::table)
            .values(NewUser {
                email: "jhon@doe.com".to_string(),
                company: "My own personal enterprise".to_string(),
                password: User::hash_password("12345678".to_string()).unwrap(),
                created_at: Local::now().naive_local()
            })
            .get_result::<User>(&pg_pool).unwrap();
    }

    fn login(mut srv: RefMut<TestServerRuntime>) -> (HeaderValue, Cookie) {
        let request = srv
                          .post("/auth")
                          .header(header::CONTENT_TYPE, "application/json")
                          .timeout(std_duration::from_secs(600));
        let response =
            srv
                .block_on(request.send_body(r#"{"email":"jhon@doe.com","password":"12345678"}"#))
                .unwrap();
        let csrf_token = response.headers().get("x-csrf-token").unwrap();
        let cookies = response.cookies().unwrap();
        let cookie = cookies[0].clone().into_owned().value().to_string();

        let request_cookie = Cookie::build("mystorejwt", cookie)
                                         .domain("localhost")
                                         .path("/")
                                         .max_age(Duration::days(1).num_seconds())
                                         .secure(false)
                                         .http_only(false)
                                         .finish();
        (csrf_token.clone(), request_cookie.clone())
    } 
```

我们使用 json 内容类型对`auth`执行 post 请求，我们添加一个更长的超时来告诉测试等待响应，然后我们传递主体，即经过身份验证的用户，最后我们从响应中获取 csrf_token 和 cookie，以便在其余的测试中使用它。

你可能注意到了`TestServerRuntime`被一个叫做`RefMut`的类型所包裹，那是因为 Rust 不允许一个变量变异超过一次，我们将为每个请求变异服务器变量，我们需要为帖子和补丁中的标题、cookies 和正文附件做这些，只要看看[标题](https://docs.rs/awc/0.2.0/src/awc/request.rs.html#181-196)的定义，你能看到函数参数中的`mut self`吗？，这是我们知道服务器将发生变异的标志。

让我们来看一个禁止突变的例子:

```
struct Books {
    name: String,
    author: String,
    subject: String,
    book_id: i32
}

fn main() {
    let mut book1 = Books { 
        name: "Rust Programming".to_string(),
        author: "Jhon Doe".to_string(),
        subject: "Programming".to_string(),
        book_id: 12
    };

    let name = change_name(&mut book1);
    change_author(&mut book1);

    println!("{}", name);
}

fn change_name(book: &mut Books) -> &String {
    book.name = "Great Rust Programming".to_string();
    &book.name
}

fn change_author(book: &mut Books) {
    book.author = "Fulano".to_string();
} 
```

如果你尝试编译前面的例子，你会得到:

```
error[E0499]: cannot borrow `book1` as mutable more than once at a time
  --> src/main.rs:17:19
   |
16 |     let name = change_name(&mut book1);
   |                            ---------- first mutable borrow occurs here
17 |     change_author(&mut book1);
   |                   ^^^^^^^^^^ second mutable borrow occurs here
18 |     
19 |     println!("{}", name);
   |                    ---- first borrow later used here 
```

让我们添加测试产品端点所需的其余函数。

```
 fn clear_products() {
        use diesel::RunQueryDsl;
        use ::mystore_lib::schema::products;

        let connection = establish_connection();
        let pg_pool = connection.get().unwrap();
        diesel::delete(products::table).execute(&pg_pool).unwrap();
    }

    fn create_a_product(mut srv: RefMut<TestServerRuntime>,
                            csrf_token: HeaderValue,
                            request_cookie: Cookie,
                            product: &NewProduct) -> Product {

        let request = srv
                          .post("/products")
                          .header(header::CONTENT_TYPE, "application/json")
                          .header("x-csrf-token", csrf_token.to_str().unwrap())
                          .cookie(request_cookie)
                          .timeout(std_duration::from_secs(600));

        let mut response =
            srv
                .block_on(request.send_body(json!(product).to_string()))
                .unwrap();

        assert!(response.status().is_success());

        let bytes = srv.block_on(response.body()).unwrap();
        let body = str::from_utf8(&bytes).unwrap();
        serde_json::from_str(body).unwrap()
    }

    fn show_a_product(mut srv: RefMut<TestServerRuntime>,
                          csrf_token: HeaderValue,
                          request_cookie: Cookie,
                          id: &i32,
                          expected_product: &Product) {

        let request = srv
                        .get(format!("/products/{}", id))
                        .header("x-csrf-token", csrf_token.to_str().unwrap())
                        .cookie(request_cookie);

        let mut response = srv.block_on(request.send()).unwrap();
        assert!(response.status().is_success());

        assert_eq!(
            response.headers().get(http::header::CONTENT_TYPE).unwrap(),
            "application/json"
        );

        let bytes = srv.block_on(response.body()).unwrap();
        let body = str::from_utf8(&bytes).unwrap();
        let response_product: Product = serde_json::from_str(body).unwrap();
        assert_eq!(&response_product, expected_product);
    }

    fn update_a_product(mut srv: RefMut<TestServerRuntime>,
                          csrf_token: HeaderValue,
                          request_cookie: Cookie,
                          id: &i32,
                          changes_to_product: &NewProduct) {

        let request = srv
                        .request(http::Method::PATCH, srv.url(&format!("/products/{}", id)))
                        .header(header::CONTENT_TYPE, "application/json")
                        .header("x-csrf-token", csrf_token.to_str().unwrap())
                        .cookie(request_cookie)
                        .timeout(std_duration::from_secs(600));

        let response =
            srv
                .block_on(request.send_body(json!(changes_to_product).to_string()))
                .unwrap();
        assert!(response.status().is_success());
    }

    fn destroy_a_product(mut srv: RefMut<TestServerRuntime>,
                          csrf_token: HeaderValue,
                          request_cookie: Cookie,
                          id: &i32) {
        let request = srv
                        .request(http::Method::DELETE, srv.url(&format!("/products/{}", id)))
                        .header(header::CONTENT_TYPE, "application/json")
                        .header("x-csrf-token", csrf_token.to_str().unwrap())
                        .cookie(request_cookie)
                        .timeout(std_duration::from_secs(600));

        let response =
            srv
                .block_on(request.send())
                .unwrap();
        assert!(response.status().is_success());
    }

    fn products_index(mut srv: RefMut<TestServerRuntime>,
                          csrf_token: HeaderValue,
                          request_cookie: Cookie,
                      mut data_to_compare: Vec<NewProduct>) {

        let request = srv
                        .get("/products")
                        .header("x-csrf-token", csrf_token.to_str().unwrap())
                        .cookie(request_cookie);

        let mut response = srv.block_on(request.send()).unwrap();
        assert!(response.status().is_success());

        assert_eq!(
            response.headers().get(http::header::CONTENT_TYPE).unwrap(),
            "application/json"
        );

        let bytes = srv.block_on(response.body()).unwrap();
        let body = str::from_utf8(&bytes).unwrap();
        let mut response_products: Vec<Product> = serde_json::from_str(body).unwrap();
        data_to_compare.sort_by_key(|product| product.name.clone());
        response_products.sort_by_key(|product| product.name.clone());

        // Can you see we're doing something weird here, 
        // we're comparing two different types, expected
        // to be the same, Vec<NewProduct> == Vec<Product>
        assert_eq!(data_to_compare, response_products);
    } 
```

在索引函数中我们期望 a `Vec<Product>`等于 a `Vec<NewProduct>`，我们怎么能期望两个不同类型的向量是相同的呢？。我们可以为此使用一个特征，`PartialEq`，让我们看看`models/product.rs`中的代码。

`src/models/product.rs` :

```
impl PartialEq<Product> for NewProduct {
    fn eq(&self, other: &Product) -> bool {
        let new_product = self.clone();
        let product = other.clone();
        new_product.name == Some(product.name) &&
        new_product.stock == Some(product.stock) &&
        new_product.price == product.price
    }
} 
```

现在我们要完成我们的主要功能:

```
 fn test() {

        create_user();

        let csrf_token_header =
            header::HeaderName::from_lowercase(b"x-csrf-token").unwrap();

        // RefCell is the type that allows us multiple mutations
        let srv = RefCell::new(TestServer::new(move || 
            HttpService::new(
                App::new()
                    .wrap(
                        IdentityService::new(
                            CookieIdentityPolicy::new(dotenv!("SECRET_KEY").as_bytes())
                                .domain("localhost")
                                .name("mystorejwt")
                                .path("/")
                                .max_age(Duration::days(1).num_seconds())
                                .secure(false)
                        )
                    )
                    .wrap(
                        cors::Cors::new()
                            .allowed_origin("localhost")
                            .allowed_methods(vec!["GET", "POST", "PUT", "PATCH", "DELETE"])
                            .allowed_headers(vec![header::AUTHORIZATION,
                                                  header::CONTENT_TYPE,
                                                  header::ACCEPT,
                                                  csrf_token_header.clone()])
                            .expose_headers(vec![csrf_token_header.clone()])
                            .max_age(3600)
                    )
                    .data(
                        CsrfTokenGenerator::new(
                            dotenv!("CSRF_TOKEN_KEY").as_bytes().to_vec(),
                            Duration::hours(1)
                        )
                    )
                    .data(establish_connection())
                    .service(
                        web::resource("/products")
                            .route(web::get()
                                .to(::mystore_lib::handlers::products::index))
                            .route(web::post()
                                .to(::mystore_lib::handlers::products::create))
                    )
                    .service(
                        web::resource("/products/{id}")
                            .route(web::get()
                                .to(::mystore_lib::handlers::products::show))
                            .route(web::delete()
                                .to(::mystore_lib::handlers::products::destroy))
                            .route(web::patch()
                                .to(::mystore_lib::handlers::products::update))
                    )
                    .service(
                        web::resource("/auth")
                            .route(web::post()
                                .to(::mystore_lib::handlers::authentication::login))
                            .route(web::delete()
                                .to(::mystore_lib::handlers::authentication::logout))
                    )

            )
        ));

        let (csrf_token, request_cookie) = login(srv.borrow_mut());
        clear_products();

        let shoe = NewProduct {
            name: Some("Shoe".to_string()),
            stock: Some(10.4),
            price: Some(1892)
        };

        let hat = NewProduct {
            name: Some("Hat".to_string()),
            stock: Some(15.0),
            price: Some(2045)
        };

        let pants = NewProduct {
            name: Some("Pants".to_string()),
            stock: Some(25.0),
            price: Some(3025)
        };
        let shoe_db = create_a_product(srv.borrow_mut(),
                                       csrf_token.clone(),
                                       request_cookie.clone(),
                                       &shoe);
        let hat_db = create_a_product(srv.borrow_mut(),
                                      csrf_token.clone(),
                                      request_cookie.clone(),
                                      &hat);
        let pants_db = create_a_product(srv.borrow_mut(),
                                        csrf_token.clone(), 
                                        request_cookie.clone(), 
                                        &pants);
        show_a_product(srv.borrow_mut(), 
                       csrf_token.clone(), 
                       request_cookie.clone(), 
                       &shoe_db.id, 
                       &shoe_db);
        let updated_hat = NewProduct {
            name: Some("Hat".to_string()),
            stock: Some(30.0),
            price: Some(3025)
        };
        update_a_product(srv.borrow_mut(), 
                         csrf_token.clone(), 
                         request_cookie.clone(), 
                         &hat_db.id, 
                         &updated_hat);
        destroy_a_product(srv.borrow_mut(), 
                          csrf_token.clone(), 
                          request_cookie.clone(), 
                          &pants_db.id);
        products_index(srv.borrow_mut(), 
                       csrf_token, 
                       request_cookie, 
                       vec![shoe, updated_hat]);
    } 
```

## 结论

我只是测试最好的情况，这取决于你去写失败测试，看看我们的软件在错误之前如何表现，你可以在这里看一下完整的源代码。
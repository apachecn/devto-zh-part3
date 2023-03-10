# 实用 Rust Web 开发-连接池

> 原文：<https://dev.to/werner/practical-rust-web-development-connection-pool-46f4>

在之前的文章中，我们为一个[网络商店](https://dev.to/werner/practical-rust-web-development-api-rest-29g1)创建了基础 Api。这次我将介绍数据库连接的增强，我们将使用一个被称为 [r2d2](https://github.com/sfackler/r2d2) 的通用连接池，主要优势是我们可以通过缓存[资源](https://en.wikipedia.org/wiki/Connection_pool)来改进与数据库的连接，Diesel 为此提供了一个模块，我们只需要启用它。

`Cargo.toml` :

```
diesel = { version = "1.0.0", features = ["postgres", "r2d2"] } 
```

然后我们需要配置我们的数据库来处理池连接，我们添加一个新函数`init_pool`来实例化一个新的数据库池，我们编辑我们的`db_connection.rs`文件:

`src/db_connection.rs` :

```
// This is my diff git showing what the changes are.

-use diesel::prelude::*;
 use diesel::pg::PgConnection;
 use dotenv::dotenv;
 use std::env;
+use diesel::r2d2::{ Pool, PooledConnection, ConnectionManager, PoolError };

-pub fn establish_connection() -> PgConnection {
+pub type PgPool = Pool<ConnectionManager<PgConnection>>;
+pub type PgPooledConnection = PooledConnection<ConnectionManager<PgConnection>>;
+
+fn init_pool(database_url: &str) -> Result<PgPool, PoolError> {
+    let manager = ConnectionManager::<PgConnection>::new(database_url);
+    Pool::builder().build(manager)
+}
+
+pub fn establish_connection() -> PgPool {
     dotenv().ok();

     let database_url = env::var("DATABASE_URL")
         .expect("DATABASE_URL must be set");
-    PgConnection::establish(&database_url)
-        .expect(&format!("Error connecting to {}", database_url))
+    init_pool(&database_url).expect("Failed to create pool")
 } 
```

我们需要执行其他更改，我也将粘贴我的 diff git，你可以看到哪些行必须删除或更改。

`src/models/product.rs` :

```
+use diesel::PgConnection;

 impl ProductList {
-    pub fn list() -> Self {
+    pub fn list(connection: &PgConnection) -> Self {
         use diesel::RunQueryDsl;
         use diesel::QueryDsl;
         use crate::schema::products::dsl::*;
-        use crate::db_connection::establish_connection;
-
-        let connection = establish_connection();

         let result = 
             products
                 .limit(10)
-                .load::<Product>(&connection)
+                .load::<Product>(connection)
                 .expect("Error loading products");

         ProductList(result)
...

 impl NewProduct {
-    pub fn create(&self) -> Result<Product, diesel::result::Error> {
+    pub fn create(&self, connection: &PgConnection) -> Result<Product, diesel::result::Error> {
         use diesel::RunQueryDsl;
-        use crate::db_connection::establish_connection;

-        let connection = establish_connection();
         diesel::insert_into(products::table)
             .values(self)
-            .get_result(&connection)
+            .get_result(connection)
     }
 }
...

 impl Product {
-    pub fn find(id: &i32) -> Result<Product, diesel::result::Error> {
+    pub fn find(id: &i32, connection: &PgConnection) -> Result<Product, diesel::result::Error> {
         use diesel::QueryDsl;
         use diesel::RunQueryDsl;
-        use crate::db_connection::establish_connection;
-
-        let connection = establish_connection();

-        products::table.find(id).first(&connection)
+        products::table.find(id).first(connection)
     }

-    pub fn destroy(id: &i32) -> Result<(), diesel::result::Error> {
+    pub fn destroy(id: &i32, connection: &PgConnection) -> Result<(), diesel::result::Error> {
         use diesel::QueryDsl;
         use diesel::RunQueryDsl;
         use crate::schema::products::dsl;
-        use crate::db_connection::establish_connection;

-        let connection = establish_connection();
-
-        diesel::delete(dsl::products.find(id)).execute(&connection)?;
+        diesel::delete(dsl::products.find(id)).execute(connection)?;
         Ok(())
     }

-    pub fn update(id: &i32, new_product: &NewProduct) -> Result<(), diesel::result::Error> {
+    pub fn update(id: &i32, new_product: &NewProduct, connection: &PgConnection) ->
+     Result<(), diesel::result::Error> {
         use diesel::QueryDsl;
         use diesel::RunQueryDsl;
         use crate::schema::products::dsl;
-        use crate::db_connection::establish_connection;
-
-        let connection = establish_connection();

         diesel::update(dsl::products.find(id))
             .set(new_product)
-            .execute(&connection)?;
+            .execute(connection)?;
         Ok(())
     }
 } 
```

在`models/product.rs`中，我们只是给每个方法添加一个新参数来传递数据库连接。

`src/handlers/products.rs` :

```
 use actix_web::{HttpRequest, HttpResponse };
+use actix_web::web;

 use crate::models::product::ProductList;
+use crate::db_connection::{ PgPool, PgPooledConnection };
+
+fn pg_pool_handler(pool: web::Data<PgPool>) -> Result<PgPooledConnection, HttpResponse> {
+    pool
+    .get()
+    .map_err(|e| {
+        HttpResponse::InternalServerError().json(e.to_string())
+    })
+}

-pub fn index(_req: HttpRequest) -> HttpResponse {
-    HttpResponse::Ok().json(ProductList::list())
+pub fn index(_req: HttpRequest, pool: web::Data<PgPool>) -> Result<HttpResponse, HttpResponse> {
+    let pg_pool = pg_pool_handler(pool)?;
+    Ok(HttpResponse::Ok().json(ProductList::list(&pg_pool)))
 }

 use crate::models::product::NewProduct;
-use actix_web::web;

-pub fn create(new_product: web::Json<NewProduct>) -> Result<HttpResponse, HttpResponse> {
+pub fn create(new_product: web::Json<NewProduct>, pool: web::Data<PgPool>) -> Result<HttpResponse, HttpResponse> {
+    let pg_pool = pg_pool_handler(pool)?;
     new_product
-        .create()
+        .create(&pg_pool)
         .map(|product| HttpResponse::Ok().json(product))
         .map_err(|e| {
             HttpResponse::InternalServerError().json(e.to_string())

-pub fn show(id: web::Path<i32>) -> Result<HttpResponse, HttpResponse> {
-    Product::find(&id)
+pub fn show(id: web::Path<i32>, pool: web::Data<PgPool>) -> Result<HttpResponse, HttpResponse> {
+    let pg_pool = pg_pool_handler(pool)?;
+    Product::find(&id, &pg_pool)
         .map(|product| HttpResponse::Ok().json(product))
         .map_err(|e| {
             HttpResponse::InternalServerError().json(e.to_string())
         })
 }

-pub fn destroy(id: web::Path<i32>) -> Result<HttpResponse, HttpResponse> {
-    Product::destroy(&id)
+pub fn destroy(id: web::Path<i32>, pool: web::Data<PgPool>) -> Result<HttpResponse, HttpResponse> {
+    let pg_pool = pg_pool_handler(pool)?;
+    Product::destroy(&id, &pg_pool)
         .map(|_| HttpResponse::Ok().json(()))
         .map_err(|e| {
             HttpResponse::InternalServerError().json(e.to_string())
         })
 }

-pub fn update(id: web::Path<i32>, new_product: web::Json<NewProduct>) -> Result<HttpResponse, HttpResponse> {
-    Product::update(&id, &new_product)
+pub fn update(id: web::Path<i32>, new_product: web::Json<NewProduct>, pool: web::Data<PgPool>) -> Result<HttpResponse, HttpResponse> {
+    let pg_pool = pg_pool_handler(pool)?;
+    Product::update(&id, &new_product, &pg_pool)
         .map(|_| HttpResponse::Ok().json(()))
         .map_err(|e| {
             HttpResponse::InternalServerError().json(e.to_string()) 
```

在`handlers/products.rs`中，我们添加了一个新函数来发送请求参数并返回我们的模型所需的数据库池，带有新变化的源代码在[这里](https://github.com/werner/practical-rust-web/tree/v1.1)可用。
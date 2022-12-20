# 实用 Rust Web 开发-认证

> 原文：<https://dev.to/werner/practical-rust-web-development-authentication-3ppg>

在本系列的这篇文章中，我将讨论身份验证。这是基于这个 [one](https://gill.net.in/posts/auth-microservice-rust-actix-web-diesel-complete-tutorial-part-2/) 的，但我将使用 actix-web 的 1.0 版本。

我们将使用 [jwt](https://en.wikipedia.org/wiki/JSON_Web_Token) 来验证 cookie 中的用户，一个安全考虑是使用 cookie 时的 CSRF 漏洞，因此，我们将使用一个板条箱来帮助我们。如果我们要使用本地存储，我们将需要 XSS 保护。在使用 jwt 的时候，你应该注意其他的安全预防措施，比如[和](https://connect2id.com/products/nimbus-jose-jwt/vulnerabilities)。如果您有任何其他安全建议，请发表评论。

我们将在注册后创建用户，为了提高安全性，你可以添加电子邮件验证，验证码或 2FA。

我们需要增加一些板条箱:

`src/Cargo.toml` :

```
jsonwebtoken = "6"
bcrypt = "0.4.0"
chrono = { version = "0.4.6", features = ["serde"] }
csrf-token = { git = "ssh://git@github.com/3dom-co-jp/csrf-token.git", branch="v0.2.x" } 
```

我们也需要我们的用户模型，但是首先让我们为表创建一个迁移。

```
diesel migration generate create_users 
```

生成的迁移:

`migrations/2019-05-19-165021_create_users/up.sql` :

```
CREATE TABLE users (
  email VARCHAR(100) NOT NULL PRIMARY KEY,
  password VARCHAR(64) NOT NULL,
  created_at TIMESTAMP NOT NULL
);
CREATE INDEX users_email_company_idx ON users (email, company); 
```

`migrations/2019-05-19-165021_create_users/down.sql` :

```
DROP TABLE users; 
```

```
diesel migration run 
```

## 用户模式

接下来是用户模型，为了创建一个用户，我们使用了一个`RegisterUser`结构并通过`NewUser`创建它，我这样做是因为我们在数据库中不需要一个`password_confirmation`字段，但是我们在注册操作中需要它。另一个结构是`AuthUser`，我仅用它来进行认证，它有两个字段，`email`和`password`，看起来有点样板，但是值得一试。

`src/models/mod.rs` :

```
pub mod user; 
```

`src/models/user.rs` :

```
use chrono::NaiveDateTime; // This type is used for date field in Diesel.
use crate::schema::users;

#[derive(Debug, Serialize, Deserialize, Queryable, Insertable)]
#[table_name = "users"]
pub struct User {
    #[serde(skip)] // we're removing id from being show in the response
    pub id: i32,
    pub email: String,
    pub company: String,
    #[serde(skip)] // we're removing password from being show in the response
    pub password: String,
    pub created_at: NaiveDateTime
}

#[derive(Debug, Serialize, Deserialize, Insertable)]
#[table_name = "users"]
pub struct NewUser {
    pub email: String,
    pub company: String,
    pub password: String,
    pub created_at: NaiveDateTime
}

use bcrypt::{hash, DEFAULT_COST};
use diesel::PgConnection;
use chrono::Local;
use crate::errors::MyStoreError;

// MyStoreError is a custom error that I will show it next.
impl User {
    pub fn create(register_user: RegisterUser, connection: &PgConnection) ->
     Result<User, MyStoreError> {
        use diesel::RunQueryDsl;

        Ok(diesel::insert_into(users::table)
            .values(NewUser {
                email: register_user.email,
                company: register_user.company,
                password: Self::hash_password(register_user.password)?,
                created_at: Local::now().naive_local()
            })
            .get_result(connection)?)
    }

    // This might look kind of weird, 
    // but if something fails it would chain 
    // to our MyStoreError Error, 
    // otherwise it will gives us the hash, 
    // we still need to return a result 
    // so we wrap it in an Ok variant from the Result type. 
    pub fn hash_password(plain: String) -> Result<String, MyStoreError> {
        Ok(hash(plain, DEFAULT_COST)?)
    }
}

#[derive(Deserialize)]
pub struct RegisterUser {
    pub email: String,
    pub company: String,
    pub password: String,
    pub password_confirmation: String
}

impl RegisterUser {
    pub fn validates(self) ->
     Result<RegisterUser, MyStoreError> {
         if self.password == self.password_confirmation {
             Ok(self)
         } else {
             Err(
                 MyStoreError::PasswordNotMatch(
                     "Password and Password Confirmation does not match".to_string()
                 )
             )
         }
    }
}

#[derive(Deserialize)]
pub struct AuthUser {
    pub email: String,
    pub password: String
}

impl AuthUser {

    // The good thing about ? syntax and have a custom error is 
    // that the code would look very straightforward, I mean, 
    // the other way would imply a lot of pattern matching 
    // making it look ugly. 
    pub fn login(&self, connection: &PgConnection) ->
     Result<User, MyStoreError> {
        use bcrypt::verify;
        use diesel::QueryDsl;
        use diesel::RunQueryDsl;
        use diesel::ExpressionMethods;
        use crate::schema::users::dsl::email;

        let mut records =
            users::table
                .filter(email.eq(&self.email))
                .load::<User>(connection)?;

        let user =
            records
                .pop()
                .ok_or(MyStoreError::DBError(diesel::result::Error::NotFound))?;

        let verify_password =
            verify(&self.password, &user.password)
                .map_err( |_error| {
                    MyStoreError::WrongPassword(
                        "Wrong password, check again please".to_string()
                    )
                })?;

        if verify_password {
            Ok(user)
        } else {
            Err(MyStoreError::WrongPassword(
                "Wrong password, check again please".to_string()
            ))
        }

    }
} 
```

如果你运行`cargo build`，你会看到一个错误:

```
the trait `diesel::Expression` is not implemented for 
`chrono::naive::datetime::NaiveDateTime` 
```

这表明我们只需要在`Cargo.toml`中为 diesel 添加一个特性，它看起来像这样:

`src/Cargo.toml` :

```
diesel = { version = "1.0.0", features = ["postgres", "r2d2", "chrono"] } 
```

现在，它应该编译没有问题。

## 自定义错误

在用户模型中，你可以看到许多`MyStoreError`错误，想法是有一个统一的定制错误，你可以操纵它，使它更容易有一个更可读的代码，感谢`?`语法糖，因为它需要有相同的错误，所以它可以将自己链接到其他函数，调用另一个返回结果类型的函数，Rust book 有一个关于 [`?`运算符](https://doc.rust-lang.org/edition-guide/rust-2018/error-handling-and-panics/the-question-mark-operator-for-easier-error-handling.html)的很好的解释。

`src/errors.rs` :

```
use std::fmt;
use bcrypt::BcryptError;
use diesel::result;

pub enum MyStoreError {
    HashError(BcryptError),
    DBError(result::Error),
    PasswordNotMatch(String),
    WrongPassword(String)
}

// We need this to performs a conversion from BcryptError to MyStoreError
impl From<BcryptError> for MyStoreError {
    fn from(error: BcryptError) -> Self {
        MyStoreError::HashError(error)
    }
}

// We need this to performs a conversion from diesel::result::Error to MyStoreError
impl From<result::Error> for MyStoreError {
    fn from(error: result::Error) -> Self {
        MyStoreError::DBError(error)
    }
}

// We need this so we can use the method to_string over MyStoreError 
impl fmt::Display for MyStoreError {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        match self {
            MyStoreError::HashError(error) => write!(f, "{}", error),
            MyStoreError::DBError(error) => write!(f, "{}", error),
            MyStoreError::PasswordNotMatch(error) => write!(f, "{}", error),
            MyStoreError::WrongPassword(error) => write!(f, "{}", error)
        }
    }
} 
```

`src/main.rs` :

```
pub mod errors; 
```

## 经手人

现在，我们只需要注册用户和登录的处理程序。

`src/handlers/register.rs` :

```
use actix_web::web;
use crate::db_connection::PgPool;
use actix_web::HttpResponse;
use crate::handlers::pg_pool_handler;

use crate::models::user::{ User, RegisterUser };

// We get a new connection pool, validates the data, 
// `password` and `password_confirmation` should be the same, 
// finally we create the user and return it.
pub fn register(new_user: web::Json<RegisterUser>, pool: web::Data<PgPool>) ->
 Result<HttpResponse, HttpResponse> {
    let pg_pool = pg_pool_handler(pool)?;
    let register_user = new_user
        .into_inner()
        .validates()
        .map_err(|e| {
           HttpResponse::InternalServerError().json(e.to_string())
        })?;
    User::create(register_user, &pg_pool)
        .map(|user| HttpResponse::Ok().json(user))
        .map_err(|e| {
           HttpResponse::InternalServerError().json(e.to_string())
        })
} 
```

`src/handlers/authentication.rs` :

```
use actix_web::HttpResponse;
use actix_web::middleware::identity::Identity;
use actix_web::web;
use csrf_token::CsrfTokenGenerator;
use hex;
use crate::utils::jwt::create_token;

use crate::models::user::AuthUser;
use crate::db_connection::PgPool;
use crate::handlers::pg_pool_handler;

// We get a new connection pool, then look up for the user,
// If there is no user a NotFound error would raise otherwise
// this would just through an InternalServerError.
pub fn login(auth_user: web::Json<AuthUser>, 
             id: Identity, 
             pool: web::Data<PgPool>, 
             generator: web::Data<CsrfTokenGenerator>) 
    -> Result<HttpResponse, HttpResponse> {
    let pg_pool = pg_pool_handler(pool)?;
    let user = auth_user
        .login(&pg_pool)
        .map_err(|e| {
            match e {
                MyStoreError::DBError(diesel::result::Error::NotFound) =>
                    HttpResponse::NotFound().json(e.to_string()),
                _ =>
                    HttpResponse::InternalServerError().json(e.to_string())
            }
        })?;

    // This is the jwt token we will send in a cookie.
    let token = create_token(&user.email, &user.company)?;

    id.remember(token);

    // Finally our response will have a csrf token for security. 
    let response =
        HttpResponse::Ok()
        .header("X-CSRF-TOKEN", hex::encode(generator.generate()))
        .json(user);
    Ok(response)
}

pub fn logout(id: Identity) -> Result<HttpResponse, HttpResponse> {
    id.forget();
    Ok(HttpResponse::Ok().into())
} 
```

`src/handlers/mod.rs` :

```
pub mod products;
pub mod register;
pub mod authentication;

use actix_web::web;
use actix_web::HttpResponse;
use crate::db_connection::{ PgPool, PgPooledConnection };

// Because I'm using this function a lot, 
// I'm including it in the mod file accessible to all handlers.
pub fn pg_pool_handler(pool: web::Data<PgPool>) -> Result<PgPooledConnection, HttpResponse> {
    pool
    .get()
    .map_err(|e| {
        HttpResponse::InternalServerError().json(e.to_string())
    })
} 
```

## Json Web Token 实现

现在我们可以继续 Jwt 库，让我们创建一个名为 utils 的文件夹，并创建一个名为`jwt.rs`的文件。

`src/utils/jwt.rs` :

```
use jwt::{decode, encode, Header, Validation};
use chrono::{Local, Duration};
use actix_web::HttpResponse;

#[derive(Debug, Serialize, Deserialize)]
struct Claims {
    sub: String,
    company: String,
    exp: usize
}

// We're using a struct so we can implement a conversion from
// Claims to SlimUser, useful in the decode function.
pub struct SlimUser {
    pub email: String,
    pub company: String
}

impl From<Claims> for SlimUser {
    fn from(claims: Claims) -> Self {
        SlimUser {
            email: claims.sub,
            company: claims.company
        }
    }
}

impl Claims {
    fn with_email(email: &str, company: &str) -> Self {
        Claims {
            sub: email.into(),
            company: company.into(),
            exp: (Local::now() + Duration::hours(24)).timestamp() as usize
        }
    }
}

pub fn create_token(email: &str, company: &str) -> Result<String, HttpResponse> {
    let claims = Claims::with_email(email, company);
    encode(&Header::default(), &claims, get_secret())
        .map_err(|e| HttpResponse::InternalServerError().json(e.to_string()))
}

pub fn decode_token(token: &str) -> Result<SlimUser, HttpResponse> {
    decode::<Claims>(token, get_secret(), &Validation::default())
        .map(|data| data.claims.into())
        .map_err(|e| HttpResponse::Unauthorized().json(e.to_string()))
}

fn get_secret<'a>() -> &'a [u8] {
    dotenv!("JWT_SECRET").as_bytes()
} 
```

`src/utils/mod.rs` :

```
pub mod jwt; 
```

## 来自请求

我们将需要使用 Actix Web FromRequest 特征，以便我们的实现可以使用登录身份验证，我们的想法是使用这个特征来捕获所有请求，并验证 csrf 令牌和 jwt 令牌。

在产品处理程序中有一些修改，因为我们需要请求中的 LoggedUser 结构。我在这篇文章中省略了代码，但是你可以看看 Github 中的源代码。

`src/handlers/mod.rs` :

```
use actix_web::{ FromRequest, HttpRequest, dev };
use actix_web::middleware::identity::Identity;
use crate::utils::jwt::{ decode_token, SlimUser };
pub type LoggedUser = SlimUser;

use hex;
use csrf_token::CsrfTokenGenerator;

impl FromRequest for LoggedUser {
    type Error = HttpResponse;
    type Config = ();
    type Future = Result<Self, HttpResponse>;

    fn from_request(req: &HttpRequest, payload: &mut dev::Payload) -> Self::Future {
        let generator = 
            req.app_data::<CsrfTokenGenerator>()
            .ok_or(HttpResponse::InternalServerError())?;

        let csrf_token =
            req
                .headers()
                .get("x-csrf-token")
                .ok_or(HttpResponse::Unauthorized())?;

        let decoded_token =
            hex::decode(&csrf_token)
                .map_err(|error| HttpResponse::InternalServerError().json(error.to_string()))?;

        generator
            .verify(&decoded_token)
            .map_err(|_| HttpResponse::Unauthorized())?;

        // We're using the CookieIdentityPolicy middleware
        // to handle cookies, with this implementation this 
        // will validate the cookie according to the secret
        // provided in main function
        if let Some(identity) = Identity::from_request(req, payload)?.identity() {
            let user: SlimUser = decode_token(&identity)?;
            return Ok(user as LoggedUser);
        }  
        Err(HttpResponse::Unauthorized().into())
    }
} 
```

最后，`main.rs`文件看起来像这样，我们使用不同的中间件，一个用于日志记录，另一个用于 cookies，另一个用于 Cors，另一个用于 csrf 令牌，尽管在最后一个中，我们使用数据方法，这意味着不是真正的中间件，它只是我们通过应用程序共享的应用程序数据，如数据库连接:

`src/main.rs` :

```
fn main() {
    std::env::set_var("RUST_LOG", "actix_web=debug");
    env_logger::init();
    let sys = actix::System::new("mystore");

    let csrf_token_header = header::HeaderName::from_lowercase(b"x-csrf-token").unwrap();

    HttpServer::new(
    move || App::new()
        .wrap(Logger::default())
        // we implement middleares with the warp method
        .wrap( 
            IdentityService::new(
                CookieIdentityPolicy::new(dotenv!("SECRET_KEY").as_bytes())
                    .domain(dotenv!("MYSTOREDOMAIN"))
                    .name("mystorejwt")
                    .path("/")
                    .max_age(Duration::days(1).num_seconds())
                    .secure(dotenv!("COOKIE_SECURE").parse().unwrap())
            )
        )
        .wrap(
            cors::Cors::new()
                .allowed_origin(dotenv!("ALLOWED_ORIGIN"))
                .allowed_methods(vec!["GET", "POST", "PUT", "DELETE"])
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
                .route(web::get().to(handlers::products::index))
                .route(web::post().to(handlers::products::create))
        )
        .service(
            web::resource("/products/{id}")
                .route(web::get().to(handlers::products::show))
                .route(web::delete().to(handlers::products::destroy))
                .route(web::patch().to(handlers::products::update))
        )
        .service(
            web::resource("/register")
                .route(web::post().to(handlers::register::register))
        )
        .service(
            web::resource("/auth")
                .route(web::post().to(handlers::authentication::login))
                .route(web::delete().to(handlers::authentication::logout))
        )
    )
    .bind("127.0.0.1:8088").unwrap()
    .start();

    println!("Started http server: 127.0.0.1:8088");
    let _ = sys.run();
} 
```

## 结论

我们在认证我们的用户时需要谨慎，你可以采取一些安全预防措施，OWASP 提供了一些资源来保护我们的 web 应用程序，如[十大](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)，所以，只要做好你的研究并努力工作，如果你有其他保护应用程序的建议，请发表评论。

你可以看看完整的源代码[这里](https://github.com/werner/practical-rust-web/tree/v2)
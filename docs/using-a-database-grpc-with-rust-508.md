# 使用 Rust 的数据库+ gRPC

> 原文：<https://dev.to/tjtelan/using-a-database-grpc-with-rust-508>

这是我编写 Rust DB 支持的服务器/客户端的经验总结，使用 [grpc-rs](https://github.com/pingcap/grpc-rs) 与后端通信，使用 [Diesel](http://diesel.rs/) 作为 ORM 与 [PostgreSQL](https://www.postgresql.org/) 一起使用。

## 我想从这个练习中得到什么？

我不认为自己是生锈的专家，也不是初学者。我关注 Rust 语言开发已经有一段时间了。我也一直想从为个人项目写代码转向为工作项目写代码。

我有特权在工作中选择我想要的工具，但我必须记住，我不是一个人工作。我需要能够向我的队友提供实用的开发建议和足够的技术指导，以保持我们所有人的生产力。

Kevin Hoffman 的博客文章让我知道我今天想要的在 stable 是可能的(而不是每夜)。Kevin 的帖子很棒，但我最初几读并不能真正理解，因为他是一个比我更有经验的 Rust 开发者。我不太理解他文章中的代码，我也不能欣赏他略读的细节，我会指出来。我希望我能提供补充细节。

### 我的目标

我期待建立一个非常基本的命令行界面客户端，和后端服务。cli 通过 gRPC 与后端通信，后端连接到数据库。

**gRPC**

根据凯文·霍夫曼的经验，以及 crates.io 上的下载活动，我还使用了 Pingcap 的库 [grpc-rs](https://github.com/pingcap/grpc-rs) 。然而，在写这篇文章的时候 [tower-rs](https://github.com/tower-rs/tower-grpc) (这是一个纯 Rust 实现)被认为是稳定的，尽管可能还没有实现所有的功能。

**数据库**

对于数据库，我决定使用 [Diesel-rs](https://crates.io/crates/diesel) ,因为我觉得在生产环境中没有其他更好的选择了。Diesel 是一个得到积极支持的成熟项目。

**命令行界面**

对于命令行界面，我选择了 [clap-rs](https://crates.io/crates/clap) ，因为我对尝试用 yaml 定义命令行内容和结构感兴趣。将来我可能会使用 [StructOpt](https://crates.io/crates/structopt) 。它碰巧在内部使用了 clap-rs，但是写出来的代码对我来说更容易阅读，在我看来，更少的代码编写派生。出于这个原因，我可能会忽略命令行实现。它提供了我所需要的最少的交互来强调看起来是惯用的模式。

在花了几个小时使用所有工具后，我想先从一个示例项目开始。

## 我第一次尝试找出自己的发展模式

我曾短暂地考虑过不要讲述我如何让所有东西都编译好的那部分故事，但事实就是这样。这最终成为一次重要的学习经历。我不会深入谈论我的意图，因为我最终没有朝这个方向前进。但我会强调我所学到的。

我专注于用 Diesel 和 gRPC 单独建造。一旦我觉得可以用这些板条箱做些有成效的事情，我就开始考虑通过首先设计协议缓冲区，然后设计数据库来实现。亲爱的读者，这是一个耗费时间的错误，希望不要重复。

### 工作流中的红旗

我正在我的 [build.rs](https://github.com/tjtelan/rust-examples/blob/master/cli-clap-grpc-pingcap-db-diesel/workspace/protos/build.rs) 中使用 [grpc-rs](https://github.com/pingcap/grpc-rs) 从`.proto`生成我的原型 Rust 代码。它在`cargo build`期间运行。基于 Diesel 的[入门](http://diesel.rs/guides/getting-started/)指南，我期望我会用同样的`#[derive()]`来注释我的原型 Rust。

但是如果我要使用生成的结构 w/ Diesel，那么我必须打破 protobuf 编译 w/一些手动步骤，以额外添加正确的注释，因为下一个`cargo build`重新生成代码并删除我的手动更改。这是一个危险信号，但我还是继续前进...

Diesel 还希望您的 struct 字段与您的表模式是 1:1，以便使用定制的`#[Derive(Queryable)]`来查询 DB。如果你没有看过`grpc-rs`生成的 grpc 代码，你会看到额外的内部使用的结构字段:`unknown_fields`和`cached_size`。这些是`grpc-rs`实现消息序列化/反序列化的一部分。向前发展可能需要在数据库中表示这些额外的字段，这有一种不好的味道并且浪费空间。

**grpc-RS 生成的带特殊字段**
的 Rust 代码示例

```
#[derive(PartialEq,Clone,Default)]
pub struct OrderForm {
    // message fields
    pub quantity: i32,
    pub product: OilProductType,
    // special fields
    pub unknown_fields: ::protobuf::UnknownFields,
    pub cached_size: ::protobuf::CachedSize,
} 
```

Enter fullscreen mode Exit fullscreen mode

> 选择直接处理这个生成的结构意味着手动修改 derive()的列表，并处理特殊字段`unknown_fields`和`cached_size`，这样 Diesel 仍然可以用于插入和查询。可能需要在表模式中添加列。在我的 protobuf 库和数据库中的数据之间，这是一种比我想要的更紧密的耦合。

### 我应该做什么

我是在使用原始原型结构编写客户机/服务器之后才意识到这一点的。然后，我开始设计数据库模式和迁移。我在尝试将 grpc 客户端调用传递给 db inserts 时遇到了困难。

我的结论是，我需要创建只有 Diesel 才会使用的新结构，因为它们的支持严重依赖于派生代码。这一切感觉就像阻抗不匹配，我不得不重新做同样的工作，没有一个清晰的路径，我要去哪里。

这是一次失败。如果我可以从数据库插入逆向工作到原型，那么对我的理解来说这可能会更好。

## 我的第二种方法

### 实施前

我还在学习如何写地道的 Rust。当我将我的 protos 编译成生成的 Rust 代码，并假设我需要直接使用它，因为它是本机代码，尽管我不熟悉 Pingcap 的 gRPC 库生成的所有代码。

> 我非常依赖使用 Into trait 来创建一个小小的反腐败层，这样我的客户机和服务器上的业务逻辑都不会直接在 protobuf 生成的结构上操作。凯文·霍夫曼

在粗略地阅读了凯文的霍夫曼的帖子后，我注意到他用同样的方式描述了这一点。如果没有一些示例代码或图表，我还不能理解这个警告。

#### 为业务逻辑使用单独的结构

我没有立即考虑我可能想要编写自己的结构，而不是使用 protobuf 生成的结构，因为我的想法是生成的代码将足够符合人类工程学以使用代码。

然而，使用独立结构的策略提供了非常容易使用的转换，因为有了`From`和`Into`特征。这对于代码的可维护性和可读性来说更容易，因为我可以将转换逻辑包含在远离业务逻辑的地方。

我可以用`.into()`在 protobuf 生成的格式和 diesel 支持的格式之间来回转换。这是如何实现的？

在实施过程中对此有更多的了解...

##### 在代码中使用这种模式是什么样子的？

交互示例如下所示

插入数据库-客户端:

```
1\. User input 
2\. Create Diesel struct + any data manipulation 
3\. Convert Diesel struct into Proto struct 
4\. Send Proto struct in gRPC call 
```

Enter fullscreen mode Exit fullscreen mode

在数据库服务器端插入:

```
1\. Receive Proto struct
2\. Convert Proto struct into Diesel struct + Any data manipulation
3\. Insert into DB 
```

Enter fullscreen mode Exit fullscreen mode

#### 最后一个复杂的细节:Rust 自定义类型映射到 Postgres 枚举

我想使用 Rust 枚举和 Postgres 枚举将我对类型的使用一直带到 DB insert/query。diesel 模式生成器不能很好地处理自定义 postgres 枚举，但是我们可以通过使用一些 Diesel 派生来手工管理转换:`SqlType`、`FromSql`和`ToSql`。我可能会在另一篇文章中介绍使用自定义 postgres 类型和 Diesel。但是现在，我将手挥这个细节。

Diesel-rs 定制类型测试对我解决这个问题非常有用。

#### 将代码组织到货物工作区

有了一些经验和对代码中应该用 crate 分隔的相关域的更好理解，我想在编写新代码之前进行组织。我做的第一件事是将代码库分成[工作区](https://doc.rust-lang.org/book/ch14-03-cargo-workspaces.html#creating-a-workspace)。

分成不同的箱子可以让我组织结构转换代码，避免使业务逻辑代码的可读性复杂化。这将使得通过导入板条箱在客户端和服务器端之间重用模式变得更加容易。

### 实现

#### 编写数据库模式

因为我需要某种类型的故事来编写代码，所以我决定编写一个石油订购系统(因为 proto-diesel 可以被描述为石油…哈哈…)

我的 postgres 类型`oil_product`有一个从炼油厂的 [wiki 页面得到的石油衍生产品的](https://en.wikipedia.org/wiki/Oil_refinery#Major_products)[饼状图](https://en.wikipedia.org/wiki/Oil_refinery#/media/File:Usesofpetroleum.png)

这帮我做了第一件事:我需要我的数据库模式- [schema.rs](https://github.com/tjtelan/rust-examples/blob/master/cli-clap-grpc-pingcap-db-diesel/workspace/models/schema.rs)

然后我可以写我的迁移:

*   [up.sql](https://github.com/tjtelan/rust-examples/blob/master/cli-clap-grpc-pingcap-db-diesel/workspace/migrations/2019-03-18-213310_create_orders/up.sql)
*   [down.sql](https://github.com/tjtelan/rust-examples/blob/master/cli-clap-grpc-pingcap-db-diesel/workspace/migrations/2019-03-18-213310_create_orders/down.sql)

#### 获取插入数据库工作

第二个是在后端工作的数据库中插入- [链接到特定的提交](https://github.com/tjtelan/rust-examples/commit/0e40e27529170b22f5419559ce8659f7a1a154f3#diff-149a61a7aa6246849298372d0b2f196e)

**后端. rs**

这是一个从后端到执行 DB insert 的内部函数的简单调用。打开一个连接后，我测试创建一个硬编码订单。

```
[...]
let conn = client::establish_connection();
let new_order = client::create_order(&conn, 1, schema::OilProductEnum::DIESEL);
[...] 
```

Enter fullscreen mode Exit fullscreen mode

**创建 _ 订单**

因为 id 被设置为`1`，所以该插入只工作一次。但是结果是在数据库中插入一个订单，并从函数中返回插入的`Order`。

```
pub fn create_order(conn : &PgConnection, quantity : i32, product_type : OilProductEnum) -> Order {
    let new_order = vec![
        Order {
            id : 1,
            quantity : quantity,
            product_type : product_type,
        },
    ];

    diesel::insert_into(orders::table)
        .values(&new_order)
        .get_result(conn)
        .expect("Error saving new order")
} 
```

Enter fullscreen mode Exit fullscreen mode

##### 为业务逻辑创建用户输入结构

我创建了一些只接受用户输入的结构。它将被转换成用于 gRPC 调用的原型形式

这些结构不包含 id 或时间戳之类的动态信息，因为它们是在服务器端插入之前生成的。

需要专门定义单独的原型消息，以便从客户端获取用户输入。

**业务逻辑结构之一**

```
pub struct OrderForm {
    pub quantity : i32,
    pub product_type : OilProductEnum,
} 
```

Enter fullscreen mode Exit fullscreen mode

**对应的 proto 消息定义**

```
message OrderForm {
    int32 quantity = 2;
    OilProductType product = 3;
} 
```

Enter fullscreen mode Exit fullscreen mode

##### 将业务逻辑结构转换为原型生成的结构

我实现了`From`特征，为 grpc 客户端调用将我的自定义类型转换为 protobuf 类型(反之亦然)。来自的[特征免费为我们提供了进入](https://doc.rust-lang.org/std/convert/trait.From.html)的[实现。](https://doc.rust-lang.org/std/convert/trait.Into.html) 

```
// Convert from the protos to our type
impl From<refinery::OrderForm> for OrderForm {
    fn from(proto_form : refinery::OrderForm) -> Self {
        OrderForm {
            quantity : proto_form.get_quantity(),
            product_type : OilProductEnum::from(proto_form.get_product()),
        }
    }
}

// Convert from our type to the proto
impl From<OrderForm> for refinery::OrderForm {
    fn from(rust_form : OrderForm) -> Self {
        let mut order = refinery::OrderForm::new();

        order.set_quantity(rust_form.quantity);
        order.set_product(refinery::OilProductType::from(rust_form.product_type));
        order
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

来自 [convert.rs](https://github.com/tjtelan/rust-examples/blob/master/cli-clap-grpc-pingcap-db-diesel/workspace/models/convert.rs) 的片段:

#### 在进行 gRPC 调用之前接受用户输入

我们希望从客户端获取用户输入，进行 grpc 后端调用，然后从后端插入到 db 中。

我们已经处理了与原型形式之间的转换，所以现在重点放在控制流上。

##### 移动插入到 gRPC 服务器端点

在从客户端调用 grpc 服务器端点之后，将发生插入。

在客户端，我用默认值创建了一个 protobuf 生成的结构，用于演示对后端工程的 gRPC 调用。事后我可以很容易地接受用户的输入。

##### 接收原型结构并转换成数据库可插入结构

最后，我接受用户输入，并用它来实例化我的一个自定义类型。在 grpc 后端调用期间，我调用。into()，它将转换为 protobuf 形式。在后端，我接受请求，并调用`.into()`将其转换回我的类型，这样我就可以封送到 diesel 插入调用中。

**服务器端**

我正在通过调用`.into()`将原型结构`req`转换成业务逻辑形式`OrderForm`。因为`create_order()` impl 接受了`OrderForm`，所以没有必要用`.into()`来注释类型，我们能够保持专注。

```
#[derive(Clone)]
struct RefineryService;

impl Refinery for RefineryService {
    // The client-side converts to refinery::OrderForm while calling this endpoint.
    // But we convert the proto type back to our custom type right before adding to the database
    fn order(&mut self, ctx: RpcContext, req: refinery::OrderForm, sink: UnarySink<refinery::OrderStatus>) {

    // Creating the return object
        let order_status = client::order_received_success();

        let f = sink
            .success(order_status.clone())
            .map(move |_| println!("Responded with status {{ {:?} }}", order_status))
            .map_err(move |err| eprintln!("Failed to reply: {:?}", err));

        let conn = client::establish_connection();
        // Convert the received proto request into our native type
        let _new_order = client::create_order(&conn, req.into());

        ctx.spawn(f)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

**订单创建功能**

我们采用业务逻辑表单`order_form`并使用它创建可插入的结构`new_order`,其中包含 Diesel 要执行的所有列值。

```
pub fn create_order(conn : &PgConnection, order_form : OrderForm) -> Order {
    let timestamp = NaiveDateTime::from_timestamp(Utc::now().timestamp(),0);

    let new_order = vec![
        NewOrder {
            quantity : order_form.quantity,
            product_type : order_form.product_type,
            received_time : timestamp,
        },
    ];

    diesel::insert_into(orders::table)
        .values(&new_order)
        .get_result(conn)
        .expect("Error saving new order")
} 
```

Enter fullscreen mode Exit fullscreen mode

##### 再做一次，反过来，进行查询

最后一项任务是重复所有这些工作，但要进行查询。

这与实现`From`特征略有不同，因为我返回的是订单列表，而 from 特征显然不容易实现 Vec 到 protobuf Rust 等价物。如果我计划将这段代码发布到除了演示之外的地方，我可能会花更多的时间来实现`From`。我最终变得懒惰起来，将手动转换包装在一个函数中，该函数循环使用我已经在`Order`类型上实现的 From 特征。

**用户输入端**

cli 中的这个客户机子命令请求数据库中的所有订单，然后打印出 protobuf 表单作为演示。下一步是将 protobuf 列表转换成某种非 protobuf 生成类型的 Vec。

```
if let Some(_matches) = matches.subcommand_matches("summary") {
    let empty_payload = protos::empty::Empty::new();

    // Send the gRPC message
    let orders = client.get_all_records(&empty_payload).expect("RPC Failed!");

    // Print all records from database
    println!("Order status: {:?}", orders);
} 
```

Enter fullscreen mode Exit fullscreen mode

**服务器端点**

服务器接受一个空的原型类型，所以我们不需要做任何类型转换。然后我们调用一个函数`client::get_all_orders()`，这个函数调用 Diesel 返回一个表中的所有数据。然后我们进行另一个函数调用`client::db_query_to_proto()`，将我们的本地数据转换成 gRPC 可发送的形式。

```
fn get_all_records(&mut self, ctx: RpcContext, _req: protos::empty::Empty, sink: UnarySink<refinery::OrderRecordList>){
    println!("Received request for all of the order records");
    let conn = client::establish_connection();

    // Call out to db
    let query_results = client::get_all_orders(&conn);

    // This conversion pattern is different than the plain `From` traits, because we
    // have to handle the outer vector in a special way, but I want to be lazy
    let parsed_query_proto = client::db_query_to_proto(query_results);
    //println!("Got results from the database: {:?}", query_results);

    let f = sink
        .success(parsed_query_proto.clone())
        .map(move |_| println!("Responded with list of records {{ {:?} }}", parsed_query_proto))
        .map_err(move |err| eprintln!("Failed to reply: {:?}", err));

    ctx.spawn(f)
} 
```

Enter fullscreen mode Exit fullscreen mode

**数据库查询**

该函数查询 orders 表中的所有内容。这里没有什么有趣的，因为柴油处理一切。我只需要标注 Diesel 将要返回的向量的类型。

```
// get_all_orders is used by the backend
pub fn get_all_orders(conn : &PgConnection) -> Vec<Order> {
    let query : Vec<Order> = orders::table.select(orders::all_columns)
    .order_by(orders::id)
    .load(conn)
    .expect("Error getting all order records");
    query
} 
```

Enter fullscreen mode Exit fullscreen mode

**查询 protobuf 列表**

你不需要预先使用所有的 Rust 特性，或者根本不使用 Rust。我们都希望能够理解这一点。

我曾短暂地尝试为`Vec<Order>`实现`From`，但是很明显这会比我现在愿意花费的精力多一点。我第一个承认这是一个小技巧，但是对于演示来说这很好。

Rust 代码中的 Protobuf 的`repeated`关键字有自己的类型，比如叫做`RepeatedField`的`Vec<T>`，我们只是循环遍历并创建一个`Vec<refinery::OrderRecord>`，这样我们就可以使用转换 impl `from_vec`。剩下的是构建返回数据。

```
// db_query_to_proto is used by the backend to convert a Vector of Order (from a Diesel select
// query) into the proto native OrderRecordList. Implementing `From` for a Vector would have taken
// longer, and used a wrapper type. That very well may be the more maintainable approach, but this
// was quicker…

pub fn db_query_to_proto(rust_record : Vec<Order>) -> refinery::OrderRecordList {
    let mut proto_vec : Vec<refinery::OrderRecord> = Vec::new();

    // Let's take advantage of the `From` trait
    for r in rust_record {
        proto_vec.push(refinery::OrderRecord::from(r));
    }

    let proto_order = protobuf::RepeatedField::from_vec(proto_vec);

    let mut proto_final = refinery::OrderRecordList::new();
    proto_final.set_order(proto_order);
    proto_final
} 
```

Enter fullscreen mode Exit fullscreen mode

## 总之

Rust library 对 gRPC 的支持在这里。用 Diesel-rs 支持 ORM 已经有一段时间了。但是如果你想在同一个项目中使用 gRPC 和 Diesel，也许你可以借鉴我的经验，并且富有成效。

不要在你的代码库中主要使用 protobuf 生成的 Rust 类型。尤其是如果您计划使用 Diesel-rs 来处理数据库插入/查询，因为为了使用 Diesel-rs 的最佳体验，结构需要与表模式 1:1。

实现`From` / `Into`特征，以便更有效地在业务逻辑结构和 protobuf 生成的 Rust 结构之间进行转换。

转换到 proto Rust 类型只是为了通过 gRPC 调用发送/返回数据，然后在接收端立即转换回您的业务逻辑类型。

不需要一口气写出完美的 Rust 代码。

* * *

这篇文章中使用的代码位于[这里](https://github.com/tjtelan/rust-examples/tree/master/cli-clap-grpc-pingcap-db-diesel)。
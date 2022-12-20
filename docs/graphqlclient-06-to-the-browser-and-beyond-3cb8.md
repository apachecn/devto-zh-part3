# graphql_client 0.6，适用于浏览器及其他

> 原文：<https://dev.to/tomhoule/graphqlclient-06-to-the-browser-and-beyond-3cb8>

[graphql_client](https://github.com/graphql-rust/graphql-client) 今天发布了新版本！最明显的新特性是一个新的 [graphql_client_web](https://github.com/graphql-rust/graphql-client/tree/master/graphql_client_web) 机箱，它使得从运行在浏览器中的 Rust 代码调用 GraphQL API 成为无样板文件。

另一个值得注意的变化是 GitHub 上的 graphql-rust 组织，在那里我们可以与其他 graphql 机箱一起共享文档和品牌工作。感谢 graphql-rust 团队对 graphql_client 的支持和反馈！

## Web 客户端

现在，在浏览器中调用 WebAssembly 代码中的 GraphQL API 就像这样简单:

*   添加 graphql_client_web 作为依赖项。
*   用`Client::new("<GraphQL endpoint URL>")`构造一个客户端。
*   用普通的 graphql_client 查询结构和查询变量结构调用它。
*   您会得到一个返回强类型响应或客户端错误的 Rust future。

这就是它的样子(直接从测试套件中复制粘贴):

```
#[derive(GraphQLQuery)]
#[graphql(
    schema_path = "tests/countries_schema.json",
    query_path = "tests/Germany.graphql"
)]
struct Country;

#[wasm_bindgen_test(async)]
fn test_country() -> impl Future<Item = (), Error = JsValue> {
    Client::new("https://countries.trevorblades.com/")
        .call(
            Country,
            country::Variables {
                country_code: "CN".to_owned(),
            },
        )
        .map(|response| {
            let continent_name = response
                .data
                .expect("response data is not null")
                .country
                .expect("country is not null")
                .continent
                .expect("continent is not null")
                .name
                .expect("country is on a continent");

            assert_eq!(continent_name, "Asia");
        })
        .map_err(|err| {
            panic!("{:?}", err);
        })
} 
```

网络(通过 fetch API)和序列化/反序列化由 graphql_client_web 负责。

此机箱仅适用于 wasm-bindgen 生态系统(web-sys，js-sys)，如果有需求，可以研究对 stdweb 的支持。

这是第一次发布，所以预计会有一些小的突破性变化，尤其是在错误处理方面。

## CLI

@h-michael 一直在推动使用 CLI 生成代码，它正在迅速形成新的特性，例如:

*   支持查询文档定义多个查询
*   使用 rustfmt 对输出代码进行可选格式化

## 其他修复和改进

还有一些其他的新特性、错误修复和突破性的(命名)改变。您可以查看一下[变更日志](https://github.com/graphql-rust/graphql-client/blob/master/CHANGELOG.md)中的完整变更列表。

## 下一步

[![reform the ninja empire](img/de8f111fb514a08c15466e2b20f3c86b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Xai4uwk1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yznas6zbllpzftsfef4w.jpg)

该库现在处于非常可用的状态，我们可以开始考虑 1.0 版本了。在这些问题中有许多增强的想法，但是核心抽象是稳定的，所以一旦文档处于更好的状态，我们不应该太害怕采取这一步骤。

## 感谢

一如既往，衷心感谢[每一个为这个箱子的诞生做出贡献的人！](https://github.com/graphql-rust/graphql-client#contributors)
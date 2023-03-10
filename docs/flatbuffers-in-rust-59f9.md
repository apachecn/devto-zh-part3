# 平板缓冲器(生锈)

> 原文：<https://dev.to/jeikabu/flatbuffers-in-rust-59f9>

继[上一篇结束 Apache Thrift 章节的文章](//./thrift-012-1mi9)之后，我们来看看另一个序列化库 [FlatBuffers](https://google.github.io/flatbuffers/) 。

## 平板缓冲器

从使用 Thrift 中得到的一个教训是，我们想要高性能、模式化的序列化和健壮的消息传递，但是*而不是*紧密耦合在一起。我们最终使用了消息组帧和其他特性，但是[使用 ZeroMQ 作为传输](//./apache-thrift-over-zeromq-66e)并实现了 pub/sub 的消息识别(与 Thrift 的请求/回复模型相反)。

其他反对节俭的行为我们直到后来才发现。C++客户端有一个 [boost](https://www.boost.org/) 依赖项，需要异常和 [RTTI](https://en.wikipedia.org/wiki/Run-time_type_information) 。增强你的爱或恨，但最后两个基本上是禁止在视频游戏行业。举个例子，让我们的 [SDK 在](https://github.com/subor/sample_ue4_platformer) [UE4](https://www.unrealengine.com/) 中工作是一件麻烦事[。](https://github.com/subor/sdk/blob/master/docs/topics/ue4.md)

潜在的更好的性能和减少的内存流失促使我们评估像 [Cap'n Proto](https://capnproto.org/) 这样的选项，我们倾向于 [FlatBuffers](https://google.github.io/flatbuffers/) 。注意事项:

*   FlatBuffers 得到了谷歌的支持
*   C#支持充其量也是可疑的
*   船长很可能在性能上有优势([【0】](https://github.com/thekvs/cpp-serializers)，[【1】](https://github.com/felixguendling/cpp-serialization-benchmark))
*   都有一流的支持[锈](https://www.rust-lang.org/) ( [平头](https://github.com/google/flatbuffers#supported-programming-languages)， [Cap'n Proto](https://github.com/capnproto/capnproto-rust) )
*   [Unity3D](https://unity3d.com/) 支持:FlatBuffers 的 C#库[目标。NET 3.5](https://github.com/google/flatbuffers/blob/master/net/FlatBuffers/FlatBuffers.csproj#L12) 和[“刚刚好”](http://exiin.com/blog/flatbuffers-for-unity-sample-code/)。船长有一个[休眠的 github 项目](https://github.com/ThomasBrixLarsen/capnproto-dotnet)。
*   FlatBuffers 在视频游戏行业更受欢迎([谷歌](https://developers.google.com/games/#Tools)， [cocos2d-x](https://cocos2d-x.org/reference/native-cpp/V3.5/d7/d2d/namespaceflatbuffers.html) )

最后两个对我们来说是重要的决定因素。开发者已经在使用 FlatBuffers 的可能性更大，这是我们的 SDK 将引入的一个更少的依赖性。

## 铁锈中的扁担

[文档](https://docs.rs/flatbuffers/0.5.0/flatbuffers/)有点轻，虽然他们在 FlatBuffers 文档中有[“防锈使用”指南](https://google.github.io/flatbuffers/flatbuffers_guide_use_rust.html)。

我们没有按照说明(我知道，我知道)使用`HEAD`，而是使用了[最新的稳定版本，1.10.0](https://github.com/google/flatbuffers/releases) 。

FlatBuffers 模式`protos/bench.fbs` :

```
namespace bench;

table Basic {
  id:ulong;
}

table Complex {
  name:string (required);
  basic:bench.Basic (required);
  reference:string (required);
} 
```

Enter fullscreen mode Exit fullscreen mode

运行`flatc --rust -o src protos/bench.fbs`生成包含两个结构`bench::Basic`和`bench::Complex`的锈源`src/bench_generated.rs`。

然后我们可以序列化/反序列化`bench::Basic`结构:

```
use crate::bench_generated as bench_fbs;

#[test]
fn it_deserializes() {
    const ID: u64 = 12;
    let mut builder = FlatBufferBuilder::new();
    // bench::Basic values that will be serialized
    let basic_args = bench_fbs::bench::BasicArgs { id: ID, .. Default::default() };
    // Serialize bench::Basic struct
    let basic: WIPOffset<_> = bench_fbs::bench::Basic::create(&mut builder, &basic_args);
    // Must "finish" the builder before calling `finished_data()`
    builder.finish_minimal(basic);
    // Deserialize the bench::Basic
    let parsed = flatbuffers::get_root::<bench_fbs::bench::Basic>(builder.finished_data());
    assert_eq!(parsed.id(), ID);
} 
```

Enter fullscreen mode Exit fullscreen mode

注意事项:

*   这里不需要`.. Default::default()`，但是显示 [`impl Default`](https://doc.rust-lang.org/std/default/trait.Default.html) 也是生成的
*   `create()`序列化该结构并返回一个`WIPOffset<bench::Basic>`,它是数据根的偏移量
*   文档用非 flatc 1.10 生成的`get_root_as_XXX()`函数反序列化(需要 HEAD？)[却似乎被](https://github.com/google/flatbuffers/issues/5000)裹住了`get_root()`。

对于`bench::Complex`结构:

```
use crate::bench_generated as bench_fbs;

#[test]
fn it_deserializes() {
    const ID: u64 = 12;
    const NAME: &str = "name";
    const REFERENCE: &str = "reference";
    let mut builder = flatbuffers::FlatBufferBuilder::new();
    {
        let args = bench_fbs::bench::BasicArgs{id: ID};
        let basic = Some(bench_fbs::bench::Basic::create(&mut builder, &args));
        let name = Some(builder.create_string(NAME));
        let reference = Some(builder.create_string(REFERENCE));
        let args = bench_fbs::bench::ComplexArgs{ basic, name, reference };
        let complex = bench_fbs::bench::Complex::create(&mut builder, &args);
        builder.finish_minimal(complex);
    }
    let parsed = flatbuffers::get_root::<bench_fbs::bench::Complex>(builder.finished_data());
    assert_eq!(parsed.basic().id(), ID);
    assert_eq!(parsed.name(), NAME);
    assert_eq!(parsed.reference(), REFERENCE);
} 
```

Enter fullscreen mode Exit fullscreen mode

注意事项:

*   需要手动序列化`bench::Complex`的每个成员既麻烦又容易出错。好像没有自动处理的方法…

## 基准

上面的模式来自于比较 protobuf 和 capnproto 的 [proto_benchmarks](https://github.com/ChrisMacNaughton/proto_benchmarks) 存储库。我偶然发现了它，并为它漂亮的[标准](https://github.com/bheisler/criterion.rs)情节而神魂颠倒。[我分叉了](https://github.com/jeikabu/proto_benchmarks)添加 FlatBuffers，迁移到 [Rust 2018](https://blog.rust-lang.org/2018/07/27/what-is-rust-2018.html) 。

flatbuffers 模式实际上是从 protobuf 模式自动生成的:

```
syntax = "proto2";
option optimize_for = SPEED;

package bench;

message Basic {
    required uint64 id = 1;
}

message Complex {
    required string name = 1;
    required Basic basic = 2;
    required string reference = 3;
} 
```

Enter fullscreen mode Exit fullscreen mode

在`build.rs` :

```
// Convert protobuf .proto to FlatBuffers .fbs
std::process::Command::new("flatc")
    .args(&["--proto", "-o", "protos", "protos/bench.proto"])
    .spawn()
    .expect("flatc");
// Generate rust source
std::process::Command::new("flatc")
    .args(&["--rust", "-o", "src", "protos/bench.fbs"])
    .spawn()
    .expect("flatc"); 
```

Enter fullscreen mode Exit fullscreen mode

使用 [`std::process::Command`](https://doc.rust-lang.org/std/process/struct.Command.html) 执行`flatc`。首先，将 protobuf 模式转换成 FlatBuffers，然后输出 Rust source。

### 结果

不完全确定我相信结果。由于对这三个库都不太了解，我们可能会比较稍微不同的东西。

基本写:

[![](img/f6e0baa9e11b62b6fbed42d59f7a5a1e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VE43Mhmu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rendered-obsolete.github.io/assets/flatbuffers_rust_basic_write.png)

复杂的构建:

[![](img/f31d6f911502516084472c55b3dd587f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8knrurDG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rendered-obsolete.github.io/assets/flatbuffers_rust_complex_build.png)

还发现[rust-serialization-benchmarks](https://github.com/erickt/rust-serialization-benchmarks)已经 3 年没有更新，似乎使用 [Bencher](https://doc.rust-lang.org/1.1.0/test/struct.Bencher.html) 进行基准测试。
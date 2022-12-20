# 高级 TypeScript 在现实生活中的功能

> 原文：<https://dev.to/ninjah187/advanced-typescript-s-capabilities-in-real-life-scenario-593l>

## 简短

在本文中，我将介绍如何为 HTTP 天气数据 API 构建静态类型、动态类型脚本客户端。这是我在工作中遇到的一个现实案例。

本文主要讨论 TypeScript 的类型合成和推断能力。我将描述如何利用这些功能来定义静态类型的、动态组合类型的天气数据度量模型。

### 代码示例

我已经创建了这个 [StackBlitz](https://stackblitz.com/edit/typescript-gzcx6q) ,将文章中提到的所有代码都连接起来，因此您可以看到这个模式是如何工作的，并自己进行试验。

## 简介

我最近在研究远程气象站系统。每个站的任务是不断收集气象数据。度量被发送到服务器并存储在数据库中，等等...但现在情况完全不同了。

让我们把重点放在天气数据上。

单一天气指标由值组成，定义如下:

```
timestamp: Date;
temperature: number;
pressure: number;
humidity: number;
rain: number;
sun: number;
windDirection: number;
windVelocity: number;
dew: number; 
```

在工作中，我遇到了有趣的问题。我需要读取选择了几个或所有值的指标，最好在单次数据库往返中检索。因此，我构建了 HTTP API 端点，允许我这样做。端点给了我很大的灵活性，我真的很喜欢，但我很难在消费者中实现同样的整洁。

跳过一些细节，为了检索站的度量，服务器期望参数位标志`MetricValue`指示消费者想要接收的值。然后，服务器返回所选的值以及打包成 JSON 数组的相应时间戳。这种方法允许我重用相同的度量端点，而不需要单独实现特定的用例。

`MetricValue`TypeScript 中的位标志定义如下:

```
enum MetricValue {
  Temperature = 1 << 0,
  Pressure = 1 << 1,
  Humidity = 1 << 2,
  Rain = 1 << 3,
  Sun = 1 << 4,
  WindDirection = 1 << 5,
  WindVelocity = 1 << 6,
  Dew = 1 << 7
} 
```

请求示例:

```
> GET metrics/1
// 1 === MetricValue.Temperature
[{timestamp: "00:01", temperature: 20.2}, {timestamp: "00:02", temperature: 20.3}, ...]

> GET metrics/3
// 3 === MetricValue.Temperature | MetricValue.Pressure
[{timestamp: "00:01", temperature: 20.2, pressure: 1013}, {timestamp: "00:02", temperature: 20.3, pressure: 1013.1}, ...] 
```

很酷，不是吗？但是我们怎么才能对这种生物进行分型呢？

## 定义 API 客户需求

公平地说，我的第一个想法，也可能行得通，是像下面这样键入 single`MetricDto`:

```
interface MetricDto {
  readonly timestamp: string;
  readonly temperature?: number;
  readonly pressure?: number;
  // other optional values ...
} 
```

就像我说的，这可能很好，但它并不完全令人满意，因为它丢失了我确切检索的值的编译时信息。所有这些可空值都很糟糕，会导致错误或太多的检查。

哦，我最近花了太多的时间阅读花哨的 TypeScript 的类型系统特性，不能就这样算了。

我决定提出一些更严格和可组合的东西，我将向你展示我发现的一切。

## 将合同声明分解成组件

首先，我决定将我的 MetricDto 分解成独立的接口。每个值一个。

```
interface TimestampDto {
  readonly timestamp: string;
}

interface TemperatureDto {
  readonly temperature: number;
}

interface PressureDto {
  readonly pressure: number;
}

// ... 
```

注意，我将特定的属性声明为非可选的，而不是最初的`MetricDto`声明。这允许我在不同的场景中重用上述接口，而无需重新声明 API 契约。

通过将原始的`MetricDto`声明分解为单值接口，我们可以将`MetricDto`输入为:

```
type MetricDto =
    TimestampDto
  & Partial<TemperatureDto>
  & Partial<PressureDto>
  & Partial<HumidityDto>
  & Partial<RainDto>
  & Partial<SunDto>
  & Partial<WindDirectionDto>
  & Partial<WindVelocityDto>
  & Partial<DewDto>; 
```

我在这里使用了名为`Partial<T>`的预定义类型，它使得泛型参数`T`的所有属性都被标记为可选。

顺便说一下，我们可以编写像`Partial<T>`这样的自定义映射类型，但是在这种情况下我们不需要任何类型。

结果，我们得到了与初始模型等价的模型`MetricDto`。不同的是，新的更容易组合。

好吧，你可能会说，这很酷，但它基本上没有给我们带来什么新东西。我们用一些花哨的东西重写了相同的代码，但是我们没有得到任何真正的好处，除了一些关于可组合性的模糊承诺。

你说得对，是时候把事情推进一点了。

## 将度量值标志映射到其对应的 DTO 类型

当我想知道我还能做什么的时候，我发现我可以使用枚举值作为类型。这样做的条件是只声明常量枚举值，所以我必须更改`MetricValue`声明以包含预先计算的标志值。

```
enum MetricValue {
  Temperature = 1,
  Pressure = 2,
  Humidity = 8,
  Rain = 16,
  Sun = 32,
  WindDirection = 64,
  WindVelocity = 128,
  Dew = 256
} 
```

也许这不是最方便的，但对于我们即将实现的目标来说，这不仅仅是一个公平的交换。正如我提到的，这种变化让我们可以使用枚举值作为类型。所以，我们现在可以做这样的事情:

```
type Temperature = MetricValue.Temperature; 
```

我发现我可以使用这个特性和条件类型来映射`MetricValue`和它对应的`*Dto`接口。

```
type Dto<T extends MetricValue> =
    T extends MetricValue.Temperature ? TemperatureDto
  : T extends MetricValue.Pressure ? PressureDto
  : T extends MetricValue.Humidity ? HumidityDto
  : T extends MetricValue.Rain ? RainDto
  : T extends MetricValue.Sun ? SunDto
  : T extends MetricValue.WindDirection ? WindDirectionDto
  : T extends MetricValue.WindVelocity ? WindVelocityDto
  : T extends MetricValue.Dew ? DewDto
  : never; 
```

用法示例:

```
type Result = Dto<MetricValue.Temperature>;

// Result is TemperatureDto. 
```

嗯，这很有趣，但它仍然不是太动态。我们真正想要的是一种方法，在编译时找出哪个`MetricValue`的消费者正在查询，从而确定返回的 DTO 类型。

## 神奇的事情发生了——从度量值类型的一般元组中推断 DTO 类型

经过一段时间的熟悉和使用`infer`关键字进行类型推断的努力，我终于想到了下面的解决方案:

```
type UnionDto<T extends MetricValue[]> = T extends (infer U)[] ? (U extends MetricValue ? Dto<U> : never) : never; 
```

这个映射所做的是获取`MetricValue`类型的元组，然后从元组类型创建映射的`*Dto`类型的并集。最好看例子:

```
type Result = UnionDto<[MetricValue.Temperature, MetricValue.Pressure]>;

// Result is TemperatureDto | PressureDto. 
```

现在我们有所进展。然而，联合并不能满足我们的要求。交集是我们需要的。幸运的是，这不是问题，因为我们可以立即将并集转换为交集。

```
type UnionToIntersection<T> = (T extends any ? (x: T) => void : never) extends ((x: infer U) => void) ? U : never; 
```

我们现在可以创建正确解析`MetricValue`到组合`Dto`的类型。

```
type CompositeDto<T extends MetricValue[]> = UnionToIntersection<UnionDto<T>>; 
```

这给了我们做我们想做的事情的能力——从作为参数传递的`MetricValue`中推断出结果`Dto`。

```
type Result = CompositeDto<[MetricValue.Temperature, MetricValue.Pressure]>;

// Result is TemperatureDto & PressureDto. 
```

## 把东西接线起来

我们终于拥有了我们一直追求的模型类型所需的一切。我们来看看。

```
type Metric<T extends MetricValue[]> = TimestampDto & CompositeDto<T>; 
```

`Metric<T>`是表示从 API 获取的指标的类型，它由强制时间戳组成，并与表示特定指标值的附加`Dto`相交，这些值是从传递的`MetricValue` s.
中推断出来的

```
type Result = Metric<[MetricValue.Temperature, MetricValue.Pressure]>;

// Result is TimestampDto & TemperatureDto & PressureDto. 
```

这是一大堆理论，但是我们如何在实践中实际运用它呢？

## 构建 API 客户端

最后，我们可以组装 API 客户端。为了简单起见，我将模糊实现细节，但关键是类似于下面的函数:

```
function fetchMetrics<T extends MetricValue[]>(stationId: number, ...metrics: T): Promise<Metric<T>[]> {
  // Details of request...
} 
```

```
// 'metrics' is array of TimestampDto & TemperatureDto & PressureDto.
fetchMetrics(stationId, MetricValue.Temperature, MetricValue.Pressure).then(metrics =>
  // IDE hints and static typing goodness available without writing any explicit types!
  for (const metric of metrics) {
    console.log(metric.temperature, metric.pressure);
  }
}); 
```

整洁！

我们的`fetchMetrics`函数是静态类型的，它返回的 DTO 类型是根据我们请求的度量值动态组合的。

我们构建了强大的客户端，现在可以在许多用例中重用，而不需要处理一大堆可空属性，也不需要为特定用例手动编写缩小的类型。

如果这不酷，那我就不知道什么是酷了。

## 总结

自从我开始尝试 TypeScript 以来，我就听说了它丰富的打字特性。我必须承认，直到这一刻我才意识到打字功能其实很丰富。

这种模式的应用肯定不止于此。如果你发现更多像这样聪明的东西，请分享。

希望你喜欢我的想法。我热衷于讨论任何与主题相关的事情。
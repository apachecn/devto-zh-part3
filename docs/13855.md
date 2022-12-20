# 在 Swift 中解码格式化的 JSON 日期

> 原文：<https://dev.to/jrtibbetts/decoding-formatted-json-dates-in-swift-2odb>

如果你在过去十年中做过网络、移动或桌面编程(如果没有，你还会在这里吗？)，您已经使用 JSON——跨设备和平台序列化数据的通用语言。JSON 的流行在于它的简单性:它只支持少数几种数据类型，如字符串、数字、布尔、数组和字典，并且通常易于阅读。但是对于像 Swift 这样的强类型语言，这种简单性可能导致次优的解析策略。Xcode 9 的 Swift SDK 引入了一种优雅的方式来处理解析，那就是使用`Codable`协议。当您将一个`class`、`struct`或`enum`声明为`Codable`时，Swift 编译器会隐式生成三个东西:`init(from: Decoder)`、`encode(to: Encoder)`和一个`CodingKey`枚举，该枚举将 JSON 键值映射到您的`Codable`属性的名称。要创建您的数据类型`Codable`，您只需声明它实现了`Codable`，并确保它的所有属性本身都是`Codable`或者是受支持的类型之一:

*   `Boolean`
*   `String`
*   `Int`
*   `Float`
*   `Date`
*   `Data`
*   `URL`
*   类型的数组
*   `Codable`类词典
*   以上所有选项

默认情况下，`JSONDecoder`只能解析`Date`的原始形式——自*参考日期*:2000 年 1 月 1 日上午 12:00 UTC<sup><small>2</small></sup>起的毫秒数`TimeInterval` (= `Double`)。本文的重点是支持定制的`Date`格式，以及我从天真的实现到完全理解的旅程。

# 日出，日落

[!["Sunrise, Sunset"](img/660ea374b8d6e252b4028dff913be99a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iE6YC8tv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://content.internetvideoarchive.com/content/photos/7223/30337512_.jpg) 
[《日出，日落》出自*《屋顶上的提琴手》* (1971)](https://youtu.be/03rzUoyq9K0?t=124)

我最近一直在使用一些 REST APIs 来查找给定纬度和经度的日出和日落时间。其中一个[Sunrise-Sunset.org](https://api.sunrise-sunset.org)，用`https://api.sunrise-sunset.org/json?lat=41.948437&lng=-87.655334&date=2019-02-14` <sup><small>3</small></sup> 调用时，返回如下 JSON(为清晰起见省略了一些行):

```
{
    "results":
    {
        "sunrise":"12:59:22 PM",
        "sunset":"11:09:45 PM"
    }
} 
```

我创建了下面的`Codable`结构，我称之为

## 方法#1:天真的方法，也就是*应该如何*工作

```
public struct SunriseSunsetResponse: Codable {
    public var results: SunriseSunset
}

public struct SunriseSunset: Codable {
    public var sunrise: Date
    public var sunset: Date
} 
```

`SunriseSunset`是代表`"results"`元素值的嵌套元素，它和`SunriseSunsetResponse`都必须实现`Codable`。现在我将通过调用 API 并解析它来创建一个`SunriseSunsetResponse`的实例:

```
if let url = URL(string: "https://api.sunrise-sunset.org/json?lat=41.948437&lng=-87.655334&date=2019-02-04") {
    do {
        let responseData = try Data(contentsOf: url)
        let response = try JSONDecoder().decode(SunriseSunsetResponse.self, from: responseData)
        let sunriseSunset = response.results
        print("Daylight duration (in seconds): \(sunriseSunset.sunset.timeIntervalSince(sunriseSunset.sunrise))")
    } catch {
        print(error)
    }
} 
```

和...它失败了。错误告诉说

```
typeMismatch(Swift.Double, Swift.DecodingError.Context(
    codingPath: [CodingKeys(stringValue: "results", intValue: nil),
                 CodingKeys(stringValue: "sunrise", intValue: nil)],
    debugDescription: "Expected to decode Double but found a string/data instead.",
    underlyingError: nil)) 
```

你发现问题了吗？`sunrise`和`sunset`属性是`Date` s，但是解析器却找到了字符串`"12:59:22 PM"`和`"11:09:45 PM"`。我该如何在不操纵 JSON 数据的情况下解决这个问题呢？我是这样做的:

## 方法 2:将日期存储为`String` s

让我们重构`SunriseSunset`来期望日期*字符串*，比如

```
public struct SunriseSunset: Codable {
    public var sunrise: String
    public var sunset: String
} 
```

这就把将字符串解析成`Date`的责任转移给了调用者。不利的一面是，如果在多个地方使用了`SunriseSunset`对象，您可能会遇到许多相同的解析调用。即使您创建了一个`DateFormatter`实例并在多个地方使用它，您仍然会违反可怕的 DRY(**D**on**R**EPE at**Y**yourself)原则。一定有更好的办法。让我们试试

## 方法三:保留`String`属性，并添加相应的计算过的`Date`属性

我想简化我的`SunriseSunset`的使用方式，所以为什么不让*自己负责解析日期呢？我将添加一个`DateFormatter`属性，加上*计算出的* `Date`属性。这听起来比天真的基于`String`的方法要好，尽管`Date`版本应该是`Optional`，因为那是`DateFormatter.date(from:)`返回的结果:*

```
public struct SunriseSunset: Codable {
    private var dateFormatter: DateFormatter

    public var sunrise: String

    public var sunriseDate: Date? {
        return dateFormatter.date(from: sunrise)
    }

    public var sunset: String

    public var sunsetDate: Date? {
        return dateFormatter.date(from: sunset)
    }
} 
```

这开始看起来有点难看，但它应该符合我的目的。但是现在不会编译了！记住*你想从 JSON 转换成&的每一个`Codable`类型的*属性必须是一个本身实现`Codable`的类型，而`DateFormatter`不实现。这里*是*对此的一个变通方法，我们称之为

## 方法四:使用自定义`CodingKeys`

这种方式是为你的`Codable`类型定义一个`CodingKeys`的枚举。`CodingKeys`枚举*必须*具有原始类型`String` *和* `CodingKey`(注意单数)，以此顺序。Swift 编译器从您的属性名*中免费为您生成这个*，但是如果您的属性名*与 JSON 数据不完全*匹配，或者像在这种情况下，您不希望从 JSON 数据中解析*您的所有*属性，您必须添加自己的属性。所以现在我们来试试:

```
public struct SunriseSunset: Codable {
    private var dateFormatter: DateFormatter

    public var sunrise: String

    public var sunriseDate: Date? {
        return dateFormatter.date(from: sunrise)
    }

    public var sunset: String

    public var sunsetDate: Date? {
        return dateFormatter.date(from: sunset)
    }

    private enum CodingKeys: String, CodingKey {
        case sunrise
        case sunriseDate
        case sunset
        case sunsetDate
    }
} 
```

注意，我已经从自定义键中省略了`dateFormatter`。但同样，这不会编译。编译器显示了许多错误，其中最重要的是:

```
error: type 'SunriseSunset4' does not conform to protocol 'Decodable' 
```

公共结构 SunriseSunset4:可编码{

```
note: protocol requires initializer 'init(from:)' with type 'Decodable'
public init(from decoder: Decoder) throws

error: type 'SunriseSunset4' does not conform to protocol 'Encodable' 
```

公共结构 SunriseSunset4:可编码{

```
note: protocol requires function 'encode(to:)' with type 'Encodable'
public func encode(to encoder: Encoder) throws 
```

这些告诉你的是(不是很清楚，IMHO)如果你有属性应该*而不是*由`JSONDecoder` / `JSONEncoder`处理，那么*你*必须提供一个定制的初始化器和编码函数。[苹果的文档](https://developer.apple.com/documentation/foundation/archives_and_serialization/encoding_and_decoding_custom_types) *真的*帮助不大。上面写着，

```
> Omit properties from the `CodingKeys` enumeration if they won't be present
> when decoding instances, or if certain properties shouldn't be included in an
> encoded representation. A property omitted from `CodingKeys` needs a default
> value in order for its containing type to receive automatic conformance to
> `Decodable` or `Codable`. 
```

这个*听起来*像是你应该能够在声明的时候给`dateFormatter`分配一个`DateFormatter`实例，比如

```
> let dateFormatter = DateFormatter() 
```

但是你不能。唯一的方法是实现初始化器和编码函数。如果您认为这真的违背了使用`Codable`的初衷，即让 Swift 编译器生成`CodingKeys`、初始化器和编码函数，那么您是完全正确的。"那里*一定有*更好的办法！"我说。而且有！它是

## 方法五:`dateDecodingStrategy`用自定义`DateFormatter`

`JSONDecoder`有一个名为`dateDecodingStrategy`的属性，属于`JSONDecoder.DateDecodingStrategy`类型，允许你改变日期的解析方式。这一个`enum`有 6 个案例:

*   `deferredToDate` ( *默认*):这将`Date` s 视为`Double`值，表示该日期自参考日期以来的毫秒数(见上文)
*   `iso8601`:格式化日期的[最佳方式，例如`"2019-02-04T12:59:22+00:00"`](https://xkcd.com/1179/)
*   `formatted(DateFormatter)`:允许您使用自定义`DateFormatter`实例
*   `custom(@escaping (Decoder) -> Date)`:允许您指定用于解析的自定义块
*   `millisecondsSince1970`:类似默认的`deferredToDate`选项，但是从 [Unix 纪元](https://en.wikipedia.org/wiki/Unix_time)(即 1970 年 1 月 1 日)开始计算日期
*   `secondsSince1970`:类似`millisecondsSince1970`，但以秒为单位，而不是毫秒

谢天谢地，`"12:59:22 PM"`恰好与`DateFormatter.TimeStyle.medium`完全匹配，所以我将相应地配置我的解码器:

```
let decoder = JSONDecoder()
let dateFormatter = DateFormatter()
dateFormatter.timeStyle = .medium
decoder.dateDecodingStrategy = .formatted(dateFormatter) 
```

现在它打印出了我期望的答案:

```
Daylight duration (in seconds): 36623.0 
```

我说完了，对吧？不完全是。进一步检查返回的日期，我发现虽然*时间*是我所期望的，但是*日期*是 2000 年 1 月 1 日！如果我想计算从日出*到今天*已经过去了多少秒，那对我没有多大帮助！现在我必须将返回的时间标准化为今天的日期，这有点棘手。一种方法是获取从参考日期到今天午夜的时间间隔，并将其添加到解析的时间中。

```
let midnightThen = Calendar.current.startOfDay(for: sunriseSunset.sunrise)
let millisecondsToSunrise = sunriseSunset.sunrise.timeIntervalSince(midnightThen)

let midnightToday = Calendar.current.startOfDay(for: Date())
let normalizedSunrise = midnightToday.addingTimeInterval(millisecondsToSunrise) 
```

然而，仅仅通过使用一个定制的`DateFormatter`实例是无法完成这种转换的，所以我们又回到了在整个应用程序中复制时间标准化调用的原始问题。嗯，原来有*有*有`dateDecodingStrategy`可以做到这一点，那就是

## 方法 6:使用定制的`dateDecodingStrategy`块

其中一个`JSONDecoder.DateDecodingStrategy`枚举实例是`custom`，它获取一个关联的块，该块获得一个`JSONDecoder`实例并返回一个`Date`。因此，让我们将之前的日期操作代码放入该块中，如下所示

```
dateDecodingStrategy = .custom({ (decoder) -> Date in
    // Parse the date using a custom `DateFormatter`
    let container = try decoder.singleValueContainer()
    let dateString = try container.decode(String.self)
    let date = self.dateFormatter.date(from: dateString)

    let midnightThen = Calendar.current.startOfDay(for: date)
    let millisecondsFromMidnight = date.timeIntervalSince(midnightThen)

    let midnightToday = Calendar.current.startOfDay(for: Date())
    let normalizedDate = midnightToday.addingTimeInterval(millisecondsFromMidnight)

    return normalizedDate
}) 
```

请注意块中的前三条语句。前两个展示了如何从`decoder`获得一个单独的`String`值。但是这个`decoder`实例是什么呢？是`Decoder`类型的(*不是*T4)！)，它保存单个元素——在本例中是一个`JSON`值字符串。(如果您的 JSON 包含一个需要操作的`Date`的数组或字典，您将相应地更改容器和解码类型。)

就这样吗？完事了吗？不*相当*。注意，这个定制解码策略仍然需要一个`DateFormatter`实例。`DateFormatter`创建实例的成本很高，所以我将创建一个实例，并将其分配给设置这个`dateDecodingStrategy`的类的属性。为了保持内容相对独立，我子类化了`JSONDecoder`，就像这样:

```
class NormalizingDecoder: JSONDecoder {

    /// The formatter for date strings returned by `sunrise-sunset.org`.
    /// These are in the `.medium` time style, like `"7:27:02 AM"` and
    /// `"12:16:28 PM"`.
    let dateFormatter: DateFormatter
    let calendar = Calendar.current

    override init() {
        super.init()
        dateFormatter = DateFormatter()
        dateFormatter.timeStyle = .medium
        keyDecodingStrategy = .convertFromSnakeCase
        dateDecodingStrategy = .custom { (decoder) -> Date in
            let container = try decoder.singleValueContainer()
            let dateString = try container.decode(String.self)
            let date = self.dateFormatter.date(from: dateString)

            if let date = date {
                let midnightThen = calendar.startOfDay(for: date)
                let millisecondsFromMidnight = date.timeIntervalSince(midnightThen)

                let today = Date()
                let midnightToday = calendar.startOfDay(for: today)
                let normalizedDate = midnightToday.addingTimeInterval(millisecondsFromMidnight)

                return normalizedDate
            } else {
                throw DecodingError.dataCorruptedError(in: container,
                                                       debugDescription:
                    "Date values must be formatted like \"7:27:02 AM\" " +
                    "or \"12:16:28 PM\".")
            }
        }
    }
} 
```

使用这个定制`JSONDecoder`，我们的`Codable`可以再次看起来像我们在方法#1 中想要的，即

```
public struct SunriseSunsetResponse: Codable {
    public var results: SunriseSunset
}

public struct SunriseSunset: Codable {
    public var sunrise: Date
    public var sunset: Date
} 
```

[![Nerdvana, according to Dilbert](img/59e01151ae38f0c8a79270d783d2a730.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PsJ0MyRG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://assets.amuniversal.com/a200ff809c43012f2fe400163e41dd5b) 
[呆伯特成就 Nerdvana](https://dilbert.com/strip/1991-09-28)
用这种方法，你可以做得更多:

*   调整时区偏移量的时间
*   处理可能采用几种可接受格式之一的日期
*   处理格式化的数组和字典

如果你已经做到了这一步，感谢你的阅读！这是我第一篇公开的技术文章，尽管我自 1996 年以来就是一名专业开发人员。

###### 脚注

<sup><small>1</small></sup> 从技术上讲，`Codable`是`Encodable`和`Decodable`的一个`typealias`。
<sup><small>2</small></sup>**C**坐标式 **U** 通用 **T** ime，更好的说法是格林威治标准时间(GMT)。
<sup><small>3</small></sup> 如果你是一个*布鲁斯兄弟*的粉丝，你可能会认出这些是埃尔伍德布鲁斯地址的坐标。
[![That's Wrigley Field!](img/724ee06b8f4bf5985b48520603ac6cf8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OCopg3gu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pbs.twimg.com/media/CEBcz0DUUAAREzb.jpg)
# 可编码——改进 JSON 解码方式的 4 种方法

> 原文：<https://dev.to/leogdion/codable--4-ways-to-improve-how-you-decode-json-5dhe>

Swift 4 中引入的 Codable 提供了一种便捷的编码和解码 JSON 的方法。然而，并不是所有东西都能开箱即用。例如，让我们看看 Twitter 上的这个 [JSON 文件。由于 JSON 解码器的工作方式，我们看到:](https://github.com/brightdigit/advanced-swift-codable/blob/master/advanced-swift-codable.playground/Resources/twitter.json)

*   引用和标准推文使用相似但不完全相同的字段。
*   被转换成属性名的键不遵循 Swift 惯例。
*   **日期不是标准的可接受格式。**
*   **颜色不是任何颜色类** (UIColor、NSColor 等)接受的格式

因此，我们将学习如何为一些特殊情况设置可编码类型。具体来说，我们将了解:

*   使用具有可编码类型的协议
*   不同的属性名和键
*   属性值中的日期
*   自定义类型的属性值

要继续下去，你可以在 GitHub 上查看本文的 Xcode 游乐场。

## JSON decoder 如何工作

以下是 JSONDecoder 如何将 JSON 转换成 structuredtype 的基本规则:

1.  属性名按原样转换。
2.  默认情况下，简单属性值被转换为`String`、`Int`或`Double`
3.  遵循正确格式的属性值可以被解码成`URL`、`Data`或`Date`。
4.  任何属性值都可以被假定为`Optional`
5.  方括号`[]`值被转换为`Array`
6.  花括号`{}`值被转换为`Dictionary`或自定义类型。
7.  所有需要解码的类型都需要实现可解码的协议。同样，需要编码的类型需要实现可编码的协议。如果类型需要解码和编码，可以简单地实现 Codable。

在这种情况下，我们将查看为 tweet 返回的 [Twitter JSON 数据](https://github.com/brightdigit/advanced-swift-codable/blob/master/advanced-swift-codable.playground/Resources/twitter.json)。因此，**我们要做的第一件事就是为返回的每种数据类型创建一些基本结构**。因此，你可以看看[在回购](https://github.com/brightdigit/advanced-swift-codable/tree/step-1)的这个分支上工作解码的第一步。

即使数据是相似的，我们也创建单独的结构，以允许更大的灵活性。然而，这并不意味着我们不能使用协议来简化函数中这些类型的使用。

## 使用带编码的协议

例如，在 Twitter API 中，tweet 的概念贯穿始终。举个例子，他们的 JSON 有一条主推文和*条引用推文*。因此，人们可能倾向于对两者使用相同的结构。然而，更好的方法是两个独立的结构:

```
 public struct Tweet : Codable {
      public let created_at : Date
      public let id : Int
      public let full_text : String
      public let display_text_range : [Int]
      public let entities : TweetEntities
      public let source : String
      public let in_reply_to_status_id : Int?
      public let in_reply_to_user_id : Int?
      public let in_reply_to_screen_name : String
      public let user : TweetUser
      public let quoted_status : QuotedTweet?
      public let is_quote_status : Bool
      public let retweet_count : Int
      public let favorite_count : Int
      public let favorited : Bool
      public let retweeted : Bool
      public let possibly_sensitive : Bool
      public let possibly_sensitive_appealable : Bool
      public let lang : String
    }

    public struct QuotedTweet : Codable {
      public let created_at : Date
      public let id : Int
      public let full_text : String
      public let display_text_range : [Int]
      public let entities : TweetEntities
      public let user : TweetUser
      public let source : String
      public let extended_entities : TweetEntities
      public let is_quote_status : Bool
      public let retweet_count : Int
      public let favorite_count : Int
      public let favorited : Bool
      public let retweeted : Bool
      public let possibly_sensitive : Bool
      public let possibly_sensitive_appealable : Bool
      public let lang : String
    } 
```

因此，这意味着重复的字段。然而，它也允许更好的灵活性和更容易的 JSON 解码。另一方面，当在函数中用作参数时，我们可以简化这些类型。例如，假设我们需要打印出推文:

```
 func printTweet (_ tweet: Tweet) {
      print(tweet.full_text)
      if let quoted_status = tweet.quoted_status {
        printTweet(quoted_status)
      }
    }

    func printTweet (_ tweet: QuotedTweet) {
      print(">",tweet.full_text)
    } 
```

然而，使用一些基本的*面向协议的编程*，我们实际上可以对此进行优化。首先，我们存根一个函数，它根据一个协议打印出 tweet，不管它是否是一个引用的 tweet。

```
 func printTweet(_ tweet: TweetProtocol, withQuoteLevel level: Int = 0) {
      print(String(repeating: ">", count: level),tweet.full_text)
      if let quotedTweet = tweet.quotedTweet {
        printTweet(quotedTweet, withQuoteLevel: level+1)
      }
    } 
```

因此，我们创建了具有上述功能所需属性的协议。

```
 public protocol TweetProtocol {
      var full_text : String { get }
      var quotedTweet : TweetProtocol? { get }
    } 
```

最后，我们实现了两种类型的协议，我们将把它用于— `Tweet`和`QuotedTweet` :

```
 extension Tweet : TweetProtocol {
      public var quotedTweet: TweetProtocol? {
        return self.quoted_status
      }
    }

    extension QuotedTweet : TweetProtocol {
      public var quotedTweet: TweetProtocol? {
        return nil
      }
    } 
```

现在，我们可以更灵活地使用 Codable，同时使用协议添加类似的功能。接下来，让我们清理属性名。

### 自定义属性名称

关于 Twitter 的 JSON，你会注意到一件事，那就是**他们使用了所谓的** [蛇案](https://en.wikipedia.org/wiki/Snake_case)** 来组合属性名称中的单词。因此，下划线 **`_`** 用于组合 JSON 键中的单词。**例如，tweet 全文的属性叫做`full_text`。然而，*** *[Swift API 设计指南](https://swift.org/documentation/api-design-guidelines/#general-conventions)** 推荐使用 **[camel case。](https://en.wikipedia.org/wiki/Camel_case)** Camel case 每个生词都大写。**因此，在这种情况下，我们的资产名称应该是`fullText`。因此，我们有两种方法来解决这个问题:
* *自定义编码键或使用 KeyEncodingStrategy。**

自定义编码键通过允许我们提供从 JSON 键到属性名的映射，为我们提供了最大的灵活性。然而，在这种情况下**,将所有键映射到属性名有一个一致的策略。**因此，在这种情况下，我们将具体使用`KeyDecodingStrategy``KeyDecodingStrategy.convertFromSnakeCase`:

此外，如果`KeyDecodingStrategy.convertFromSnakeCase`不太适合您的 JSON 键，Swift 允许使用[定制策略。](https://developer.apple.com/documentation/foundation/jsondecoder/keydecodingstrategy/custom)换句话说，您可以提供一个闭包，用于将 CodingKey 数组转换为单个 CodingKey 结果。
最后但同样重要的是，如果这些策略都不能将一个键转换成一个属性名，你可以提供一个定制的 CodingKeys 枚举。**简而言之，您希望使用最简单和最一致的策略来映射属性名或 JSON 键。**

### 自定义属性值

Twitter 的 JSON 数据的另一个奇怪之处是它们如何格式化一些属性值。让我们来看看最容易处理的是日期。

### 如何处理日期

默认情况下，日期被序列化为一个浮点小数，具体来说就是从[2001 年 1 月 1 日](https://developer.apple.com/documentation/foundation/nsdate/1417376-timeintervalsincereferencedate)的*参考日期*开始的[时间间隔](https://developer.apple.com/documentation/foundation/timeinterval)(秒数)。但是，如果 JSON 属性值的格式不同，您可以使用[中可用的策略之一](https://developer.apple.com/documentation/foundation/jsondecoder/datedecodingstrategy):

因此，**如果日期格式不符合其中任何一个，您需要提供一个闭包来转换它，或者提供一个** [DateFormatter](https://developer.apple.com/documentation/foundation/dateformatter)** 。**在 [DateFormatter](https://developer.apple.com/documentation/foundation/dateformatter) 中指定格式的主要方法是使用 [dateFormat 字符串属性](https://developer.apple.com/documentation/foundation/dateformatter/1413514-dateformat)。换句话说，通过使用 [Unicode 提供的代码](http://www.unicode.org/reports/tr35/tr35-31/tr35-dates.html#Date_Format_Patterns)，我们可以创建一个 *dateFormat* 字符串来表示 Twitter 提供的日期和时间格式。因此，从我们 JSON 中的日期`Mon Mar 28 14:39:13 +0000 2016`可以推断出格式为:`eee MMM dd HH:mm:ss ZZZZ yyyy`。因此，我们可以创建一个 [DateFormatter](https://developer.apple.com/documentation/foundation/dateformatter) 并相应地设置策略:

总之，通过自定义 DateFormatter，可以将日期解码为 Date 属性值。然而，颜色会稍微更有挑战性。

### 自定义属性值的解码

对于像配置文件背景色这样的颜色，我们需要创建一个单独的结构，并自己实现编码和解码，将十六进制字符串转换成可用的颜色结构:

```
 import Foundation
    import CoreGraphics

    public struct Color : Codable {
      public let red : CGFloat
      public let green : CGFloat
      public let blue : CGFloat
      public let alpha : CGFloat

    public init(from decoder: Decoder) throws {
        let hexCode = try decoder.singleValueContainer().decode(String.self)
        let scanner = Scanner(string: hexCode)
        var hexint : UInt32 = 0
        scanner.scanHexInt32(&hexint)

    self.red = CGFloat((hexint & 0xff0000) >> 16) / 255.0
        self.green = CGFloat((hexint & 0xff00) >> 8) / 255.0
        self.blue = CGFloat((hexint & 0xff) >> 0) / 255.0
        self.alpha = 1
      }

    public func encode(to encoder: Encoder) throws {
        let string = String(format: "%02lX%02lX%02lX", lroundf(Float(red * 255.0)), lroundf(Float(green * 255.0)), lroundf(Float(blue * 255.0)))
        var container = encoder.singleValueContainer()
        try container.encode(string)
      }
    } 
```

在这种情况下，我们覆盖 init 方法并从`singleValueContainer`中获取字符串。之后，我们用一个[扫描仪](https://developer.apple.com/documentation/foundation/scanner)将彩色六进制码转换成十六进制数。然后使用按位 and 逻辑运算的组合来获取 0 到 255 之间的每个组件值。最后，我们使用十进制除法来计算 0 和 1 之间的值。(为了完整起见，我还包括了编码方法。)因此，我们可以轻松地将 hexcode 字符串转换为 Color struct。

由于我们不能使用现有的系统颜色类型，下面是从我们的自定义颜色结构转换成系统默认 UI 颜色类型的代码:

```
 #if os(iOS) || os(watchOS) || os(tvOS)
    import UIKit
    public typealias SystemColor = UIColor
    #elseif os(macOS)
    public typealias SystemColor = NSColor
    #endif

    extension Color {  
      public var systemColor : SystemColor {
        return SystemColor(red: self.red, green: self.green, blue: self.blue, alpha: self.alpha)
      }
    } 
```

## 让 Codable 为我们工作

Codable 提供了许多层次的可定制性。然而，在更多的情况下，我们希望使用最少的定制和最大的一致性。

*   对于以相似方式使用的数据**使用不同的类型，但实现相同的协议**
*   如果属性名称具有一致的策略，**使用 KeyEncodingStrategy 而不是自定义编码 Keys**
*   对于日期**，使用 dateDecodingStrategy、DateFormatter 或闭包**。
*   对于其他自定义类型，* *创建一个单独的类型，并在那里自定义解码** (和编码)。

如果您有其他问题希望我调查，请通过 twitter 联系我。此外，如果您有兴趣了解更多关于复杂 JSON 解码或编码以及其他 Swift 主题的信息，[订阅我的时事通讯以了解最新消息](http://eepurl.com/c5uqxP)。
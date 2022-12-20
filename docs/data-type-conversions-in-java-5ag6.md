# Java 中的数据类型转换

> 原文：<https://dev.to/attacomsian/data-type-conversions-in-java-5ag6>

*本文最初发表于[attacomsian.com/blog](https://attacomsian.com/blog/data-type-conversions-java)。*

* * *

与 PHP 或 JavaScript 不同，Java 是一种强类型编程语言。它本质上意味着每个变量都必须用预定义的数据类型声明，并且以后不能更改。Java 中有两种数据类型:

*   原始数据类型- int，double，float，byte，long，boolean 等。
*   引用数据类型——整数、双精度、浮点、日期、字符串、对象等。

在本教程中，我们将重点关注原始数据类型的类型转换。

## 字符串到整数

`String`到`int`的转换有两种方法:`Integer.parseInt()`返回一个原语`int`和`Integer.valueOf()`返回一个`Integer`对象。

```
String str = "1050";

int inum = Integer.parseInt(str);   //return primitive
System.out.println(inum);

Integer onum = Integer.valueOf(str); //return object
System.out.println(onum); 
```

## 串来长

与`int`类似，我们可以通过`Long.valueOf()`方法使用`Long.parseLong()`或对象`Long`将`String`转换为原始的`long`值。

```
String longStr = "1456755";

long ilong = Long.parseLong(longStr); //return primitive
System.out.println(ilong);

Long olong = Long.valueOf(longStr); //return object
System.out.println(olong); 
```

## 弦向浮动

使用`Float.parseFloat()`方法可以将`String`转换为原始值`float`。`Float.valueOf()`方法可以用来将一个`String`转换成一个`Float`对象。

```
String floatStr = "49.78";

float ifloat = Float.parseFloat(floatStr); //return primitive
System.out.println(ifloat);

Float ofloat = Float.valueOf(floatStr); //return object
System.out.println(ofloat); 
```

## 字符串为双精度

`double`和`float`数据类型可能看起来相同，但它们存储值的方式不同。`float`是单精度(32 位或 4 字节)浮点数据类型，而`double`是双精度(64 位或 8 字节)浮点数据类型。

使用`Double.parseDouble()`方法可以将`String`值转换为`double`值。类似地，`Double.valueOf()`将一个`String`转换成一个`Double`对象。

```
String doubleStr = "99.378";

double idouble = Double.parseDouble(doubleStr); //return primitive
System.out.println(idouble);

Double odouble = Double.valueOf(doubleStr); //return object
System.out.println(odouble); 
```

## NumberFormatException

如果在`int`、`float`或`double`转换期间`String`不包含可解析的值，则抛出`NumberFormatException`。

```
try {
    String exeStr = "14c";
    int exeInt = Integer.parseInt(exeStr);
    System.out.println(exeInt);
} catch (NumberFormatException ex) {
    System.out.println(ex.getMessage());
} 
```

## 字符串转为布尔型

使用`Boolean.parseBoolean`方法可以将`String`值转换为原始的`boolean`值。对于转换为`Boolean`对象，可以使用`Boolean.valueOf()`方法。

```
String trueStr = "true";
String falseStr = "false";
String randomStr = "java";

System.out.println(Boolean.parseBoolean(trueStr)); //true
System.out.println(Boolean.valueOf(falseStr)); //false
System.out.println(Boolean.parseBoolean(randomStr)); //false 
```

## 字符串到日期

Java 提供了用于格式化和解析日期的`SimpleDateFormat`类。它有以下两个重要的方法:

*   `parse()` -它将一个`String`值转换成一个`Date`对象
*   `format()` -它将`Date`对象转换成一个`String`值

在创建`SimpleDateFormat`类的实例时，您需要传递日期和时间模式，告诉实例应该如何解析或格式化日期。

```
String dateStr = "10/03/2019";

SimpleDateFormat format = new SimpleDateFormat("dd/MM/yyyy");
Date dateObj = format.parse(dateStr);
System.out.println(dateObj); 
```

在上面的例子中，我使用了`dd/MM/yyyy`模式来解析`10/03/2019`字符串。`dd`表示两位数的日，`MM`表示两位数的月，`yyyy`表示四位数的年。以下是`SimpleDateFormat`中最常用的日期和时间模式列表。完整列表请参考官方 [JavaDoc](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html) 。

| 信 | 描述 | 例子 |
| --- | --- | --- |
| y | 年 | 2019, 19 |
| M | 一年中的月份 | 2003 年 3 月 3 日 |
| d | 一个月中的第几天 | 1-31 |
| E | 周中的日期名称 | 周五至周日 |
| a | Am/pm 标记 | 上午，下午 |
| H | 一天中的小时 | 0-23 |
| h | 上午/下午的小时 | 1-12 |
| m | 小时中的分钟 | 0-59 |
| s | 分钟秒 | 0-59 |
| S | 毫秒秒 | 0-999 |
| z | 通用时区 | 中欧时间，PST，GMT +05:00 |

以下是一些模式示例，以及每个模式如何解析日期的示例，反之亦然:

```
yyyy/MM/dd  <--> (2019/03/09)

dd-MM-YYYY  <-->  (10-03-2019)

dd-MMM-yy  <-->  (13-Feb-19)

EEE, MMMM dd, yyy  <--> (Fri, March 09, 2019)

yyyy-MM-dd HH:mm:ss <--> (2019-02-28 16:45:23)

hh:mm:ss a <--> (11:23:36 PM)

yyyy-MM-dd HH:mm:ss.SSS Z <--> (2019-01-31 21:05:46.555 +0500) 
```

## 日期为字符串

正如我们上面讨论的，`SimpleDateFormat`也支持将日期格式化成字符串。下面是一个将日期格式化为字符串的例子:

```
Date date = Calendar.getInstance().getTime(); // OR new Date()

SimpleDateFormat dateFormat = new SimpleDateFormat("EEEE, MMMM dd, yyyy HH:mm:ss.SSS Z");

String formatStr = dateFormat.format(date);
System.out.println(formatStr); 
```

根据您所在的位置，上面的代码片段将打印以下内容:

```
Sunday, March 10, 2019 20:01:22.417 +0500 
```

## 日期到 ISO 8601 的字符串

ISO 8601 是一种国际标准，涵盖了日期和时间相关数据的交换。有几种方法可以用 ISO 格式表示日期和时间:

```
2019-03-30T14:22:15+05:00
2019-03-30T09:22:15Z
20190330T092215Z 
```

下面是一个用 Java 将日期对象转换成 ISO 8601 等价字符串的例子:

```
TimeZone timeZone = TimeZone.getTimeZone("UTC");
SimpleDateFormat isoFormat = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ss'Z'");
isoFormat.setTimeZone(timeZone);
String isoFormatStr = isoFormat.format(new Date());
System.out.println(isoFormatStr); 
```

以下是 ISO 格式的日期和时间模式:

| 模式 | ISO 日期格式 |
| --- | --- |
| yyyy-MM-DD t ' hh:MM:ssXXX | 2019-03-30T14:22:15+05:00 |
| yyyy-MM-dd'T'HH:mm:ss'Z ' | 2019-03-30T09:22:15Z |
| yyyyMMdd 'T 'HHmmss 'Z ' | 20190330T092215Z |

> **源代码:**从 [GitHub](https://github.com/attacomsian/code-examples/tree/master/core-java/conversions) 下载完整的源代码，可以在 MIT 许可下获得。

## 结论

对于开发人员来说，数据类型转换非常常见。这些转换中的大多数都是琐碎的，并且对于有经验的程序员来说是众所周知的。然而，字符串到日期的转换有点棘手，尤其是对初学者来说。如果没有正确指定模式，您可能会遇到错误。但是，如果你花一些时间记住这些模式，它可能会节省大量的时间，同时找出为什么某个转换不编译或执行。

我错过了本教程中任何重要的类型转换吗？随时给我发一条推特让我知道。

* * *

✌️:我写了关于现代 JavaScript、Node.js、Spring Boot 和所有 web 开发的东西。 [**订阅我的简讯**](https://attacomsian.com/newsletter) 每周获取 web 开发教程& protips。
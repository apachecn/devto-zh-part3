# ★验证分隔数据的规则

> 原文：<https://dev.to/freekmurze/a-rule-to-validate-delimited-data-5b45>

laravel-validation-rules 是一个包含方便的定制验证规则的包，这些规则被用于我们在 [Spatie](https://spatie.be) 做的各种客户端项目中。上周，我在包中添加了一个名为`Delimited`的新验证规则。

在这篇博文中，我想解释一下我们为什么要添加它，以及如何使用它。

## 你真的需要多值输入吗？

上周 [Caleb“弦乐之王”Porzio](https://twitter.com/calebporzio) 在推特上写道:

> 让我们从[@ push silver](https://twitter.com/pushsilver?ref_src=twsrc%5Etfw)([@ davidhemphill](https://twitter.com/davidhemphill?ref_src=twsrc%5Etfw))开始，用一分钟的时间来欣赏这个文本字段的美丽
> 
> 多个项目的简单文本字段。没有 select2，没有 vue-select，没有带“添加”按钮的自定义多输入。
> 
> 只是后端的一点简单解析。？[pic.twitter.com/xPvljsVXqr](https://t.co/xPvljsVXqr)
> 
> ——迦勒法索([@迦勒法索](https://dev.to/calebporzio))[2019 年 5 月 14 日](https://twitter.com/calebporzio/status/1128307281395900416?ref_src=twsrc%5Etfw)

我认为他是对的。当接受一个分隔的字符串已经足够好的时候，为什么还要费心去创建一个多输入的东西呢？

在我正在做的一个项目中，有些字段接受多个电子邮件地址，有些字段接受多个电话号码。我决定让它们作为逗号分隔的字符串输入。首先需要的是验证。

## 引入`Delimited`规则

为了验证逗号分隔的电子邮件和逗号分隔的电话号码，我创建了一个名为`Delimited`的新的[验证规则](https://laravel.com/docs/master/validation#using-rule-objects)，它可以验证所有类型的验证数据。它应该用在[表单请求](https://laravel.com/docs/master/validation#form-request-validation)中。其构造函数接受一个验证规则，该规则将用于验证分隔字符串中的每一项。

下面是它用法的一个例子:

```
// in a `FormRequest`

public function rules()
{
    return [
        'emails' => [new Delimited('email')],
    ];
} 
```

以下是一些符合此规则的示例输入:

*   `'sebastian@example.com, alex@example.com'`
*   `''`
*   `'sebastian@example.com'`
*   `'sebastian@example.com, alex@example.com, brent@example.com'`
*   `' sebastian@example.com , alex@example.com , brent@example.com '`

此输入不会通过:

*   `'@example.com'`
*   `'nocomma@example.com nocommatoo@example.com'`
*   `'valid@example.com, invalid@'`

如果您需要用电话号码验证逗号分隔的字符串，您可以简单地将`phone`规则传递给`Delimited`。

```
// in a `FormRequest`

public function rules()
{
    return [
        'phone_numbers' => [new Delimited('phone')],
    ];
} 
```

如何处理经过验证的分隔数据取决于您。您可以将经过验证的分隔字符串保存到数据库中。或者您可以`explode`这个值，并将它作为一个数组保存在数据库中。选择权在你。

## 在关闭

`Delimited`规则有许多其他选项。您可以设置项目的最小值和最大值、使用另一个分隔符、接受重复、禁用修剪，...看看[laravel-validation 包](https://github.com/spatie/laravel-validation-rules#delimited)的自述文件，了解更多关于这些选项的信息。
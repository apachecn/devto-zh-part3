# 了解 Laravel 的 Mirza Yandex 翻译

> 原文：<https://dev.to/yak0d3/get-to-know-mirza-yandex-translator-for-laravel-319p>

嗯，这是我的第一个 Laravel 包！:D

这个包使用 Yandex。翻译 API 来翻译文本，并提供了几种方法，使其更容易使用的 API！

回购网址:[https://github.com/yak0d3/Mirza_Yandex_Translator](https://github.com/yak0d3/Mirza_Yandex_Translator)

非常感谢您的反馈！

# 方法表

| 方法 | 因素 | 返回 | 投 | 描述 |
| --- | --- | --- | --- | --- |
| 翻译 | `string $text` `string $lang` *可选:* `string $format \[html or plain\]`(默认:“平原”) | 线 | ***异常:*** 如果文本无法翻译。 | 将给定的$text 翻译成给定的$lang(语言) |
| 转化为 | `string $text` `array $langs` | 字符串(json) | ***异常:*** 如果不支持一种或多种语言。 | 将给定的`$text`翻译成多种`$langs`(语言) |
| 平移数组 | `array $textArray`T3【可选】T2 | 字符串(json) | ***例外:***
1。如果不支持目标语言。
2。如果`$assoc`被设置为`true`并且给定的数组不是关联的。 | 将一个`$textArray`(文本数组)翻译成给定的`$lang`(语言)
***注意:*** 如果`$assoc`设置为`true`，返回的 json 字符串将具有相同的索引名称 |
| 检测语言 | `string $text` *可选:* `bool $langName` | 线 | ***例外:*** 1。如果没有找到语言代码。2.如果没有找到语言名称 | 检测给定`$text`的语言，返回语言代码 ***注:*** 如果`$langName`设置为`true`，则返回语言全称。 |
| getsupportedlangauges | *无* | 字符串(json) | ***异常:*** 如果试图获取支持函数列表时出现未知错误 | 返回一个包含所有支持语言列表的`json string` |
| translateToAll | `string $text` | 字符串(json) | *无* | 将字符串(`$text`)翻译成所有支持的语言。 ***注意:*** 这可能需要一段时间并导致一个`PHP max_execution_time TIMEOUT Exception` |
| yandex _ 权利 | *可选:* `string $color (Default: #fff)` `string $fontsize (Default: 14px)` | 线 | *无* | 返回“由 Yandex 提供动力”的字符串。翻译”链接字符串。也称 via `blade`指令 [`@yandex_rights`](#) 。
***注:*** 请参考 [Yandex Translate:翻译结果使用要求](https://tech.yandex.com/translate/doc/dg/concepts/design-requirements-docpage/)了解更多关于字号、颜色和摆放要求。 |
| 语言 _ 选择 | *无* | 线 | *无* | 返回带有所有可用语言列表的`HTML` `<select>`标签的字符串。
亦称 via `blade`指令 [`@languages_select`](#) |

# 叶片指令表

| 指示的 | 因素 | 描述 |
| --- | --- | --- |
| `@yandex_rights` | *可选:* `string $color (Default: #fff)` `string $fontsize (Default: 14px)` | 为“由 Yandex 提供支持”生成一个 HTML 链接。翻译”文本。 |
| `@languages_select` | *无* | 生成一个包含所有可用语言列表的`HTML` `<select>`标签。 |
| `@translate` | `string $text` `string $lang` | 将给定的`$text`字符串翻译成给定的`$lang`(语言) |

​
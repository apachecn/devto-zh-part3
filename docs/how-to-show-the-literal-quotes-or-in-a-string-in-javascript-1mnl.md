# 快速提示:如何在 javascript 中显示字符串中的文字引号“或”

> 原文：<https://dev.to/akhil_001/how-to-show-the-literal-quotes-or-in-a-string-in-javascript-1mnl>

通常我们在 javascript 中声明一个字符串如下

```
var str1 = "Hello there!";
//or
var str2 = 'Hello there!'; 
```

有些情况下，您需要在字符串中包含双引号或单引号。

为了实现这一点，我们可以使用转义符' \ '，如下所示

```
var str1 = "\"Hello there!\"- Obi Wan Kenobi";
// "Hello there" - Obi Wan Kenobi
var str2 = '\'Urr Arrgghh Urr\' - Chewbacca' 
// 'Urr Arrgghh Urr'- Chewbacca 
```

### 奖金内容

既然' \ '被认为是转义序列，如何在字符串中显示' \ '？即使在这种情况下，转义序列也可以用来显示' \'

```
var str3 = "1\\2 is perfectly balanced";
/* 1\2 is perfectly balanced */ 
```

有关字符串和转义字符的更多细节，请参考: [MDN Docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)

希望你觉得有用！
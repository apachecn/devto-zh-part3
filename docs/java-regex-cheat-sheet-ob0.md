# Java 正则表达式备忘单

> 原文：<https://dev.to/voidjuneau/java-regex-cheat-sheet-ob0>

这个帖子对我来说更多的是一个提示。不是有意写帖子，而是找不到自己想要的全面的。所以，这篇文章不会写得很好，而且，我不会说任何关于表达的东西，因为有数以百万计的好资源。

与 JavaScript 相比，在 Java 中使用 Regex 有点烦人。是的，这是真的，这也是我写这篇文章的原因。

## 语法

对于模式，Java 要求**双反斜线`\\`** ，而不是单反`\`。好消息是，我不知道其他编辑器，但至少在 NetBeans 中，当你从剪贴板粘贴代码时，额外的`\`会自动添加。
此外，**不同的字符串需要它自己的匹配器**，除非用`matches()`方法调用。

### 方法

#### 图案

```
Pattern compile(String regex[, int flags])    // flags optional, use fields sepreate by |
boolean matches([String regex, ]CharSequence input)    // regex optional.
String[] split(String regex[, int limit])    // limit optional.
String quote(String s)    // returns a literal pattern String for the specified String. 
```

Enter fullscreen mode Exit fullscreen mode

#### 匹配

```
// for names, in regex, (?<name>pattern)
int start([int group | String name])    // argument optional
int end([int group | String name])    // argument optional.
boolean find([int start])    // start is optional.
String group([int group | String name])    // argument optional.
Matcher reset() 
```

Enter fullscreen mode Exit fullscreen mode

#### 字符串

```
boolean matches(String regex)
String replaceAll(String regex, String replacement)
String[] split(String regex[, int limit])    // limit optional 
```

Enter fullscreen mode Exit fullscreen mode

还有更多方法。

### 用途

#### 如果有匹配存在。

```
String str = "She sells seashells by the seashore";
String reg = "\\w*se\\w*";
Pattern p = Pattern.compile(reg);
Matcher m = p.matcher(str);
System.out.println(m.find());    // true 
```

Enter fullscreen mode Exit fullscreen mode

#### 如果它们相同

```
String str = "She sells seashells by the seashore";
String reg = "\\w*se\\w*";
Pattern p = Pattern.compile(reg);
Matcher m = p.matcher(str);
System.out.println(m.matches());    // false, only true when
                                    // the pattern matches with string
                                    // without remainder.

System.out.println("seashore".matches(reg));    // true 
```

Enter fullscreen mode Exit fullscreen mode

或者，

```
String str = "She sells seashells by the seashore";
String reg = "\\w*se\\w*";
System.out.println(Pattern.matches(reg, str);    // false 
```

Enter fullscreen mode Exit fullscreen mode

#### 所有匹配的数组

```
String str = "She sells seashells by the seashore";
String reg = "\\w*se\\w*";
Pattern p = Pattern.compile(reg);
Matcher m = p.matcher(str);
List<String> matches = new ArrayList<>();
while (m.find()) {
    matches.add(m.group());
}
System.out.println(matches);    // [sells, seashells, seashore] 
```

Enter fullscreen mode Exit fullscreen mode

#### 统计匹配数

```
String str = "She sells seashells by the seashore";
String reg = "\\w*se\\w*";
Pattern p = Pattern.compile(reg);
Matcher m = p.matcher(str);
int counter = 0;
while (m.find()) {
    counter++;
} 
```

Enter fullscreen mode Exit fullscreen mode

我发现 code golf 并不适用于所有情况。对于火柴的数组也是一样。

# 旗帜

所有标志都是模式类的字段。在 Java 中，你可能认为所有的正则表达式都是全局的。

| Java 语言(一种计算机语言，尤用于创建网站) | Javascript 等价物 | 解释 |
| --- | --- | --- |
| 佳能 _ 情商 |  | 启用规范等效。 |
| 不区分大小写 | 我 | 启用不区分大小写的匹配。 |
| 评论 |  | 空白和注释在模式中被忽略。 |
| 刀塔 | s | 点也匹配结束行字符。 |
| 逐字的 |  | 启用模式的文字解析。 |
| 多线 | m | 启用多线模式。 |
| UNICODE_CASE | u | 启用支持 Unicode 的大小写折叠。 |
| UNIX_LINES |  | 在的行为中只识别' \n '行结束符。、^和美元。 |

可能有我错过的 Javascript 等价物。

## 引用

*   [图案](https://docs.oracle.com/javase/8/docs/api/java/util/regex/Pattern.html)
*   [匹配](https://docs.oracle.com/javase/8/docs/api/java/util/regex/Matcher.html)
*   [字符串](https://docs.oracle.com/javase/8/docs/api/java/lang/String.html)
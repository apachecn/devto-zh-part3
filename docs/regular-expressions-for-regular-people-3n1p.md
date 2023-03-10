# 普通人的正则表达式

> 原文：<https://dev.to/neilberg/regular-expressions-for-regular-people-3n1p>

同样优雅而神秘的是，正则表达式是输入验证、字符串操作和许多其他任务的重要工具。起初，它们的语法可能令人望而生畏，但是斜线之间有一个清晰的逻辑，我发现通过一些例子可以很好地揭示这一点。

因此，这篇文章涵盖了 JavaScript 中正则表达式的一些常见用法的创建和应用。它是为普通人准备的。我指的是像我这样的人，他们不是专家，而是普通人，只是偶尔需要接触一下。

### 创建正则表达式

创建正则表达式(RegEx)有两种方法:

1.  斜杠之间模式的正则表达式文字

*   最常见的创建方法
*   当您预先知道模式并且它不会改变时使用

```
const re = /flower/ 
```

1.  构造函数 RegExp

*   当模式未知，但在代码执行期间发生变化或模式来自用户输入或外部来源时使用

```
const re = new RegExp('flower') 
```

### 测试正则表达式

有两种方法可以测试正则表达式:

1.  使用 RegExp 对象的方法:

*   `test` -匹配则返回真，不匹配则返回假
*   `exec` -返回匹配细节的数组，如果不匹配则返回空值

```
/flower/.test('flowerpot')
// true 
```

```
/flower/.exec('flowerpot')
// ["flower", index: 0, input: "flowerpot", groups: undefined]
// returned details include: matched text, starting index of the match
// and the original text input 
```

1.  使用 String 对象的方法:
    *   `match` -返回匹配细节的数组，如果不匹配则返回空值
    *   `replace` -返回新字符串，匹配的文本替换为定义的新文本
    *   (不太常见):`search`和`split`

```
'Los Angeles, California'.match(/California/)
//["California", index: 13, input: "Los Angeles, California", groups: undefined] 
```

```
'Los Angeles, California'.replace(/California/, 'CA')
// "Los Angeles, CA" 
```

最后，有一个很棒的在线工具叫做[正则表达式 101](https://regex101.com/) ，它允许模式的交互测试。还提供了匹配发生的原因的解释。

### 旗帜

可以将标志附加到修改搜索参数的模式上。常见标志包括:

*   `g` -全局匹配，即查找所有匹配
*   `i` -不区分大小写，即忽略大小写

```
'one hat two hat red hat blue hat'.match(/hat/g)
// ["hat", "hat", "hat", "hat] 
```

```
'App.js app.js wrapper.js'.match(/app/gi)
// ["App", "app", "app"] 
```

### 量词

在模式之后可以包括某些量化字符和表达式，以细化它们的专用性。

#### 可选配`?`

```
/colou?r/.test('color')
// true 
// The "u" is optional 
```

#### 与`+`的一个或多个匹配

```
'yassss queen'.match(/yas+/)
// ["yassss", index: 0, input: "yassss queen", groups: undefined]
// The "s" is matched one or more times (4 times in this case) 
```

#### 无，一个或多个与`*`匹配

```
'ya queen'.match(/yas*/)
// ["ya", index: 0, input: "ya queen", groups: undefined]
// The "s" does not need to be matched 
```

#### 【始于】用`^`

```
/^App/.test('App.js');
// true 
```

#### 【结尾用】使用`$`

```
/js$/.test('App.js');
// true 
```

#### 使用`{n}`匹配精确的 *n* 次

```
/5{3}/.test('555'); // true
// Three 5's are exactly matched 
```

#### 匹配至少 *n 次*使用`{n,}`

```
/5{3,}/.test('5555'); // true
// At least three 5's are matched 
```

#### 匹配在 *n* 和 *m* 之间使用`{n,m}`的次数

```
/5{1,4}/.test('55'); // true
// Between one and four 5's are matched 
```

### 元字符

除了字母和数字之外，正则表达式也可以由元字符组成，元字符的前面总是有一个反斜杠。一些常见的元字符包括:

*   `\w` -任何字母数字或单词字符
*   `\W` -任何非字母数字或单词字符

```
'R2-D2'.match(/\w{2}\W\w{2}/);
// ["R2-D2", index: 0, input: "R2-D2", groups: undefined]
// Two word characters (R2), then one non-word character (-), then
// two word characters (D2) are matched 
```

*   `\d` -任何数字
*   `\D` -任何非数字

```
'C-3PO'.match(/\D{2}\d\D{2}/);
// ["C-3PO", index: 0, input: "C-3PO", groups: undefined]
// Two non-digits (C-), then one digit (3), then
// two non-digits (PO) are matched 
```

*   `\s` -任何空白字符(空格、制表符、换行符)
*   `\S` -任何非空白字符

```
'apples or oranges'.match(/\S+\s\S+\s\S+/);
// ["apples or oranges", index: 0, input: "apples or oranges", groups: undefined]
// One or more non-whitespace characters (apples), then a whitespace,
// then one or more non-whitespace characters (or), then a whitespace,
// then one or more non-whitespace characters (oranges) are matched 
```

### 多个模式用“或”运算符

正则表达式中的 OR 运算符由管道`|`定义。如果找到多个模式中的至少一个，这允许出现肯定匹配。

```
'555'.match(/\d{3}|\(\d{3}\)/)
// ["555", index: 0, input: "555", groups: undefined]
// 555 is matched by \d{3} 
```

```
'(555)'.match(/\d{3}|\(\d{3}\)/)
// ["(555)", index: 0, input: "(555)", groups: undefined]
// (555) is matched by \(\d{3}\)
// Note: parentheses in literal form need to be escaped by backslahes 
```

### 字符集

方括号定义当*中的任何*字符匹配时匹配的字符集:

```
/[A-Z]\d-[A-Z]\d/.test('R2-D2')
// true since the following is matched:
// any uppercase letter, digit, hypen, any uppercase letter, digit 
```

```
/[A-D]-[0-4][N-Q][N-Q]/.test('C-3PO')
// true since the following is matched:
// any uppercase letter between A and D, hypen, any digit between 0 and 4, 
// any uppercase letter between N and Q, any uppercase letter between N and Q 
```

当在字符集的中使用*时，插入符号`^`创建一个反转集，也就是匹配除了*之外的任何字符*。*

```
/[^A-Q]\d-[^A-C]\d/.test('R2-D2')
// true since the following is matched:
// any uppercase letter not between A and Q, digit, hypen,
// any uppercae letter not between A and C, digit 
```

### 捕捉群体

用括号括起来的模式称为捕获组。这允许稍后在正则表达式中引用该模式，或者在匹配细节的数组中引用该模式。

```
/(\w+)\s\1\s\w+\s(\w+)\s\2/.test('same same but different different')
// true
// capture group 1 is "same"
// capture group 2 is "different"
// \1 and \2 reference "same" and "different", respectively
// true since the following is matched:
// one or more word characters (capture group 1), whitespace, 
// value of capture group 1, whitespace, one or more word characters, 
// one or more word characters (capture group 2), whitespace, 
// value of capture group 2 
```

使用`exec` RegExp 方法，我们可以更清楚地看到这个正则表达式的细节:

```
/(\w+)\s\1\s\w+\s(\w+)\s\2/.exec('same same but different different')
// "same same but different different", "same", "different", index: 0, 
// input: "same same but different different", groups: undefined] 
```

返回的结果包括:

*   匹配的子字符串
*   捕获组(例如“相同”和“不同”)
*   第一场比赛的起始索引
*   原文

捕获组也可以用来替换句子中的重复单词。

```
'My name name is is Neil'.replace(/(\w+)\s\1/g, '$1');
// "My name is Neil" 
```

正则表达式匹配一个或多个单词字符(捕获组)，后跟一个空格，后跟该捕获组的值。由于使用了全局标志，这就匹配了“name name”和“is is”。“name”是第一个匹配中捕获组的值,“is”是第二个匹配中捕获组的值。`\1`引用正则表达式中捕获组的值，而`'$1'`引用捕获组作为`replace`方法的第二个参数。因此，匹配的文本“name name”和“is is”分别被替换为“name”和“is”。

类似地，捕获组可用于重新排列句子中的单词。

```
'James Bond'.replace(/(\w+)\s(\w+)/, 'My name is $2, $1 $2');
// "My name is Bond, James Bond" 
```

匹配的文本是“詹姆斯·邦德”，其中“詹姆士”是捕获组 1 的值，“邦德”是捕获组 2 的值。我们将匹配的文本替换为“我的名字是”,后跟捕获组 2 的值，一个逗号，然后是捕获组 1 和 2 的值。

### 前瞻

只有当一个模式被另一个模式跟随(“正前瞻”)或没有被另一个模式跟随(“负前瞻”)时，前瞻才允许匹配。用`pattern1(?=pattern2)`创建正前视，用`pattern1(?!pattern2)`创建负前视。

下面的例子还引入了`.`通配符，它可以匹配任何字符。

```
/(.)+(?=Berg)/.test('Neil Berg');
// True
// "Berg" follows any one or more characters (Neil ) 
```

```
/(.)+(?!Berg)/.test('Neil Diamond');
// True
// "Berg" does not follow any one or more characters 
```

### 示例:密码验证

假设我们为用户登录制定了以下密码规则:

*   必须包括至少 1 个数字
*   必须包含小写和大写字母
*   必须包含至少 1 个特殊字符(例如！@#\$%&)
*   长度必须至少为 8 个字符

这些条件可以由下面的正则表达式表示:

`/(?=.*\d)(?=.*[a-z])(?=.*[A-Z])(?=.*[!@#$%^&*]).{8,}/`

四个肯定的前视用于检查文本中任何位置的下列条件是否被任何字符零次或多次跟随:

*   一个数字:`(?=.*\d)`
*   小写字母:`(?=.*[a-z])`
*   一个大写字母:`(?=.*[A-Z])`
*   特殊字符:`(?=.*[!@#$%^&*])`

最后，文本必须至少有 8 个字符长`.{8,}`

有趣的是，甚至不需要 Javascript 来测试 HTML 中文本、日期、搜索、url、电话、电子邮件和密码输入的正则表达式。这些输入可以接受一个包含正则表达式的[模式属性](https://www.w3schools.com/tags/att_input_pattern.asp)，该正则表达式用于检查表单提交。一旦正则表达式被匹配，表单就被提交。

让我们创建一个用户名/密码表单，其中的密码输入有一个模式属性。还可以包括一个标题属性，如果用户试图在通过模式检查之前提交，就会显示该属性。

```
<div class="form-container">
  <form>
    <label for="username">Username: </label>
    <input type="text" id="username" required>
    <label for="password">Password: </label>
    <input
      type="password"
      id="password"
      pattern="(?=.*\d)(?=.*[a-z])(?=.*[A-Z])(?=.*[!@#$%^&*]).{8,}"
      title="Must contain at least one number, uppercase letter,
      lowercase letter, and special character, and be at least 8
      or more characters long"
      required>
    <input type="submit" id="submit" value="Submit">
  <form>
</div> 
```

[![Regex password validation](img/9715f466ec8bef50064b1600aff2c093.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WwSo1Szz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1vswmeq0u2s8gp6ixpq5.gif)

我还在密码输入中添加了一个事件监听器，用于在每次击键后测试正则表达式。一旦验证为真，提交按钮就会从灰色变为绿色:

```
const submit = document.getElementById('submit');
const password = document.getElementById('password');

function validate() {
  const re = /(?=.*\d)(?=.*[a-z])(?=.*[A-Z])(?=.*[!@#$%^&*]).{8,}/;
  if (re.test(password.value)) {
    submit.style.background = '#039b1c';
    submit.style.color = '#F3F3F3';
  } else {
    submit.style.background = '#ccc';
    submit.style.color = "black";
  }
}

password.addEventListener('keyup', validate); 
```
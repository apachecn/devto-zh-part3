# ES6 -模板文字

> 原文：<https://dev.to/gyi2521/es6---template-literals-4ljf>

ES6 或 ECMAScript 2015 中我最喜欢的功能之一是“模板文字”。在我最近的编码训练营中学习“模板文字”之前，我曾经在 JavaScript 中使用字符串连接，如下所示:

```

var user = {
  name: 'Gina',
  hobby: 'Traveling',

};

console.log('Hi, I\'m ' + user.name + '! My hobby is "' + user.hobby + '".');

//Hi, I'm Gina! My hobby is "Traveling".

```

现在用模板文字，我可以写如下:

```
var user = {
  name: 'Gina',
  hobby: 'Traveling',

};

console.log(`Hi, I'm ${user.name}! My hobby is "${user.hobby}".`);

//Hi, I'm Gina! My hobby is "Traveling".

```

两个例子给出了相同的结果，但是您看到第二个例子使用“模板文字”的可读性了吗？使用字符串连接时，需要使用反斜杠(\)来转义特殊字符。由于一些字符的重复使用，阅读和理解文本可能具有挑战性。使用模板文字，您可以像在反勾号(``)内编写一个简单的英语句子一样构造字符串。如果你想添加变量，只需使用一个美元符号后面跟着花括号。如果有必要，您甚至可以添加一个简单的 Javascript 语句，如下所示:

```
console.log(`Two times seven is ${2*7}.`);

// Two times seven is 14.

```

此外，有了模板文字，换行符变得容易多了。

```
With Template Literals:

console.log(`Hi Gina,
Good luck with your presentation today!
                          -your friend`)

//Hi Gina,
Good luck with your presentation today!
                          -your friend

```

```
Without Template Literals:

console.log('Hi Gina, \n' +
'Good luck with your presentation today! \n' +
'\t\t\t\t\t\t   -your friend')

//Hi Gina,
Good luck with your presentation today!
                          -your friend

```

你看到它是多么容易阅读吗？使用模板文本，您可以在给朋友写便条时创建文本，而无需使用换行符(\n)或制表符(\t)。到目前为止，我还没有遇到这个功能的任何问题，我真的很喜欢使用它。如果你还没有尝试过，你一定要试一试...

感谢阅读！
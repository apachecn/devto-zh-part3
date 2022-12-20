# 如何在 JavaScript 中反转字符串

> 原文：<https://dev.to/skptricks/how-to-reverse-a-string-in-javascript-357i>

帖子链接:[如何在 JavaScript 中反转一个字符串](https://www.skptricks.com/2018/11/how-to-reverse-string-in-javascript.html)

他的教程解释了如何在 JavaScript 中反转字符串。我们使用 reverse 方法，reduce 方法来反转 javascript 中的字符串。

如何在 JavaScript 中反转字符串

方法 1 :
我们使用 split 方法将字符串分割成一个单独字符串的数组，然后链接到 reverse 方法。

const str = "ABCDEFGH "

let getReverseString = str.split(' ')。反转()。联接(“”)

console.log(getReverseString)

## 输出:

> "HGFEDCBA"

方法 2 :
使用 while 循环以传统方式反转一个字符串。

函数反向限制(str){

const arr = [...str]
让 reverse =

while(arr . length){
reverse = reverse+arr . pop()
}

返回反向
}

const stringvalue = "ABCDEFGH "

console . log(reverse string(string value))

## 输出:

> "HGFEDCBA"

[![](img/a392ed82559b7f7ce72ea3a4f0c9d09d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CDsKhWLp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://3.bp.blogspot.com/-EmU8UkZlLhQ/W_9ntXXwOVI/AAAAAAAACLg/57s1MfWVjaoNsi36sayL0xulQA8u8fBDACLcBGAs/s400/reverse.png)
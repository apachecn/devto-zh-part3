# 正则表达式备忘单

> 原文：<https://dev.to/emmabostian/regex-cheat-sheet-2j2a>

正则表达式(regex)用于匹配字符串的各个部分。下面是我创建正则表达式的备忘单。

* * *

**测试正则表达式**

*   使用`.test()`方法

```
let testString = "My test string";
let testRegex = /string/;
testRegex.test(testString); 
```

**测试多个模式**

*   使用 OR 运算符(|)

```
const regex = /yes|no|maybe/; 
```

**忽略案例**

*   使用`i`标志区分大小写

```
const caseInsensitiveRegex = /ignore case/i;
const testString = 'We use the i flag to iGnOrE CasE';
caseInsensitiveRegex.test(testString); // true 
```

**提取变量的第一个匹配**

*   使用`.match()`功能

```
const match = "Hello World!".match(/hello/i); // "Hello" 
```

**提取数组中的所有匹配项**

*   使用`g`标志

```
const testString = "Repeat repeat rePeAT";
const regexWithAllMatches = /Repeat/gi;
testString.match(regexWithAllMatches); // ["Repeat", "repeat", "rePeAT"] 
```

**匹配任意字符**

*   使用通配符`.`作为任何字符的占位符

```
// To match "cat", "BAT", "fAT", "mat"
const regexWithWildcard = /.at/gi;
const testString = "cat BAT cupcake fAT mat dog";
const allMatchingWords = testString.match(regexWithWildcard); // ["cat", "BAT", "fAT", "mat"] 
```

**匹配单个字符的多种可能性**

*   使用字符类，它允许您定义一组想要匹配的字符
*   你把它们放在方括号内`[]`

```
// Match "cat" "fat" and "mat" but not "bat"
const regexWithCharClass = /[cfm]at/g;
const testString = "cat fat bat mat";
const allMatchingWords = testString.match(regexWithCharClass); // ["cat", "fat", "mat"] 
```

**匹配字母表中的字母**

*   使用字符集`[a-z]`内的范围

```
const regexWithCharRange = /[a-e]at/;
const catString = "cat";
const batString = "bat";
const fatString = "fat";

regexWithCharRange.test(catString); // true
regexWithCharRange.test(batString); // true
regexWithCharRange.test(fatString); // false 
```

**匹配特定的数字和字母**

*   您也可以使用连字符来匹配数字

```
const regexWithLetterAndNumberRange = /[a-z0-9]/ig;
const testString = "Emma19382";
testString.match(regexWithLetterAndNumberRange) // true 
```

**匹配单个未知字符**

*   要匹配一组您*不想要的字符，请使用否定字符集*
*   要否定一个字符集，请使用插入符号`^`

```
const allCharsNotVowels = /[^aeiou]/gi;
const allCharsNotVowelsOrNumbers = /[^aeiou0-9]/gi; 
```

**匹配在一行中出现一次或多次的字符**

*   使用`+`符号

```
const oneOrMoreAsRegex = /a+/gi;
const oneOrMoreSsRegex = /s+/gi;
const cityInFlorida = "Tallahassee";

cityInFlorida.match(oneOrMoreAsRegex); // ['a', 'a', 'a'];
cityInFlorida.match(oneOrMoreSsRegex); // ['ss']; 
```

**匹配在一行中出现零次或多次的字符**

*   使用星号`*`

```
const zeroOrMoreOsRegex = /hi*/gi;
const normalHi = "hi";
const happyHi = "hiiiiii";
const twoHis = "hiihii";
const bye = "bye";

normalHi.match(zeroOrMoreOsRegex); // ["hi"]
happyHi.match(zeroOrMoreOsRegex); // ["hiiiiii"]
twoHis.match(zeroOrMoreOsRegex); // ["hii", "hii"]
bye.match(zeroOrMoreOsRegex); // null 
```

**懒搭配**

*   符合给定要求的字符串的最小部分
*   默认情况下，正则表达式是贪婪的(匹配满足给定要求的字符串的最长部分)
*   使用`?`字符进行惰性匹配

```
const testString = "catastrophe";
const greedyRexex = /c[a-z]*t/gi;
const lazyRegex = /c[a-z]*?t/gi;

testString.match(greedyRexex); // ["catast"]
testString.match(lazyRegex); // ["cat"] 
```

**匹配起始字符串模式**

*   要测试字符串开头的字符匹配，请使用插入符号`^`，但要在字符集之外

```
const emmaAtFrontOfString = "Emma likes cats a lot.";
const emmaNotAtFrontOfString = "The cats Emma likes are fluffy.";
const startingStringRegex = /^Emma/;

startingStringRegex.test(emmaAtFrontOfString); // true
startingStringRegex.test(emmaNotAtFrontOfString); // false 
```

**匹配结束字符串模式**

*   在正则表达式末尾使用美元符号`$`来检查字符串末尾是否存在模式

```
const emmaAtBackOfString = "The cats do not like Emma";
const emmaNotAtBackOfString = "Emma loves the cats";
const startingStringRegex = /Emma$/;

startingStringRegex.test(emmaAtBackOfString); // true
startingStringRegex.test(emmaNotAtBackOfString); // false 
```

**匹配所有字母和数字**

*   使用`\word`速记

```
const longHand = /[A-Za-z0-9_]+/;
const shortHand = /\w+/;
const numbers = "42";
const myFavoriteColor = "magenta";

longHand.test(numbers); // true
shortHand.test(numbers); // true
longHand.test(myFavoriteColor); // true
shortHand.test(myFavoriteColor); // true 
```

**匹配除字母&数字**之外的所有内容

*   你可以用`\W`来代替`\w`

```
const noAlphaNumericCharRegex = /\W/gi;
const weirdCharacters = "!_$!!";
const alphaNumericCharacters = "ab283AD";

noAlphaNumericCharRegex.test(weirdCharacters); // true
noAlphaNumericCharRegex.test(alphaNumericCharacters); // false 
```

**匹配所有号码**

*   你可以使用字符集`[0-9]`，或者使用简写`\d`

```
const digitsRegex = /\d/g;
const stringWithDigits = "My cat eats $20.00 worth of food a week.";

stringWithDigits.match(digitsRegex); // ["2", "0", "0", "0"] 
```

**匹配所有非数字**

*   你可以用`\D`来代替`\d`

```
const nonDigitsRegex = /\D/g;
const stringWithLetters = "101 degrees";

stringWithLetters.match(nonDigitsRegex); // [" ", "d", "e", "g", "r", "e", "e", "s"] 
```

**匹配空格**

*   使用`\s`来匹配空白和回车

```
const sentenceWithWhitespace = "I like cats!"
var spaceRegex = /\s/g;
whiteSpace.match(sentenceWithWhitespace); // [" ", " "] 
```

**匹配非空格**

*   你可以用`\S`来代替`\s`

```
const sentenceWithWhitespace = "C a t"
const nonWhiteSpaceRegex = /\S/g;
sentenceWithWhitespace.match(nonWhiteSpaceRegex); // ["C", "a", "t"] 
```

**匹配字符数**

*   您可以使用`{lowerBound, upperBound}`指定一行中的特定字符数

```
const regularHi = "hi";
const mediocreHi = "hiii";
const superExcitedHey = "heeeeyyyyy!!!";
const excitedRegex = /hi{1,4}/;

excitedRegex.test(regularHi); // true
excitedRegex.test(mediocreHi); // true
excitedRegex.test(superExcitedHey); //false 
```

**匹配最低数量的字符计数**

*   您可以使用`{lowerBound,}`来定义最小数量的字符要求
*   这被称为数量说明符

```
const regularHi = "hi";
const mediocreHi = "hiii";
const superExcitedHey = "heeeeyyyyy!!!";
const excitedRegex = /hi{2,}/;

excitedRegex.test(regularHi); // false
excitedRegex.test(mediocreHi); // true
excitedRegex.test(superExcitedHey); //false 
```

**匹配精确的字符数**

*   您可以使用`{requiredCount}`指定字符需求的确切数量

```
const regularHi = "hi";
const bestHi = "hii";
const mediocreHi = "hiii";
const excitedRegex = /hi{2}/;

excitedRegex.test(regularHi); // false
excitedRegex.test(bestHi); // true
excitedRegex.test(mediocreHi); //false 
```

**匹配全部或不匹配一个字符**

*   要检查字符是否存在，使用`?`

```
const britishSpelling = "colour";
const americanSpelling = "Color";
const languageRegex = /colou?r/i;

languageRegex.test(britishSpelling); // true
languageRegex.test(americanSpelling); // true 
```
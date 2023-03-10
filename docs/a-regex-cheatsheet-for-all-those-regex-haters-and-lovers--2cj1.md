# 这是给所有讨厌正则表达式的人(和爱好者)的一个正则表达式备忘单👀

> 原文：<https://dev.to/catherinecodes/a-regex-cheatsheet-for-all-those-regex-haters-and-lovers--2cj1>

# 我使用 regex 的经历

我一直对 regex 敬而远之。在我第一年的计算机科学实验室中，有一个涉及 regex 的练习。我想那是我第一次被介绍给它。我当时认为这很酷，但它似乎太难了，所以我一直避免它或只是谷歌如何解决某个正则表达式的问题。但是我终于花了一些时间来正确地学习它🎉

[![](img/0c6cbcccfc3657a761d4a605ca471ed8.png)](https://i.giphy.com/media/czoS1CAP2YZBS/giphy.gif)

在阅读了一些参考资料并涉猎了一些内容之后，可以肯定地说我不再害怕 regex 了！我发现自己在做的许多编码练习中都在使用它。所需要的只是练习！下面是我收集的关于我所学的正则表达式和我所使用的资源的备忘单(有例子)👀

# 备忘单

我包含了一些我学到的 Javascript 中没有的正则表达式。对于这些，我都注释掉了。需要的话记得“g”修饰语！在我的例子中，我省略了修饰语。

```
let regex;

/* matching a specific string */
regex = /hello/; // looks for the string between the forward slashes (case-sensitive)... matches "hello", "hello123", "123hello123", "123hello"; doesn't match for "hell0", "Hello" 
regex = /hello/i; // looks for the string between the forward slashes (case-insensitive)... matches "hello", "HelLo", "123HelLO"
regex = /hello/g; // looks for multiple occurrences of string between the forward slashes...

/* wildcards */
regex = /h.llo/; // the "." matches any one character other than a new line character... matches "hello", "hallo" but not "h\nllo"
regex = /h.*llo/; // the "*" matches any character(s) zero or more times... matches "hello", "heeeeeello", "hllo", "hwarwareallo"

/* shorthand character classes */
regex = /\d/;  // matches any digit
regex = /\D/;  // matches any non-digit
regex = /\w/;  // matches any word character (a-z, A-Z, 0-9, _)
regex = /\W/;  // matches any non-word character
regex = /\s/;  // matches any white space character (\r (carriage return),\n (new line), \t (tab), \f (form feed))
regex = /\S/;  // matches any non-white space character

/* specific characters */
regex = /[aeiou]/; // matches any character in square brackets
regex = /[ck]atherine/; // matches catherine or katherine
regex = /[^aeiou]/; // matches anything except the characters in square brackets

/* character ranges */
regex = /[a-z]/; // matches all lowercase letters
regex = /[A-Z]/; // matches all uppercase letters
regex = /[e-l]/; // matches lowercase letters e to l (inclusive)
regex = /[F-P]/; // matches all uppercase letters F to P (inclusive)
regex = /[0-9]/; // matches all digits
regex = /[5-9]/; // matches any digit from 5 to 9 (inclusive)
regex = /[a-zA-Z]/; // matches all lowercase and uppercase letters
regex = /[^a-zA-Z]/; // matches non-letters

/* matching repetitions using quantifiers */
regex = /(hello){4}/; // matches "hellohellohellohello"
regex = /hello{3}/; // matches "hellooo" and "helloooo" but not "helloo"
regex = /\d{3}/; // matches 3 digits ("312", "122", "111", "12312321" but not "12")
regex = /\d{3,7}/; // matches digits that occur between 3 and 7 times (inclusive)
regex = /\d{3,}/; // matches digits that occur at least 3 times

/* matching repetitions using star and plus */
regex = /ab*c/; // matches zero or more repetitions of "b" (matches "abc", "abbbbc", "ac")
regex = /ab+c/; // matches one or more repetitions of "b" (matches "abc", "abbbbc", but not "ac")

/* matching beginning and end items */
regex = /^[A-Z]\w*/; // matches "H", "Hello", but not "hey"
regex = /\w*s$/; // matches "cats", "dogs", "avocados", but not "javascript"

/* matching word boundaries 

positions of word boundaries:
1\. before the first character in string (if first character is a word character)
2\. after the last character in the string, if the last character is a word character
3\. between two characters in string, where one is a word character and the other isn't */
regex = /\bmeow\b/; // matches "hey meow lol", "hey:meow:lol", but not "heymeow lol"

/* groups */
regex = /it is (ice )?cold outside/; // matches "it is ice cold outside" and "it is cold outside"
regex = /it is (?:ice )?cold outside/; // same as above except it is a non-capturing group
regex = /do (cats) like taco \1/; // matches "do cats like taco cats"
regex = /do (cats) like (taco)\? do \2  \1 like you\?/; // matches "do cats like taco? do taco cats like you?"

//branch reset group (available in Perl, PHP, R, Delphi... commented out because this is a js file)
// regex = /(?|(cat)|(dog))\1/; // matches "catcat" and "dogdog"

/* alternative matching */
regex = /i like (tacos|boba|guacamole)\./; // matches "i like tacos.", "i like boba.", and "i like guacamole."

/* forward reference (available in Perl, PHP, Java, Ruby, etc... commented out because this is a js file) */
// regex = /(\2train|(choo))+/; // matches "choo", "choochoo", "chootrain", choochootrain", but not "train"

/* lookaheads */
regex = /z(?=a)/; // positive lookahead... matches the "z" before the "a" in pizza" but not the first "z"
regex = /z(?!a)/; // negative lookahead... matches the first "z" but not the "z" before the "a"

/* lookbehinds */
regex = /(?<=[aeiou])\w/; // positive lookbehind... matches any word character that is preceded by a vowel
regex = /(?<![aeiou])\w/; // negative lookbehind... matches any word character that is not preceded by a vowel

/* functions I find useful */
regex.test("hello"); // returns true if found a match, false otherwise
regex.exec("hello"); // returns result array, null otherwise
"football".replace(/foot/,"basket"); // replaces matches with second argument 
```

Enter fullscreen mode Exit fullscreen mode

感谢 Sarthak 为我的小抄制作了一个 GitHub 的要点，并感谢 Xian-an 翻译成中文的👏

# 资源

*   “正则表达式”挑战是“Javascript 算法和数据结构认证”的一部分，在 [FreeCodeCamp](https://freecodecamp.org) 上
*   [MDN 正则表达式文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions)
*   [RegexOne](https://regexone.com/)
*   [Regex101](https://regex101.com/) 用于测试(你也可以使用 Chrome 开发者控制台)
*   [HackerRank](https://hackerrank.com/) regex 实践挑战

[![](img/cbb3c1dd31e45e122f7c7464d6c9a9bd.png)](https://i.giphy.com/media/111ebonMs90YLu/giphy.gif)

就是这样，伙计们！希望这是有帮助的☺️
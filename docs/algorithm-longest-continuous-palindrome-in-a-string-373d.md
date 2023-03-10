# 算法:字符串中最长的连续回文

> 原文：<https://dev.to/anuj/algorithm-longest-continuous-palindrome-in-a-string-373d>

先决条件:任何语言的基本编程经验
问题:给定一个字符串，找出其中最长的连续回文。我们不应该改变任何字符的位置，而是应该找到有多少个回文，以及其中最长的一个。例如:如果给定的输入字符串是" madam "，那么可能的回文是(" m "，" a "，" d "，" a "，" m "，" ada "，" madam ")，输出将是" madam "作为其最长的回文。或者如果给定的输入是“Crore”输出将是“ror”
如果你在理解这个问题上仍然有困惑，参考下面的链接
[https://www . geeks forgeeks . org/longest-palindrome-substring-set-1/](https://www.geeksforgeeks.org/longest-palindrome-substring-set-1/)
对于这个问题我们可以假设给定的输入只包含所有小写字符。我将使用 Java 编写代码，但是您可以将这种逻辑应用到您选择的任何语言中。
方法 1-强力:
强力解决方案是考虑/生成所有子串，检查它们是否是回文，并在回文串中找到最大的。考虑到 n 是字符串的长度，该算法将以 O(n)运行。
方法 2:
我们可以通过以下方法将时间复杂度降低到 O(n)。
为了解决这个问题，我们可以尝试从每个索引中找到最大的回文。对于每个索引，我们将保持两个指针开始和结束。开始索引将代表左侧，结束索引将代表右侧。我们将检查两个索引处的字符是否相同，然后我们将开始移动到更左边，结束移动到更右边。我们一直这样做，直到左索引和右索引的字符不相同。最后，我们的子串将是回文。这个结果我们将与先前结果的长度进行比较，如果它更大，我们将更新结果。下面是完整的代码

```
public static String longestPalindrome(String str) {
    if (str == null || str.length() <= 1) return str;
    String res = str.substring(0,1);
    for (int i = 0; i < str.length(); i++) {
        String str1 = expands(str, i, i);

        String str2 = expands(str, i, i + 1);
        if(str1.length()>res.length()){
            res = str1;
        }
        if(str2.length()>res.length()){
            res = str2;
        }

    }

    return res;
}

private static String expands(String str, int start, int end) {
    while(start>=0&& end<str.length() &&  str.charAt(start)==str.charAt(end)){
        start--;
        end++;
    }
    return  str.substring(start+1, end);
} 
```

该算法运行时间复杂度为 O(n)。
方法 3:
如果你看到前面的问题，我们正在创建许多浪费内存的子串。我们仍然可以改进我们的算法，以避免每次都创建子串，而是从 expand 方法返回长度。基于长度，我们可以有我们的开始和结束索引，它可以用来提取最后的子串。下面是实现这一点的代码。

```
public static String longestPalindromic(String str) {
    if (str == null || str.length() <= 1) return str;
    int start = 0;
    int end = 0;
    for (int i = 0; i < str.length(); i++) {
        int len1 = expand(str, i, i);
        int len2 = expand(str, i, i + 1);
        int len = Math.max(len1, len2);
        if (len > end - start) {
            start = i - (len - 1) / 2;
            end = i + len / 2;
        }
    }
    return str.substring(start, end + 1);
}

private static int expand(String str, int start, int end) {
    while (start >= 0 && end < str.length() && str.charAt(start) ==     str.charAt(end)) {
        start--;
        end++;
    }
    return end - start - 1;
} 
```

感谢您的阅读。如果发现任何错误，请评论。你可以在 Twitter 和 GitHub 上关注我
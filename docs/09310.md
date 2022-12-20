# 如何构建 Web 应用程序，第 4 部分

> 原文：<https://dev.to/gtanyware/how-to-build-a-web-app-part-4-1a52>

[< < <简介](https://dev.to/gtanyware/how-to-build-a-web-app-1-3fb2) | [< < <设置](https://dev.to/gtanyware/how-to-build-a-web-app-part-2-5cln) | [< < <讲故事](https://dev.to/gtanyware/how-to-build-a-web-app-part-3-5ae3)

# 字，字，字

在本文中，我将展示一个变位词查找器组件的代码。和以前一样，我的目标受众是技术水平从初级到中级的人。

在上一篇文章中，我展示了如何通过使用 EasyCoder 脚本语言来构建应用程序的用户界面，从而使维护变得容易。然而，寻找变位词的实际工作对于这种类型的脚本来说太多了；相反，我们需要一些 JavaScript。

这是完整的源代码。我怀疑这是否是有史以来最有效的算法，但它很容易理解。我主要使用传统的 JavaScript，而不是最近的风格。

```
const AnagramFinder = {

  alphabet: [
    `a`, `b`, `c`, `d`, `e`, `f`, `g`, `h`, `i`, `j`, `k`, `l`, `m`,
    `n`, `o`, `p`, `q`, `r`, `s`, `t`, `u`, `v`, `w`, `x`, `y`, `z`
  ],

  // Get anagrams from the given text
  getAnagrams: function (text, list) {
    const words = [];
    let remaining = text;
    let found;
    while (remaining) {
      found = false;
      const reduced = AnagramFinder.reduce(list, remaining.length);
      AnagramFinder.shuffle(reduced);
      const mtext = AnagramFinder.measure(remaining);
      for (let n = 0; n < reduced.length; n++) {
        const letters = AnagramFinder.measure(reduced[n]);
        if (AnagramFinder.contains(mtext, letters)) {
          words.push(reduced[n]);
          remaining = AnagramFinder.remove(mtext, letters);
          found = true;
          break;
        }
      }
      if (!found) break;
    }
    return {
      status: found ? `found` : ``,
      words
    };
  },

  // Remove a found word from the remaining text
  remove: function(remaining, letters) {
    let result = '';
    for (let n = 0; n < 26; n++) {
      const letter = AnagramFinder.alphabet[n];
      remaining[letter] -= letters[letter];
      for (let m = 0; m < remaining[letter]; m++) {
        result += AnagramFinder.alphabet[n];
      }
    }
    return result;
  },

  // Copy a list, removing words longer than a given length
  reduce: function(list, len) {
    const result = [];
    for (let n = 0; n < list.length; n++) {
      if (list[n].length <= len) {
        result.push(list[n]);
      }
    }
    return result;
  },

  // Shuffle a list
  shuffle: function(list) {
    for (let i = list.length - 1; i > 0; i--) {
      const j = Math.floor(Math.random() * (i + 1));
      [list[i], list[j]] = [list[j], list[i]];
    }
  },

  // Check if the first string contains the second, by comparing character maps
  contains: function(a, b) {
    for (let n = 0; n < 26; n++) {
      if (a[AnagramFinder.alphabet[n]] < b[AnagramFinder.alphabet[n]]) {
        return false;
      }
    }
    return true;
  },

  // Measure a word, returning a map of letter counts
  measure: function (t) {
    const map = {};
    for (let n = 0; n < 26; n++) {
      map[AnagramFinder.alphabet[n]] = 0;
    }
    const text = t.toLowerCase();
    for (let n = 0; n < text.length; n++) {
      const c = text.charAt(n);
        if (c.toLowerCase() != c.toUpperCase()) {
          map[c]++;
        }
      }
      return map;
    }
  }; 
```

该组件被构造为单个对象，理想情况下，其名称不会与您可能添加到 web 页面的任何其他内容发生冲突。里面是它使用的所有功能。

它首先定义一个由字母表中的字母组成的数组。然后是主程序，`getAnagrams()`。这给定了要处理的文本和要使用的词典，如果有合适的单词列表，这允许该库用于英语以外的语言。

该函数使用可用的字母在字典中查找任何单词。当它找到一个字母时，它会将其添加到输出列表中，从原始文本中删除它使用的字母，然后重试。最终，它要么是把所有可用的字母都用光了，要么是找不到一个单词。无论哪种方式，它都会发回一个小对象，其中包含找到的单词和一个成功/失败标志。

在开始运行之前，字典是随机的，否则每次都会出现相同的单词。通过将单词转换成包含 26 个元素的集合来比较单词，这些元素包含字母表中每个字母在单词中出现的次数。

`remove()`函数从文本中删除一个单词。

`reduce()`函数复制原始列表，删除所有比输入文本长的单词。

`shuffle()`函数将单词列表随机化。

函数测试一个字符串是否包含另一个字符串的所有字母。这与 JavaScript 函数`indexOf()`不同，后者要求字母顺序正确。这里我们只关心它们是否存在。

`measure()`函数将一个单词转换成一个字母计数图。它使用一个简洁但不明显的技巧来判断一个字符是否是字母，通过测试当从小写转换成大写时它是否改变。只有字母学家会这么做。

## 谁做什么

变位词查找器将花费可变长度的时间来搜索一组单词，结果将是成功或失败。下一步将是再试一次，希望重新排列字典会产生一个结果。字典中有超过 30，000 个单词，在所有可能的组合都被剔除之前，这可能要做上百万次，所以问题是如何组织这些重试。当然，可以给查找器本身添加一个重试机制，但是这有一些问题:

*   JavaScript 只有一个线程，所以当 finder 运行时，其他的都将被阻塞。使用上面的功能，单个变位词搜索只需几分之一秒，即使在智能手机上，也不会被注意到，但运行数千次则是另一回事。您可以让 finder 偶尔暂停一下，并允许另一个接口接收停止消息，但是这使得与组件的接口更加复杂。除此之外，除了终止浏览器进程之外，没有其他方法可以停止程序，这是非常极端的。
*   现代计算机是短跑运动员，不是马拉松运动员。它们的速度快得令人难以置信，但只能持续很短时间，之后它们会迅速开始过热。如果你编写了一个循环代码，却忘记了增加循环计数器，几秒钟后，计算机的风扇就会启动，如果你有温度监视器，你会看到它上升到危险的水平。再说一次，阻止它的唯一方法是终止它的进程，除非你不能让它听你的，因为整个计算机都被锁住了，快要熔化了。你所能做的就是按下重置按钮。

如果你想运行字谜查找器一千次或更多次，你需要在运行之间暂停一下，让电脑喘口气，冷静一下。JavaScript 中的暂停使得代码更难理解，因为它们必须通过回调来完成，而且您还必须考虑如何告诉 finder 停止运行。

这些因素与实际寻找字谜没有什么关系，所以为了保持整洁，最好让 web 应用程序自己处理重试、暂停和用户交互。然后变位词查找器就可以完成它的工作，而不需要担心所有其他的东西。

## 库 API

从 web 应用的角度来看，这个模块只公开了一个有用的功能；`getAnagrams()`。其他功能是可用的，但在 finder 本身之外不太可能需要。总的来说，界面越少越好，因为它避免了系统看起来像一碗意大利面条，并且使得将库集成到 web 应用程序中变得更简单。

一些 web 组件——比如 Google Maps——本质上是可视化的，而另一些，比如 anagram finder，只是进行某种数据处理。重要的考虑是他们是否有一个清晰的 API 集，允许他们与他们将要使用的环境完全分离。这样，它们可以很容易地被插入和拔出，或者被测试线束等其他组件替换。当你构建一个网页时，寻找真正独立的部分，并把它们作为独立的组件来构建。这减少了您必须管理的代码的大小，并且在许多情况下提高了可靠性，因为组件适合在几个地方使用，这增加了它们经历的测试量。

## 即将到来...

本系列的最后一部分是一个附录，描述了 EasyCoder 的插件机制，它支持额外的特性，比如将变位词查找器无缝地添加到语言中。除了那些使用 EasyCoder 并希望用这种语言处理外部功能的人，其他人都不会对它感兴趣。

对于其他人，我想感谢您阅读本系列，并祝您在构建自己的 web 应用程序时成功和愉快，无论您选择如何做。

克拉克·蒂布斯在 [Unsplash](https://unsplash.com/search/photos/words?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的标题照片
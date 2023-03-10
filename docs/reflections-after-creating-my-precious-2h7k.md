# 创作《我的宝贝》后的思考

> 原文：<https://dev.to/jessicabetts/reflections-after-creating-my-precious-2h7k>

# 简介

[![its-done](img/0819c2f432c7c27531e2d98b63d6caf8.png)](https://i.giphy.com/media/3oKIPf3C7HqqYBVcCk/giphy.gif)

上周，在熨斗学校，克里斯·汉克斯和我发布了一种叫做 Precious 的深奥的编程语言，它就像一颗红宝石。Precious 使用可以翻译成 Ruby 代码的 LOTR 关键字。查看这里的源代码: [My_Precious](https://github.com/jessicabettsftw/my_precious)

# 宝石利弊

[![my-precious](img/28735d5ca6ddd7e5568d321438d80d3a.png)](https://i.giphy.com/media/3oFyCVxsQn6RBa0r5u/giphy.gif)

将这个项目变成一个珍宝很可能是我们所能做出的最好的决定。作为一个独立的项目，这很有趣。但是，从一个脚本改变项目的脚手架允许我们探索一个新的领域。在这个项目之前，我从未想过要创造自己的宝石。自从它发布以来，我真的为我们所做的工作感到骄傲。拥有一些人们可以积极去看、玩和贡献的东西是非常鼓舞人心的。最重要的是，看着下载量增加是非常令人兴奋的。

## 缺点:

当我们第一次试图把我们的项目变成一个宝石，我只是去了。我事先没有做很多研究，遇到了很多麻烦。我浪费了几个小时的时间，删除了一个分支代码，不得不请求帮助返回并恢复到以前的保存点。在这样做的时候，我丢失了一些重要的提交。所以如果我能给你一些建议的话，我会说提前计划！当你构建你的 Gem 时，从单独的根目录开始！我计划我的下一个博客是一步一步如何建立一个宝石，因为我自己犯了这么多错误，我可以用它们来帮助别人。

这是一次学习经历。当我们试图测试目录中的代码时，我们不知道如何查看它是否正常工作。基本上。如何测试宝石内部的宝石？我们唯一能想到的就是发布代码。在代码准备好之前就发布让我非常焦虑。第一印象至关重要，所以让别人判断和看到不符合我标准的代码，有点让我抓狂。我不停地烦我的伙伴去修正错误、措辞和自述文件，让它们尽可能地接近完美。如果能重来一次，我会想办法在发行之前测试一下创业板！

# 解析器

代码又大又重复又乱。到目前为止，我对我们所做的大量重构感到非常满意，但是我确实看到了很多改进的空间。下面是一个我很想重构的领域的例子。

```
 #check if there are strings in line
    quote = ""
    if line.include? '"'
      quote = get_quote(line)
      line = line.gsub(quote, 'quote_placeholder')
    end

    #check if line is a comment
    comment = ""
    if COMMENT_KEYWORDS.any? { |word| line.include?(word) }
      line = parse(line, COMMENT_KEYWORDS)
      comment = store_important(line, '#')
      line = line.gsub(comment, 'comment_placeholder')
    end

    #check if line has params
    params = ""
    if ((PARAM_KEYWORDS.any? { |word| line.include?(word) }) &&
      ((FUNCTION_DEF_KEYWORDS.any? { |word| line.include?(word) }) ||
      (FUNCTION_CALL_KEYWORDS.any? { |word| line.include?(word) })))
      line = parse(line, PARAM_KEYWORDS)
      params = remove_newline(store_important(line, '('))
      line = line.gsub(params, 'param_placeholder ')  + ')'
    end

    class_word = ""
    if CLASS_KEYWORDS.any? { |word| line.include?(word) }
      line = parse(line, CLASS_KEYWORDS)
      class_word = store_important(line, ':')
      line = line.gsub(class_word, ' class_word_placeholder')
    end 
```

我还认为，在这一点上，我认为我们的解析是以“简单”的方式完成的。我们基本上检查一个“行”或者一个字符串是否包含关键字或者另一个字符串。我绝对愿意通过正则表达式匹配来实现这一点。例如，我们真的想拥有打字的能力:

```
If Gollum is as smart as Smeagle
    gandalf says "fly, you fools!"
you shall not pass 
```

然而，把“as”和“is”都作为关键字给我们带来了很多麻烦。这一行可以翻译成:

```
if gollum === smeagle
    puts "fly, you fools!"
end 
```

并且===对 Ruby 无效:(

我打算在未来实现的其他功能是，代替逐行读取文件，我希望解析器寻找句子结束标记，如“！,.；?"这样，代码可以以段落的形式编写，而不是逐行编写。我觉得这种解析和我们的设计配合得很好，因为它是基于英语的，而且读起来几乎像一个故事。

最重要的是——修复当前方法定义的错误

长期目标:

*   面向对象的能力
*   有珍贵的评价而不是翻译

[![keep going](img/179678cc7465c8aed4eaff32a06f57f8.png)](https://i.giphy.com/media/Sg4He6Tb4LoXK/giphy.gif)

基本上我想说的是，虽然项目已经结束...我打算长期坚持下去！
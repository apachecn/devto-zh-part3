# 向我的博客添加自动文本林挺

> 原文：<https://dev.to/raymondcamden/adding-automated-text-linting-to-my-blog-286l>

我从事技术写作已经将近 20 年了，如果有一件事让我越来越欣赏的话，那就是一个好编辑的力量。在我职业生涯的不同阶段，我都有过编辑，坦率地说，毫无疑问，在他们的帮助下，我的写作水平有了很大提高。虽然我相信一个真正的人来检查我的工作是最好的，但我很好奇我是否可以使用我上周遇到的一个工具来添加一点自动化。

textlint 是一项林挺服务，它基于各种规则来查找文本中的不同问题。规则清单相当长，坦白地说，有点让人不知所措。例如，第二个记录的规则具有这样的功能:“该规则检查没有以重复的连接开始。”如果你知道什么是“重复连词”，请举手。

我浏览了一下清单，添加了我认为有意义的内容。每条规则也有配置选项，但在大多数情况下，我保留了它们的默认值。我使用了以下规则:

*   no-start-duplicated-connection:我仍然不明白这到底是什么意思，但它似乎可以防止多个句子以“但是”、“所以”等词开头。
*   无死链接:这个非常酷——它检查你的链接，以确保它们确实可以解析。
*   术语:寻找类似“JavaScript”而不是“Javascript”和“npm”而不是“NPM”的东西
*   无匹配对:基本上使用 a(忘记了)。
*   亚历克斯:这真的很有趣——它寻找可能具有攻击性或不具包容性的文本。举个简单的例子，用“mailman”代替“mailperson”或“邮政工人”。显然，这是你可能不关心的事情，但它绝对吸引了我。
*   拼写检查器:我在 Visual Studio 代码中使用拼写检查器，但有时我会忽略警告。不幸的是，这条规则现在对我来说被打破了，没有标记任何问题，但我在他们的存储库中有一个问题。

有了这些规则，我可以像这样对一篇文章进行人工检查:

```
./node_modules/.bin/textlint ./_posts/2018/12/13/2018-12-13-using-alexa-to-mess-with-your-kids-because-why-not.md 
```

Enter fullscreen mode Exit fullscreen mode

这里是输出:

```
11:1 error [postman-postwoman] `mailman` may be insensitive, use `mail carrier`, `letter carrier`, `postal worker` instead alex
   11:630 error [kids] Be careful with “kids”, it’s profane in some cases alex
   13:180 error [kid] Be careful with “kid”, it’s profane in some cases alex
   22:194 error [crap] Don’t use “crap”, it’s profane alex
  129:330 error [screw] Reconsider using “screw”, it may be profane alex

✖ 5 problems (5 errors, 0 warnings) 
```

Enter fullscreen mode Exit fullscreen mode

相当酷！现在有趣的部分来了——将它集成到一个自动化的过程中。Elijah Manor 有一篇关于这个的很棒的文章([“在 git 预提交钩子中运行 npm 脚本”](https://elijahmanor.com/npm-precommit-scripts/))。在本文中，他讨论了如何在通过 Git 提交时自动运行 linters。虽然这篇文章很好，但他现在推荐了另一个工具，叫做[哈士奇](https://github.com/typicode/husky)。最后，我还必须添加 [lint-staged](https://github.com/okonet/lint-staged) ，一个处理识别将要提交的内容并在其上运行 linter 的工具。

在我的 repo 中添加了这两个工具之后，我修改了我的 package.json，添加了以下内容:

```
"husky": {
    "hooks": {
        "pre-commit": "lint-staged"
    }
},
"lint-staged":{
    "*.md":"textlint"
} 
```

Enter fullscreen mode Exit fullscreen mode

然后做了一个测试:

```
✖ textlint found some errors. Please fix them and try committing again.

/mnt/c/projects/raymondcamden2018/_posts/2018/12/28/2018-12-28-adding-automated-text-linting-to-my-blog.md
22:210 ✓ error Incorrect usage of the term: “repo”, use “repository” instead terminology
40:236 error [he-she] `he` may be insensitive, use `they`, `it` instead alex
40:345 error [he-she] `he` may be insensitive, use `they`, `it` instead alex

✖ 3 problems (3 errors, 0 warnings) 
```

Enter fullscreen mode Exit fullscreen mode

很好。我完全同意回购/存储库的评论，但后两个不适用，因为我在谈论一个单数的人。(如果我说错了，请在下面给我留言！)我现在可以在发表文章之前获得反馈，如果我不同意，我只需:`get commit -m "something" --no-verify`绕过检查。

你怎么想呢?在下面给我留言，让我知道你是否认为这个过程有意义，或者你是否会以不同的方式做事情。
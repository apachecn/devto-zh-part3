# HashTables -赎金通知(HackerRank)

> 原文：<https://dev.to/jeff_codes/hashtables-ransom-note-hackerrank-1p14>

嘿伙计们。这是我在这里的第一个帖子，所以请温柔点！作为一名即将毕业的计算机科学学生，我想我最好温习一下我的数据结构&编码面试的算法。我一直在使用 HackerRank 进行练习，但是没有注意到很多高质量的 JavaScript 解决方案和解释。我想改变这一切！我会尽可能多地尝试发布其中一个问题的解决方案。所以这里什么都没有！

## 哈希表-赎金笔记[链接！](https://www.hackerrank.com/challenges/ctci-ransom-note/problem?h_l=interview&isFullScreen=false&playlist_slugs%5B%5D%5B%5D%5B%5D=interview-preparation-kit&playlist_slugs%5B%5D%5B%5D%5B%5D=dictionaries-hashmaps)

### 问题

实际上，你得到了一本杂志和一张你想从这本杂志中创作的便条。杂志和便笺都是字符串数组。这基本上可以归结为:音符数组中的字符串是否存在于杂志数组中。(请通过上面的链接阅读整个问题)

### 要考虑的事情

*   你必须考虑到这些词在杂志中的出现频率
    *   例如:magazine = ['我真的应该学习物理'] note = ['我真的应该学习物理']
    *   如果你只是简单地检查一下纸条上的每一个字是否都包含在杂志里，你会得到肯定的答案。然而，上面的注释需要两个“真的”，因此正确答案应该是“不”

### 我的解决方案

在我的解决方案中，我利用了一个哈希表(只是 javaScript 中的一个普通对象)来计算杂志中每个单词的出现频率。

这里说够了就是代码！

```
function checkMagazine(magazine, note) {
    let magDict = {};
    let works = '';

    //Populate magDict with words from the magWordArr
    for (let i = 0; i < magazine.length; i++){
        let word = magazine[i];
        magDict[word] = magDict[word] ? magDict[word] + 1 : 1; 
    }

    //Loop through the note and compare with magazine object
    for (let i = 0; i < note.length; i++){
        //magDict has the word needed for the note
        if (!(note[i] in magDict)) {
            works = 'No';
            break;
        } else {
            //In the magDict
            if (magDict[note[i]] < 1) {
                works = 'No'
                break;
            }
            //Subtract one from the number of words in dict
            magDict[note[i]] = magDict[note[i]] - 1;
        }
    }
    if (works === '') { works = 'Yes' }
    console.log(works);
} 
```

Enter fullscreen mode Exit fullscreen mode

1.  我做的第一件事是用 magazine 数组中的所有值填充 magDict，并跟踪单词的频率。

例如:我得到了一串可爱的椰子

`{ ive: 1, got: 1, a: 1, lovely: 1, bunch: 1, of: 1, coconuts: 1 }`

1.  然后我循环了笔记数组中的每个单词
    *   我检查该单词是否包含在杂志对象中。如果不是，则将“工作”标志设置为“否”并中断循环，因为继续循环没有意义。
    *   如果在料盒对象中，检查频率是否大于 1。如果不是，则将“工作”标志设置为“否”,然后中断循环。
    *   快乐之路！它在杂志对象中，并且频率大于 1。我们要“使用”这个词，所以我们要从频率中减去 1。
2.  最后，我们检查“works”标志是否仍然是一个空字符串，以及它是否将其更改为 Yes。

### 最后的想法

这只是我的解决方案。这个问题可以用许多不同的方法来解决。如果你自己解决了，请在评论中留下，这样我们都可以学习。此外，请随时留下您对我的解决方案的反馈！
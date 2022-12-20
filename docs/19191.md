# 呃，装船吧！

> 原文：<https://dev.to/captainsafia/eh-ship-it-hg>

这是代码 2018 挑战来临的第二天。再一次，我在这里通过博客介绍解决方案。在开始之前，你应该先看一下[日的挑战](https://adventofcode.com/2018/day/2)。

我首先将盒子 id 列表下载到一个`box-ids.txt`文件中。一般程序会要求我们遍历 ID，记录我们在 ID 中找到两个和三个相同字母的次数，然后将这些计数相乘。

因为我决定每天用不同的语言进行挑战。今天，我将在 Node 中写出解决方案。节点标准库没有 Python 的标准库丰富。Python 中有一些标准库非常适用于这个问题，但是没关系。我在这里抛弃了算法的初始迭代。

```
const fs = require("fs");

function getOccurenceCount(string) {
  let result = {};
  for (var index in string) {
    const char = string.charAt(index);
    result[char] = (result[char] || 0) + 1;
  }
  return result;
}

fs.readFile("box-ids.txt", (error, boxIdsBuffer) => {
  if (error) console.error(error);

  let countOfTwos = 0;
  let countOfThrees = 0;
  const boxIds = boxIdsBuffer.toString().split("\n");
  for (let index in boxIds) {
    const boxId = boxIds[index];
    occurenceCount = getOccurenceCount(boxId);
    occurenceCountList = Object.values(occurenceCount);
    countOfTwos += occurenceCountList.includes(2);
    countOfThrees += occurenceCountList.includes(3);
  }
  console.log(countOfThrees * countOfTwos);
}); 
```

有趣的事实是，我实际上花了大约 5 分钟的额外时间来获得这个初始迭代，因为我没有完全阅读说明(真可耻！).我假设你必须加上一个字符出现两次的次数，而不仅仅是一个字符出现两次。那句话措辞笨拙，但是从问题陈述中复制和粘贴应该有希望阐明我的意思。

```
abcdef contains no letters that appear exactly two or three times.
bababc contains two a and three b, so it counts for both.
abbcde contains two b, but no letter appears exactly three times.
abcccd contains three c, but no letter appears exactly two times.
aabcdd contains two a and two d, but it only counts once.
abcdee contains two e.
ababab contains three a and three b, but it only counts once. 
```

事实证明，这个特殊的谜题还有第二部分。我会把它的链接[贴在这里](https://adventofcode.com/2018/day/2#part2)。如果你没有完成第一部分，我不确定你是否能看到它。我现在将致力于第二部分，然后在以后优化这两个解决方案，因为相信我，上面肯定会在某些地方使用一点爱。

对于第二部分，我们将需要找到仅相差一个字母的 id。我敢打赌，我需要计算密码字符串之间的[汉明距离](https://en.wikipedia.org/wiki/Hamming_distance)。

中场休息:距离我写最后一句话，已经过去了 8 个小时左右。我必须去做一个人，所以现在我回来赶着完成这一切。下面是我为计算只有一个字符串差异的两个密码而设计的简单解决方案。

```
const fs = require("fs");

function getOccurenceCount(string) {
  let result = {};
  for (var index in string) {
    const char = string.charAt(index);
    result[char] = (result[char] || 0) + 1;
  }
  return result;
}

function hammingDistance(s, t) {
  let distance = 0;

  for (let index in s) {
    if (s[index] !== t[index]) {
      distance += 1;
    }
  }

  return distance;
}

fs.readFile("box-ids.txt", (error, boxIdsBuffer) => {
  if (error) console.error(error);

  let countOfTwos = 0;
  let countOfThrees = 0;

  const boxIds = boxIdsBuffer.toString().split("\n");
  for (let index in boxIds) {
    const boxId = boxIds[index];
    occurenceCount = getOccurenceCount(boxId);
    occurenceCountList = Object.values(occurenceCount);
    countOfTwos += occurenceCountList.includes(2);
    countOfThrees += occurenceCountList.includes(3);
  }

  console.log(countOfThrees * countOfTwos);

  for (let index in boxIds) {
    const boxId = boxIds[index];
    boxIds.map(otherBoxId => {
      if (hammingDistance(boxId, otherBoxId) === 1) {
        for (let index in boxId) {
          if (boxId[index] === otherBoxId[index]) {
            process.stdout.write(boxId[index]);
          }
        }
        return;
      }
    });
  }
}); 
```

好吧！这就完成了任务。有几种方法可以让它变得更好。使用 streams 实例将缓冲区转换为字符串并对其进行迭代，减少重复 for 循环的数量，清理出现次数的计算。

这是我做的最后清理。呃，它起作用了。装运它！

```
const fs = require("fs");

function getOccurenceCount(string) {
  let result = {};
  for (var index in string) {
    const char = string.charAt(index);
    result[char] = (result[char] || 0) + 1;
  }
  return result;
}

function hammingDistance(s, t) {
  let distance = 0;

  for (let index in s) {
    if (s[index] !== t[index]) {
      distance += 1;
    }
  }

  return distance;
}

fs.readFile("box-ids.txt", (error, boxIdsBuffer) => {
  if (error) console.error(error);

  let countOfTwos = 0;
  let countOfThrees = 0;

  const boxIds = boxIdsBuffer.toString().split("\n");
  for (let index in boxIds) {
    const boxId = boxIds[index];
    occurenceCount = getOccurenceCount(boxId);
    occurenceCountList = Object.values(occurenceCount);
    countOfTwos += occurenceCountList.includes(2);
    countOfThrees += occurenceCountList.includes(3);

    boxIds.map(otherBoxId => {
      if (hammingDistance(boxId, otherBoxId) === 1) {
        console.log(
          Array.from(boxId)
            .filter((character, index) => {
              return character === otherBoxId[index];
            })
            .join("")
        );
      }
    });
  }
  console.log(countOfThrees * countOfTwos);
}); 
```
# AoC 第 1 天:长期校准

> 原文：<https://dev.to/rpalo/aoc-day-1-chronal-calibration-264e>

*   [概述](#overview)
*   [我的解决方案](#my-solution)
*   [开发者排行榜](#dev-leaderboards)

## 概述

在我昨晚的帖子之后， [@aspittel](https://dev.to/aspittel) 建议我们开始一个针对这些挑战的每日讨论帖子链。我认为这是一个好主意，比每个人都说“嘿，看看我的 git repo！”要有效得多

我对今年的降临节任务感到兴奋，时间旅行拯救圣诞节！这将是一个讨论今天的问题，时间校准的地方。我们的任务是通过累加连续的整数来校准我们的时间旅行装置。

另外，如果你有额外的时间，建立一些从文件中读取输入文本的基础设施可能是个好主意，因为去年几乎所有的挑战赛都给出了一些简单的例子(有利于编写一些基本的测试)，然后是一个多行明文文件形式的庞大输入。

如果你还没有解决今天的难题，现在就停止阅读，因为我要在这里发布我的解决方案。如果你*已经*完成了一个解决方案，请在评论中发布！也可以随意给别人的解决方案提供*(充满爱心、指导性、友善)*的反馈，帮助他们读得更好或跑得更快。我看到一些人正在利用这个机会学习一门新的语言，所以如果你看到一些有用的东西，但对你最喜欢的语言来说并不地道，那就提出建议吧！这也包括我，因为我刚刚开始学习 Rust，所以我确信这将是一个拙劣翻译的 Python-isms 和官样文章的混合体。

## 我的解决方案

```
// day1.rs

/// Given a bunch of integers (changes in frequency), one per line,
/// calculate the final sum
pub fn final_frequency(text: &str) -> i32 {
    text.lines()
        .map(|value: &str| -> i32 {value.parse().expect("Not a number")})
        .sum()
}

/// Given a bunch of integers (changes in frequency), one per line,
/// calculate the running sum until you hit a cumulative sum that you've
/// seen before.  Return that first repeated cumulative sum.
pub fn first_duplicate_frequency(text: &str) -> i32 {
    let mut history = vec![0];
    let mut total = 0;
    for line in text.lines().cycle() {
        let value: i32 = line.parse().expect("Not a number.");
        total += value;
        if history.contains(&total) {
            return total;
        }
        history.push(total);
    }
    return 0;
}

#[cfg(test)]
mod tests {
    use super::final_frequency;
    use super::first_duplicate_frequency;

    #[test]
    fn final_all_positives() {
        assert_eq!(3, final_frequency("+1\n+1\n+1"));
    }

    // More tests... 
```

Enter fullscreen mode Exit fullscreen mode

```
// main.rs

// This file will change day to day as I just use it to exercise each day's module.

use std::fs::File;
use std::io::prelude::*;

mod day1;

fn main() {

    let mut input = File::open("data/day1.txt").expect("File not found.");

    let mut contents = String::new();
    input.read_to_string(&mut contents).expect("Couldn't read file.");
    println!("{}", day1::first_duplicate_frequency(&contents));
} 
```

Enter fullscreen mode Exit fullscreen mode

## 开发者排行榜

最后，我为 DEV.to 创建了一个“私人”排行榜，以防有人感兴趣。显然，每个排行榜只能容纳 200 人，所以先到先得。显然，DEV 家族中有超过 200 人，所以如果你到了那里，房间已经满了，请在[代码网站](https://adventofcode.com/2018/leaderboard/private)上创建你自己的房间代码，并在评论中发布你的房间代码。如果我看到有人张贴房间代码，我会尽我所能编辑我的帖子并复制到下面，但通读评论以防我错过它。

快乐——我是说，**快乐编码！**我很想知道人们的想法。

[@rpalo](https://dev.to/rpalo) : 224198-25048a19
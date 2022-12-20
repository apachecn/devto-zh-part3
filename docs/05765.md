# 无可奉告？

> 原文：<https://dev.to/jessicabetts/no-comment-s-pnh>

[![no comment](img/fed5c35f96737f0e827b84cee4a028cc.png)](https://i.giphy.com/media/1iuHoo38f5o1n7vvbs/giphy.gif)

我只是想解决这个问题…我知道给你的代码加注释很乏味！我们许多开发人员不喜欢它，并且经常完全忽视它。短期来看很糟糕。它占用时间而且很无聊。但是它超级重要。我保证评论是为了帮助你！！！评论的目的是让你的生活更轻松。我写这个博客是因为我觉得教育中对评论的重要性强调得不够。

## 都是关于你的！

[![all about me](img/5b6b13f2e79bf3cf3abfe8d51132a30d.png)](https://i.giphy.com/media/7dWcOP3IMbgXLrFm8K/giphy.gif)

可读性。可读性。可读性。

假设您已经创建了一个项目。6 个月后，你决定重新编写代码，并用你刚学过的新语言重写。你正在仔细阅读你的代码，遇到一个你不理解或者不记得写过的函数。想象一下，如果有一些注释来解释正在发生的事情，代码会变得更加容易理解。

```
 def self.valuable?(word) #checks to see if the word should be kept
    valuable = false
    if /[[:upper:]]/.match(word[0]) #if variable
      valuable = true
    elsif word == '"quote_placeholder"' #if string/quote
      valuable = true
    elsif word == '#comment_placeholder' #if comment
      valuable = true
    elsif word == '(param_placeholder' #if params
      valuable = true
    elsif word == 'class_word_placeholder' #if class
      valuable = true
    elsif word.to_i.to_s == word #if num
      valuable = true
    elsif OPERATOR_KEYWORDS.include? word #if operator
      valuable = true
    end
    valuable
  end 
```

## 不全是你！

[![comes across as selfish](img/8f967016cd00990fec9255081154ac93.png)](https://i.giphy.com/media/lw40GmL1z9l5f88B2R/giphy.gif)

当您在开发团队中工作或者接管遗留代码时，这个概念变得更加重要。大多数人都会同意，调试别人的代码比调试自己的代码要难得多。然而，这就是开发商的所作所为。有时你为新问题编写新的解决方案，但通常你会修正别人的旧代码。因为如果别人的代码有注释，那么理解它会更容易，所以你也应该这样做。

### 抽象

注释可以让不熟悉你的代码的人更快地理解。不需要一行一行地阅读，你可以在评论中简单地阅读它的功能。这也让非程序员能够更清楚地理解你的代码。如果之前的开发人员没有对他们的代码进行注释并做好，那么理解一个方法的功能所花费的时间将会大大增加。

## 什么时候应该发表评论

我认为我们有责任对所有代码进行注释:

*   其他人会触摸/看到
*   漫长的
*   将来会被使用/查看
*   不是超直观的
*   不遵守单一责任原则
*   不符合描述性命名约定

基本上一直都是。

## 例子:

函数名
输入:类型
输出:类型
目的:完成内容的简要描述。

```
/*
findStart
input: an array of brewery objects
output: a single brewery object
purpose: finds which brewery you should start with on your brewery crawl
 by:
 - finding the center point of all breweries
 - finding which brewery is farthest from that center point
*/

static findStart(breweries) {
  let coords = Map.getCoords(breweries);
  let center = Map.findCenterPoint(coords)

  let greatest_diff = 0
  let start = null
  breweries.forEach(brewery => {
    let diffLat = Math.abs(center.lat - parseFloat(brewery.latitude))
    let diffLng = Math.abs(center.lng - parseFloat(brewery.longitude))
    let totalDiff = (diffLat * diffLat) + (diffLng * diffLng)
    if (totalDiff > greatest_diff) {
      greatest_diff = totalDiff
      start = brewery
    }
  })
  return start
} 
```

帮你自己一个忙。帮你的编码同事一个忙。注释您的代码。
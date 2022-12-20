# CSS 预处理程序如何为您节省时间和金钱

> 原文：<https://dev.to/ispirett/how-css-preprocessors-save-you-time-and-money-ublished-false-1g09>

* * *

作为成长中的程序员，我们在寻找简单有效的方法来完成任务方面的旅程永无止境。便利和轻松让我们更快乐，也让开发者更有效率，因此，近年来框架、库、预处理器等快速增长。

# CSS 预处理器

CSS 预处理程序是我在上一段中提到的便利工具之一。这些工具允许我们在更高的抽象层次上编写 CSS，使我们能够自动化大量的 ccs 代码，否则我们将不得不手动编写。我知道我知道，作为程序员，我们不喜欢项目中有太多的依赖。别担心，这段代码会编译成 CSS，所以它只在您的开发环境中需要。
下面是 CSS 预处理程序的一些例子:
Css

这是一个列宽网格系统的例子，类似于 bootstrap 的列。

`.col-md-1 {
flex: 0 0 8.3333333333%; }
.col-md-2 {
flex: 0 0 16.6666666667%; }
.col-md-3 {
flex: 0 0 25%; }
.col-md-4 {
flex: 0 0 33.3333333333%; }
.col-md-5 {
flex: 0 0 41.6666666667%; }
.col-md-6 {
flex: 0 0 50%; }
.col-md-7 {
flex: 0 0 58.3333333333%; }
.col-md-8 {
flex: 0 0 66.6666666667%; }
.col-md-9 {
flex: 0 0 75%; }
.col-md-10 {
flex: 0 0 83.3333333333%; }
.col-md-11 {
flex: 0 0 91.6666666667%; }
.col-md-12 {
flex: 0 0 100%; }`

## 萨斯

我个人用的是 SASS 预处理器，这里是创建上面 CSS 的 SASS。

`$columns: 12;
@for $i from 1 through $columns {
.col-md-#{$i} {
flex: 0 0 100% / $columns * $i;
}
}`

四行 SCSS 代码对二十四行 CSS 代码。如果没有哇！！你，你一定是史蒂夫·乔布斯 lol。这是第一件让我爱上 CSS 预处理器的事情。在阅读上面的 SCSS 代码时，您已经从中受益，因为与之前的 CSS 代码相比，您节省了几秒钟的时间。

## 萨斯特性

sass 提供的一些功能和待遇如下:
。虽然我们现在可以在 ccs3 中使用变量，但这曾经是不可能的，你可以想象这会导致很多不愉快。

`$text-color: red;`

## [T1。混合蛋白](#mixins)

混音在 SCSS 是一个非常强大的功能。它允许我们重用我们的代码，尊重程序员的荣誉准则:不要重复你自己。我们也可以传递参数，所以它基本上是一个函数。

`@mixin box-sizing($parameter){
-webkit-box-sizing:$parameter;
-moz-box-sizing:$parameter;
box-sizing:$parameter;
}
.no-padding{
@include box-sizing(padding-box);}
.no-border{@include box-sizing(border-box) }`

## [T1。控制指令](#control-directives)

这些基本特征提供了一个可能性的世界。

## [T1。@如果](#if)

`$bg-color: black;
$text-white: white;
$text-grey:grey;
@if $bg-color==black {
color:$text-white;}
@else{color:$text-grey;}`

## [T1。@for](#for)

`$columns: 12;
@for $i from 1 through $columns {
.col-md-#{$i} {
flex: 0 0 100% / $columns * $i;}
}`

## [T1。@每个](#each)

`@each $color in blue, red, green{
.text-#{$color} {
color:#{color};
}
}`

## [T1。扩展](#extend)

Extend 有助于减少 CSS 的臃肿，而且它只在使用它们的情况下才可用。

`%use-me-when-you-need-me{
display-block;}
.hidden{
@extends use-me-when-you-need-me;}`

## [T1。注意](#note)

所有的 CSS 语法在 SCSS 都有效
还有更多功能唾手可得，所以我会在本文末尾留下一些参考。

[![Joy](img/0353e64dd7bcf3f2a8e04a2f95117914.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--thByDwn_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AFn8e4JC6VD1HqzicHUY5CQ.gif) 
我不知道你怎么想，但这让我高兴得跳了起来。感谢 SCSS 的创造者 Natalie Weizenbaum、Chris Eppstein 和贡献者，不用再手工编写 1000 多行 CSS 了。

## 获取开始

这是一个 ruby 实现，尽管 Ruby 支持现在已经过时了。

#### 1 一旦你的机器上安装了 ruby，输入

`gem install haml`

或者

`gem install sass`

#### 2 在你的工作目录下添加你的 SASS 或者 SCSS 文件 ex: main.scss 然后运行这个命令。

`sass --watch scss/main.scss:css/main.css`

这将在 css 文件夹中创建一个 main.css 文件。- watch 标志在 SCSS 文件和 CSS 文件之间保持活动链接，对 SCSS 文件所做的更改将立即反映在 CSS 文件中。

## 好的做法

一个好的做法是在使用 SCSS 时不要直接编辑 CSS 文件，这样做会导致跟踪问题，因为 SCSS 文件不会包含您在 CSS 文件中编写的代码。

## 结论

在当前这个时代，个人、初创公司甚至发展良好的公司都需要快速完成工作，像 SCSS 这样的工具真的大放异彩。

## 通过这些链接加深你的知识。

[入门](http://alistapart.com/article/getting-started-with-sass/lang.com/documentation/syntax)
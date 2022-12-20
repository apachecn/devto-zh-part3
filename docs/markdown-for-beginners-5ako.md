# 初学者减价

> 原文：<https://dev.to/sanamsoodan/markdown-for-beginners-5ako>

## 什么是 markdown？

*   轻量级标记语言
*   可以转换成 HTML/XHTML 等格式
*   它的主要目的是可读性和易用性

## 是用来做什么的？

*   自述文件(GitHub 等)
*   论坛和博客
*   在许多静态站点生成器中使用

您可以格式化的一些基本内容

标题代码块

列出图像

强调块引号

链接水平标尺

## 标题

```
# H1 Heading
## H2 Heading
### H3 Heading
#### H4 Heading
##### H5 Heading
###### H6 Heading 
```

# H1 航向

## H2 航向

### H3 航向

#### H4 航向

##### H5 航向

###### H6 航向

* * *

## 斜体

```
*This text is in italics*

_This text_ is in italics 
```

*该文本为斜体*

*这段文字*是在

* * *

## 强大

```
 **This text is in strong**

__This text__ is in strong 
```

**该文本为斜体**

文本以斜体显示

* * *

## 删除线

```
~~This text~~ is strikethough 
```

此文本有删除线

* * *

## 横尺

```
--- 
```

* * *

## 显示特殊字符

```
\*This text is in italics\* 
```

*本文以斜体显示*

* * *

## 块引号

```
> This is a Quote 
```

> 这是一段引言

```
[https://www.sanamdeep.dev](https://www.sanamdeep.dev) 
```

[https://www . sanam deep . dev](https://www.sanamdeep.dev)

* * *

## 悬停时显示标题

```
[https://www.sanamdeep.dev](https://www.sanamdeep.dev "sanamdeep.dev") 
```

[[https://www . sanam deep . dev](https://www . sanam deep . dev](https://www.sanamdeep.dev%5D(https://www.sanamdeep.dev)" sanam deep。

* * *

## 无序列表

```
* Item 1
  * Item 1.1
    * Item 1.1.1
* Item 2
  * Item 2.1
    * Item 2.1.1 
```

*   项目 1
    *   项目 1.1
    *   项目 1.1.1
*   项目 2
    *   项目 2.1
    *   项目 2.1.1

* * *

## 有序列表

```
1\. Item
    1\. Item1.1
        1\. Item 1.2
2\. Item 2
    1\. Item 2.1
        1\. Item 2.1.1 
```

1.  项目
    1.  项目 1.1
        1.  项目 1.2
2.  项目 2
    1.  项目 2.1
        1.  项目 2.1.1

* * *

## 块引号

```
`<p>This is a paragraph</p>` 
```

`<p>This is a paragraph</p>`

* * *

## 图像

```
![Markdown Logo](https://markdown-here.com/img/icon256.png) 
```

[![Markdown Logo](img/ab01074e14dd84650f8f0756fbc08000.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pFn86d2h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://markdown-here.com/img/icon256.png)

* * *

## GitHub 降价

### 代码块

> ` ` bash
> npm 安装
> ` `

```
npm install 
```

> ` ` javascript
> 函数 add(num1，num2) {
> 返回 num 1+num 2；
> }
> ` ' '

```
 function add(num1, num2) {
    return num1 + num2;
  } 
```

> ` ` python '
> def add(num 1，num2):
> 返回 num 1+num 2
> `

```
 def add(num1, num2):
    return num1 + num2 
```

* * *

### 表格

```
| Name    |  Email   |
| ----    |  ------  |
| John Doe | john@gmail.com |
| Jane Doe | john@gmail.com | 
```

| 名字 | 电子邮件 |
| --- | --- |
| 无名氏 | [john@gmail.com](mailto:john@gmail.com) |
| 简·多伊 | [john@gmail.com](mailto:john@gmail.com) |

* * *

### 任务列表

```
* [x] Task 1 completed
* [x] Task 2 completed
* [ ] Task 3 not completed 
```

*   [x]任务 1 已完成
*   [x]任务 2 已完成
*   [ ]任务 3 未完成
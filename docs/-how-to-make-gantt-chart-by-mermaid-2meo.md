# 如何用美人鱼制作甘特图

> 原文：<https://dev.to/mizumm/-how-to-make-gantt-chart-by-mermaid-2meo>

# 简介

Mermaid 是一种简单的类似 markdown 的脚本语言，用于从文本生成图表。本文件展示了使用 mermaid cli 工具创建以下甘特图的方法。

[![Sample Gant Chart](img/a81d4a1b397d8ecaa3dfc4916e9a9880.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GRX6MjNS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8jcx0uzgzsaod2ovg2jd.png)

美人鱼 Github 回购:
[https://github.com/knsv/mermaid](https://github.com/knsv/mermaid)

# 环境

Ubuntu 18.04 lt(x86 _ 64)

# 设置

*   安装 npm 命令

    有一些安装 npm 的方法。谷歌一下:-)

*   通过 npm 安装 mermaid.cli】

```
 $ npm install mermaid.cli 
```

# 如何创建甘特图

*   创建一个美人鱼文件，例如 test.mmd:

```
 gantt
         title A Gantt Diagram
         dateFormat YYYY-MM-DD
         section Section
         A task :a1, 2019-01-01, 30d
         Another task :after a1 , 20d
         section Another
         Task in sec :2019-01-12 , 12d
         another task : 24d 
```

语法在这里:
[https://mermaidjs.github.io/gantt.html](https://mermaidjs.github.io/gantt.html)

*   运行 mmdc 命令。

```
$ ~/node_modules/.bin/mmdc -i test.mmd -o test.png 
```

你会看到 test.png 的 PNG 文件。

# 提示

## 去掉今天的红线

*   创建以下 json 文件。

```
 {  "themeCSS":  ".today {fill: none; stroke: red; stroke-width: 0px;}"  } 
```

*   如下传递 json 文件。

```
 $ ~/node_modules/.bin/mmdc -c config.json -i test.mmd -o test.png 
```
# 蓝图——终端的样板工具

> 原文：<https://dev.to/mikister/blueprint---a-boilerplate-tool-for-the-terminal-57pb>

## 这是什么

简而言之，它会代替你复制文件和目录(*文件夹*)。

假设您有一个称为`flask-boilerplate`的**蓝图**(文件和目录集合的 WIP 名称)。您转到新 flask 项目的一个空目录，并在终端中键入这个内容:

```
$ blueprint flask-boilerplate 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！

如果这就是你的蓝图:

```
.
├── templates
|   └── example_template.html
├── static
|   ├── style.css
|   └── main.js
└── start_server.py 
```

Enter fullscreen mode Exit fullscreen mode

那么这就是你将得到的，添加到你的项目中。

## 工作原理

#### 创造新的*蓝图*

```
$ blueprint new blueprint-name-here 
```

Enter fullscreen mode Exit fullscreen mode

#### 删除已有的*蓝图*

```
$ blueprint delete blueprint-name-here 
```

Enter fullscreen mode Exit fullscreen mode

#### 列出所有现存的*蓝图*

```
$ blueprint list

#output
# Avaiable blueprints:
#  i │ name     │ size
# ───┼──────────┼───────
#  0 │ html-min │ 24K
#  1 │ test1    │  4K
#  2 │ test2    │  4K 
```

Enter fullscreen mode Exit fullscreen mode

#### 用正则表达式搜索*蓝图*名称

```
$ blueprint list regex-here

#output
# Avaiable blueprints:
#  i │ name            │ size
# ───┼─────────────────┼───────
#  0 │ regex-here1     │  15M
#  0 │ dont-regex-here │ 300G 
```

Enter fullscreen mode Exit fullscreen mode

#### 显示蓝图的内容

```
$ blueprint showfiles blueprint-name-here

#output
# Showing file for blueprint: blueprint-name-here
#    F | file1.txt
#    F | file2.txt
#    D | some-directory 
```

Enter fullscreen mode Exit fullscreen mode

## 我打算用这个做什么

这是一个有趣的小项目，但我真的没有任何计划。它可以在我正在使用的 Linux 上运行。我可能会为 Mac OS 或 Windows 制作一个版本。我已经把它上传到任何地方供下载，但以后会这样做。

## 任何反馈

如果你有什么建议，我想听听。
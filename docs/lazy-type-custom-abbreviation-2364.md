# 惰性类型(自定义缩写)

> 原文：<https://dev.to/hhypnos/lazy-type-custom-abbreviation-2364>

这个( [link](https://gitlab.com/hhypnos/custom-abbreviation) )迷你脚本给你机会用简单的缩写写日常文本。

您应该在 config.py
中添加
`keyboard.add_abbreviation('Abbreviation','value')`，并以`sudo python3 abbr.py`开始脚本

## config.py 示例:

`settings=[
keyboard.add_abbreviation('@@', '[example@email.com](mailto:example@email.com)'),
keyboard.add_abbreviation('HH', 'SecondAbbreviation'),
]` 

## 用法:

在任何地方写下你的缩写，然后按空格键
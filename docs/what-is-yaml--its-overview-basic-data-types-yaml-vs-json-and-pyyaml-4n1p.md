# 什么是:YAML——概述、基本数据类型、YAML vs JSON 和 PyYAML

> 原文：<https://dev.to/setevoy/what-is-yaml--its-overview-basic-data-types-yaml-vs-json-and-pyyaml-4n1p>

[![](img/306c3bca0210fe58776a60aa6974ec2c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jqxd9BXo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rtfm.co.ua/wp-content/uploads/2016/01/What-is-it-e1452250201867.jpg)YAML——是最流行的格式之一…

嗯，实际上，他们不知道什么格式…

最初它是另一种标记语言，后来它变成了 *YAML 不是标记语言*:

> 最初，YAML 被认为是另一种标记语言、<sup id="cite_ref-YAML_spec_2001_08_01_12-0">、[、【12】、](https://en.wikipedia.org/wiki/YAML#cite_note-YAML_spec_2001_08_01-12)、</sup>的意思，指的是其作为[标记语言](https://en.wikipedia.org/wiki/Markup_language)的目的，而[是另一种](https://en.wikipedia.org/wiki/Yet_another)结构，但后来它被重新定义为*YAML Ain not Markup Language*，这是一个[递归缩写词](https://en.wikipedia.org/wiki/Recursive_acronym)，以区分其面向数据的目的，而不是文档标记。

在俄语维基百科中,“友好”这个词甚至被加了引号——我完全同意这一点。

事实上——YAML 只是另一种数据序列化类型，JSON 格式的继承者，但是具有一些额外的能力。

在乌克兰 DevOps 社区最近的调查中，YAML 获得了大约 90%的选票。

至于我 JSON 还是最方便的，但是 YAML 用的很多地方都得用它。

在这篇文章中，我们将仔细观察 YAML 的数据类型，并与 JSON 进行快速比较。

### YAML 主要原则

*   总是使用 UTF-8 来避免可能的问题
*   不要使用制表符缩进

### YAML 语法验证

要检查 Linux 中的 YAML 语法，可以使用 [`yamllint`](https://github.com/adrienverge/yamllint) 。

安装它:

```
$ sudo pacman -S yamllint 
```

并检查文件:

```
$ yamllint monitoring.yml
monitoring.yml
1:1       warning  missing document start "---"  (document-start)
20:34     error    trailing spaces  (trailing-spaces)
22:32     error    trailing spaces  (trailing-spaces)
23:37     error    trailing spaces  (trailing-spaces)
33:7      error    wrong indentation: expected 8 but found 6  (indentation)
35:9      error    wrong indentation: expected 10 but found 8  (indentation)
36:11     error    wrong indentation: expected 12 but found 10  (indentation) 
```

虽然 Ansible 使用这个文件没有任何问题，但它的格式仍然存在一些问题。

#### JSON 验证

例如——JSON 文档使用 Python 的`json`模块从 Linux 控制台进行验证:

```
$ python -m json.tool < json-example.json
{
  "key1": "value1",
} 
```

### `vim`插件

还有`vim`的 [`vim-yaml`](https://vimawesome.com/plugin/vim-yaml-all-too-well) 插件。

添加到您的`.vimrc` :

```
...
" https://vimawesome.com/plugin/vim-yaml-all-too-well
Plug 'avakhov/vim-yaml'
" add yaml stuffs
au! BufNewFile,BufReadPost *.{yaml,yml} set filetype=yaml foldmethod=indent
autocmd FileType yaml setlocal ts=2 sts=2 sw=2 expandtab
... 
```

重新加载配置并安装:

```
:source %
:PlugInstall 
```

### PyYAML

为了使用 Python 中的 YAML，有一个 [PyYAML](https://yaml.readthedocs.io/en/latest/example.html) 库。

下面是一些例子。

### YAML 格式化

#### 评论 YAML

YAM 为数不多的优势之一是能够在文件中添加注释。

注释格式是常见的–使用#。

可以在任何地方添加注释。

例子:

```
---
# I'm comment
- name: somestring
  value1: "# I'm not a comment!"
  value: anotherstring  # another comment 
```

#### 缩进

YAML 上最令人头疼的是凹痕。

在这种情况下，在整个文件中，空格的数量(空格–从不使用制表符！)肯定是一样的。

也就是说，如果在一个地方使用了两个空格，那么整个文件必须使用两个空格。

更重要的是——协议是使用两个空格，尽管可以是任何空格——在任何地方都必须相同。

例如:

```
---
parent_key:
    key1: "value1"
    key2: "value2"
    key3: "%value3" 
```

将是有效形式，但下一个例子:

```
---
parent_key1:
    key1: "value1"
    key2: "value2"
    key3: "%value3"
parent_key2:
  key1: "value1"
  key2: "value2"
  key3: "%value3" 
```

不会。

而在 Python 中，由于对空格的依赖，这种格式经常被指责，尽管这违反了标准:

```
#!/usr/bin/env python
def a():
    print("A")

def b():
  print("B")

a()
b() 
```

结果:

```
$ python spaces.py
A
B 
```

#### 单线 YAML

除了标准视图和空格缩进，你还可以使用类似 JSON 的格式，比如:

```
---
parent_key: {key1: "value1", key2: "value2"} 
```

#### 文字块标量

YAML 支持添加多行文字块标量的能力，有三种类型:常用的，使用“`|`”和“`>`”。

常见的格式如下:

```
---
string: This
    is
    some text
    without newlines 
```

Python 控制台中的结果:

```
>>> yaml.load(open('yaml-example.yml'))
{'string': 'This is some text without newlines'} 
```

使用| ( *[)文字样式)](http://www.yaml.org/spec/1.2/spec.html#id2795688)*–将保存所有换行符和结束空格:

```
---
string: |
    This
    is
    some text
    with newlines 
```

结果是:

```
>>> yaml.load(open('yaml-example.yml'))
{'string': 'This\nis\nsome text\nwith newlines\n'} 
```

并使用>( *[)折叠样式](http://www.yaml.org/spec/1.2/spec.html#id2796251)* ):

```
---
string: >
    This
    is
    some text
    without newlines 
```

将在一行中返回整个文本+结束换行符:

```
>>> yaml.load(open('yaml-example.yml'))
{'string': 'This is some text without newlines\n'} 
```

但是，您仍然必须遵守相同的空间格式。

还有，查一下 StackOverflow 上的伟大答案[这里> > >](https://stackoverflow.com/a/21699210/2720802) :

> 在 YAML，有 569 种(或者 63 种，取决于你怎么数)不同的方法来写多行字符串。

### YAML 基本数据格式

YAML 使用三种主要的数据格式:

*   标量:在 *key:value* 视图中最简单的
*   列表/序列:按索引排序的数据
*   字典/映射:类似于标量，但可以包含嵌套数据，包括其他数据类型

* * *

#### 标量

基本数据类型–标量，只是一个键:作为编程变量的值:

```
---
key1: "value1"
key2: "value2" 
```

对建议值使用引号，以避免特殊字符可能产生的问题:

```
cat example.yml
---
key1: "value1"
key2: "value2"
key3: %value3 
```

验证它:

```
$ yamllint example.yml
example.yml
4:7       error    syntax error: found character '%' that cannot start any token 
```

不过，对于布尔值 true/false 和整数类型，您可以跳过引号。

##### 标量——YAML vs JSON

例如–YAML 的标量:

```
---
key: "value" 
```

还有 JSON:

```
{
    "key": "value"
} 
```

##### 巨蟒

Python 中的 YAML 标量示例:

```
>>> import yaml
>>> yaml.load("""
... key: "value"
... """)
{'key': 'value'} 
```

或者从文件:

```
>>> import yaml
>>> yaml.load(open('yaml-example.yml'))
{'key': 'value'} 
```

* * *

#### YAML 列表

列表、序列、集合–表示有序数据的集合，其中每个元素都可以通过其索引进行访问。

例如:

```
# SIMPLE LIST
- element1
- element2 
```

##### YAML 嵌套列表

类似于上面的例子——列表可以包含嵌套列表:

```
# SIMPLE LIST
- element1
- element2
# nested list
-
  - element1 
```

或者可以是命名列表:

```
---
itemname:
  - valuename 
```

这样，列表也可以包含标量或字典:

```
---
itemname:
  - valuename
  - scalar: "value"
  - dict: {item1: "value1", item2: "value2"} 
```

##### 榜单——YAML vs JSON

YAML 列表:

```
---
- item1
- item2
- item3 
```

JSON 中的列表:

```
[
    "item1",
    "item2",
    "item3"
] 
```

YAML 嵌套列表:

```
---
- item1
- item2
- item3
-
  - nested1 
```

JSON 中的嵌套列表:

```
[
    "item1",
    "item2",
    "item3",
    [
        "nested1"
    ]
] 
```

##### Python 和 YAML-列表

下面是所有类似标量的例子:

```
>>> yaml.load(open('yaml-example.yml'))
['item1', 'item2', 'item3', ['nested1']]
>>> for i in yaml.load(open('yaml-example.yml')):
...   print(i)
...
item1
item2
item3
['nested1'] 
```

* * *

#### 字典

字典，也称为映射，类似于标量类型，包含一个键:值数据，但不同于标量的基本类型——字典可以包含嵌套元素，例如列表:

```
---
key1: "value1"
key2:
  - value2
  - value3 
```

或者另一个嵌套字典:

```
---
key1: "value1"
key2:
  - value2
  - value3

key3:
  key4: "value4"
  key5: "value5"
  key6:
    key7: "value7" 
```

##### 字典——JSON vs YAML

YAML 词典:

```
---
key1: "value1"
key2:
  - value2
  - value3 
```

JSON 中的字典:

```
{
    "key1": "value1",
    "key2": [
        "value2",
        "value3"
    ]
} 
```

##### 巨蟒

```
>>> yaml.load(open('yaml-example.yml'))
{'key1': 'value1', 'key2': ['value2', 'value3']}
>>> type(yaml.load(open('yaml-example.yml')))
<class 'dict'> 
```

并且支持 Python 的所有常用字典操作:

```
>>> dict = yaml.load(open('yaml-example.yml'))
>>> type(dict)
<class 'dict'>
>>> dict.update({'key3':'value3'})
>>> print(dict)
{'key1': 'value1', 'key2': ['value2', 'value3'], 'key3': 'value3'} 
```

* * *

总的来说，就是这样。

更多详细信息，请查看这些页面:

*   [完全白痴的 yaml 入门](https://github.com/Animosity/CraftIRC/wiki/Complete-idiot%27s-introduction-to-yaml)
*   [YAML vs JSON](http://sangsoonam.github.io/2017/03/13/yaml-vs-json.html)
*   [YAML 语法](https://docs.ansible.com/ansible/latest/reference_appendices/YAMLSyntax.html)
*   [YAML 语法](https://learn.getgrav.org/15/advanced/yaml)
*   [在 Y 分钟内学会 X](https://learnxinyminutes.com/docs/yaml/)

### 类似的帖子

*   T005/13/2015 年 T1T 2 Django:建立 T3“家庭会计”项目
*   <small>02/05/2015</small>[django book:urlconfи《слабыесвязи》](https://rtfm.co.ua/django-book-urlconf-i-slabye-svyazi/)<small>(0)</small>
*   <small>05/31/2015</small>[python:【pyint’pycharm】](https://rtfm.co.ua/python-integraciya-pylint-i-pycharm/)<small></small>
<small></small>

<small></small>
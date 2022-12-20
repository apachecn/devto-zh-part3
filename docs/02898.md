# 字典的默认值，Python 技巧

> 原文：<https://dev.to/burdier/default-value-of-a-dictionary-python-trick-4dmf>

当你需要从 dicctionary 中获取一个值是非常容易的，任何人都知道怎么做，但我相信这个解决方案是一个新的 python 程序员所不知道的。

从字典中获取值的最常见方法:

```
computer_component = {"output":"monitor","input":"teclado","storage":"SSD"}
get_component = computer_component['output']
print(get_component) 
```

问题是当值不在字典中时:

```
computer_component = {"output":"monitor","input":"teclado","storage":"SSD"}
get_component = computer_component['internet']
print(get_component)

#output:
#Traceback (most recent call last):
#  File "main.py", line 2, in <module>
#    get_component = computer_component['internet']
#KeyError: 'internet' 
```

好的，您可以验证值是否存在:

```
computer_component = {"output":"monitor","input":"teclado","storage":"SSD"}

if "internet" in computer_component.keys():
  print(computer_component["internet"])
else:
  print("internet 404")

#output:
#internet 404 
```

但是从字典中获取价值的最好方法是:

```
computer_component = {"output":"monitor","input":"teclado","storage":"SSD"}

print(computer_component.get("output","404"))

#output
#monitor 
#--in case when value not exists: print(computer_component.get("internet","404"))
#output
#404 
```

其中，默认情况下，第一个参数是值，第二个参数是值。

请原谅，我的英语有点差，我想提高它。
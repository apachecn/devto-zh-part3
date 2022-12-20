# python 对象的嵌套 json

> 原文：<https://dev.to/mandrewcito/nested-json-to-python-object--5ajp>

今天我在创建一个配置文件，过去，我把配置作为一个字典来访问，但是这次，我想改变它。下面的代码递归地创建带有对象键的动态属性。

但是首先，我将展示 json 对象:

```
{  "key":  "value",  "list":  [  "a",  "b",  "c",  1,  {  "key":  1  }  ],  "object":  {  "key":  {  "key":  1  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

关于转换，我们有三种情况:

*   列表
*   字典(新对象)
*   bool、int、float 和 str

```
import json

class AppConfiguration(object):
    def __init__(self, data=None):
        if data is None:
            with open("cfg.json") as fh:
                data = json.loads(fh.read())
        else:
            data = dict(data)

        for key, val in data.items():
            setattr(self, key, self.compute_attr_value(val))

    def compute_attr_value(self, value):
        if type(value) is list:
            return [self.compute_attr_value(x) for x in value]
        elif type(value) is dict:
            return AppConfiguration(value)
        else:
            return value 
```

Enter fullscreen mode Exit fullscreen mode

关键是，值对是属性-对象。

```
instance = AppConfiguration()
>>>instance.key
'value'

>>>instance.list[4].key
1

>>> instance.object.key.key
1 
```

Enter fullscreen mode Exit fullscreen mode
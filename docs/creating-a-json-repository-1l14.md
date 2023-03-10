# 创建 json 存储库

> 原文：<https://dev.to/mandrewcito/creating-a-json-repository-1l14>

# [T1】简介](#intro)

几天前，我开始重构并重新实现我在工作中创建的一个小型 json 存储库，主要原因是提供一个没有数据库的快速实现并存储一些对象。

我认为这个实现还可以改进，但是它是有效的，并且对于第一个方法来说，它是有效的，在接下来的几个月里我将继续努力。

实现非常简单，有一个使用上下文存储数据的存储库，在文章的结尾有一个使用示例，在后面的文章中，我将详细介绍另一种使用方法。

# 上下文

```
import os
import json 
import uuid
import threading
from ..errors.entity_not_found import EntityNotFound

class JsonContext(object):
  def __init__(self, enity_name, filepath="./", key_field="id", key_generate_func=lambda: str(uuid.uuid4())):
    self.file_path = os.path.join(filepath, "{0}s.json".format(enity_name))
    self.entity_name = enity_name
    self.session_values = []
    if not os.path.exists(self.file_path):
      self.commit()      
    self.key_field = key_field
    self.key_generate_func = key_generate_func
    self.lock = threading.Lock()

  def open(self):
    self.lock.acquire()
    with open(self.file_path) as f:
      data = json.load(f)
      self.session_values = data["values"]

  def close(self):
    self.lock.release()

  def __enter__(self):
    self.open()
    return self

  def __exit__(self, exc_type, exc_val, exc_tb):
    self.close()

  def exists(self, identifier):
    matches = list(filter(
        lambda x: x[self.key_field] == identifier,
      self.session_values))
    return len(matches) > 0

  def add(self, entity):
    if (self.key_field in entity and self.exists(entity[self.key_field])):
      self.delete(entity)
    else:
      entity[self.key_field] = self.key_generate_func()

    self.session_values.append(entity)
    return entity

  def delete(self, entity):
    self.session_values = list(filter(
      lambda x: x[self.key_field] != entity[self.key_field],
      self.session_values))

  def commit(self):
      with open(self.file_path, 'w+') as f:
        f.write(json.dumps({"name": self.entity_name, "values" : self.session_values}))

  def find(self, query_function):
    return list(filter(query_function, self.session_values))

  def get(self, identifier):
    if not self.exists(identifier):
      raise EntityNotFound("identifier not found")
    return self.find(lambda x: x[self.key_field] == identifier)[0]

  def get_all(self):
    return self.session_values 
```

Enter fullscreen mode Exit fullscreen mode

# 储存库

```
from ..context.json_context import JsonContext

class BaseJsonRepository(object):
  def __init__(self, entity_name, filepath="./"):
    self.context = JsonContext(entity_name, filepath=filepath)

  def __enter__(self):
    self.context.open()
    return self

  def __exit__(self, exc_type, exc_val, exc_tb):
    self.context.close()

  def insert(self, entity):
    return self.context.add(entity)

  def update(self, entity):
    return self.context.add(entity)

  def delete(self, entity):
    self.context.delete(entity)

  def get_all(self):
    return self.context.get_all()

  def get(self, identifier):
    return self.context.get(identifier)

  def find(self, function):
    return self.context.find(function) 
```

Enter fullscreen mode Exit fullscreen mode

# 举例

例子...看看[测试](https://github.com/mandrewcito/json_repository/blob/master/test/sample/foobar_test.py) :)

```
from json_repository.repositories.base_json_repository import BaseJsonRepository
from json_repository.errors.entity_not_found import EntityNotFound

class FoobarRepository(BaseJsonRepository):
  def __init__(self):
    super(FoobarRepository, self).__init__("foo")

with FoobarRepository() as repo:
    value = repo.insert({
        "foo": "a foo value",
        "bar":" a bar value"
        })
    repo.context.commit() 
```

Enter fullscreen mode Exit fullscreen mode

# 链接

[Github](https://github.com/mandrewcito/json_repository)
[Pypi](https://pypi.org/project/json-repository/)

欢迎任何关于这个‘玩具’的反馈，在下面留下评论:)。
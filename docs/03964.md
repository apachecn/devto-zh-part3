# 你如何看待 Python 的 ObjectBox 数据库

> 原文：<https://dev.to/ivan/what-do-you-think-of-the-objectbox-database-for-python-32oh>

继 Android、iOS、Java、Swift、C & Go 之后，Python 也获得了第一批支持。

[https://objectbox.io/objectbox-python/](https://objectbox.io/objectbox-python/)

```
id = box.put(Person(first_name="Joe", last_name="Green"))  # Create person = box.get(id)  # Read person.last_name = "Black"
box.put(person)       # Update box.remove(person)    # Delete 
```
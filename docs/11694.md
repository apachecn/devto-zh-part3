# python:if _ _ name _ _ = = " _ _ main _ _ "是什么？

> 原文：<https://dev.to/setevoy/python-what-is-the-if-name--main--2ji0>

[![Python](img/5954812d3a7e0500b481ec51e096cad7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--I_D9g-RS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://rtfm.co.ua/wp-content/uploads/2013/02/python_logo.jpg) 在许多 Python 模块中你可以找到类似于
的结构

```
if __name__ == "__main__": func() 
```

它的主要目的是在将代码导入到另一个代码中之后，以及在将模块本身作为专用脚本运行时，将代码作为一个模块调用时执行。

***注**:这篇文章最初写于 2014 年 9 月 12 日，在我的[俄语博客版本](https://rtfm.co.ua/python-zachem-nuzhen-if-__name__-__main__/)中，所以这里使用的是 Python 2。*

让我们来看几个例子。

**剧本 1** :

```
#!/usr/bin/env python

print('Script 1\. My name is: %s' % __name__)
print('This is simple code from script 1')

def func():
    print('This is code from function from script 1')

if __name__ == "__main__":
    func() 
```

还有**剧本二** :

```
#!/usr/bin/env python

print('Script2\. My name is: %s' % __name__)
print('Importing ifname1')
import ifname1 
```

**脚本 2** 的执行结果直接:

```
$ ./ifname2.py
Script2\. My name is: __main__
Importing ifname1
Script 1\. My name is: ifname1
This is simple code from script 1 
```

**脚本 1** 的执行结果直接:

```
$ ./ifname1.py
Script 1\. My name is: __main__
This is simple code from script 1
This is code from function from script 1 
```

再比如。

**剧本 1** :

```
#!/usr/bin/env python

if __name__ == "__main__":
    print('I am running as an independent program with name = %s' % __name__)
else:
    print('I am running as an imported module with name = %s' % __name__) 
```

还有**剧本 2**——此处无改动:

```
#!/usr/bin/env python

print('Script2\. My name is: %s' % __name__)
print('Importing ifname1')
import ifname1 
```

执行**脚本 2** 将给出下一个结果:

```
$ ./ifname2.py
Script2\. My name is: __main__
Importing ifname1
I am running as an imported module with name = ifname1 
```

运行**脚本 1** 时–下一个:

```
$ ./ifname1.py
I am running as an independent program with name = __main__ 
```

### 类似的帖子

*   <small>03/16/2018</small>[Python:pip–attribute error:'模块'对象没有属性' SSL_ST_INIT'](https://rtfm.co.ua/python-pip-attributeerror-module-object-has-no-attribute-ssl_st_init/) <small>(0)</small>
*   <small>12/31/2017</small>[python:boto 3′OAS 安全组](https://rtfm.co.ua/python-boto3-i-skript-obnovleniya-aws-security-group/)<small>【0】</small>
*   T0 02/26/2015 t1 T2:PYTHON:PYLINT——查找错误和分析 T3 代码质量
*   t001/02/2018 t1py thon:boto 3—T3t 授权的示例
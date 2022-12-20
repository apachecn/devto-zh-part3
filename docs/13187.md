# Python 中的猴子补丁

> 原文：<https://dev.to/hasansajedi/monkey-patching-in-python-1apk>

在 Python 中，术语“猴子补丁”指的是类或模块的动态(或运行时)修改。在 Python 中，我们实际上可以在运行时改变代码的行为。

# monk.py

A 类:
def func(self):
打印" func()正在被调用"

我们在下面的代码中使用上面的模块(monk ),并通过分配不同的值来改变 func()在运行时的行为。

导入 monk
def monkey_f(self):
打印“monkey_f()正在被调用”

# 用“monkey_f”替换“func”的地址

和尚。A.func = monkey_f
obj = monk。答()

# 调用地址被替换的函数“func”

# 用函数“monkey_f()”

obj.func()

输出:monkey_f()正在被调用
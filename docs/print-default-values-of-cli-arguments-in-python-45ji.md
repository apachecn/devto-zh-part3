# 用 Python 打印 CLI 参数的默认值

> 原文：<https://dev.to/wincentbalin/print-default-values-of-cli-arguments-in-python-45ji>

如果您想打印命令行参数的默认值，并且如果您在 Python 程序中使用了`argparse.ArgumentParser`类，请考虑将`formatter_class`选项设置为`argparse.ArgumentDefaultsHelpFormatter`。

示例代码:

```
parser = argparse.ArgumentParser(description=sys.modules[__name__]. __doc__ ,
                                 formatter_class=argparse.ArgumentDefaultsHelpFormatter)
parser.add_argument('-s', '--std', type=float, default=440.0, help='Standard value')
args = parser.parse_args() 
```

Enter fullscreen mode Exit fullscreen mode
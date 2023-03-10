# 不要惊慌！借助静态分析，调试速度提高 10 倍

> 原文：<https://dev.to/snwfdhmp/stop-panic-ing-start-debugging-10x-faster-with-static-analysis-3701>

我想介绍一下 [Errlog](https://github.com/snwfdhmp/errlog) ，这是一个可破解的错误处理包，它使用堆栈跟踪和静态分析来确定哪个 func 调用对您的错误负责。

用
将您想要调试的错误包装起来

```
err := failingFunc()
if errlog.Debug(err) {
    return err
} 
```

输出:

[![Output](img/e04aeffe0ad7072a0a3aa3cab29768ba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yL0AuwvU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/59y7tsvwnb0vtmrfb97f.png)

可插入任何适当的记录器。

[尝试 errlog](https://github.com/snwfdhmp/errlog) 。
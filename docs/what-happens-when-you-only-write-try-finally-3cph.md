# 只写 try/finally 会怎么样

> 原文：<https://dev.to/mcrowder65/what-happens-when-you-only-write-try-finally-3cph>

我今天想，嗯，当你做`try/finally`时会发生什么，没有`catch`子句，那么，这里的输出是什么？

```
const errorThrower = () => {
  throw new Error("i am an error");
};

const errorInvoker = () => {
  try {
    errorThrower();
    console.log("errorInvoker");
  } finally {
    console.log("finally");
  }
};

const catcher = () => {
  try {
    errorInvoker();
    console.log("catcher");
  } catch (error) {
    console.log("catcher caught the error");
  }
};

catcher(); 
```

Enter fullscreen mode Exit fullscreen mode

我以为输出会是:

```
finally
catcher 
```

Enter fullscreen mode Exit fullscreen mode

但实际输出是:

```
finally
catcher caught the error 
```

Enter fullscreen mode Exit fullscreen mode

在`errorInvoker`中，try 块执行，`errorThrower()`抛出一个错误，然后在抛出错误后立即最终执行，然后 catcher 捕捉到`errorThrower`抛出的错误，并记录`catcher caught the error`。
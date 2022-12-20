# std::cout 未在控制台/终端中打印

> 原文：<https://dev.to/lucpattyn/stdcout-is-not-printing-in-consoleterminal-5emn>

假设在一个 C++程序中，您正在这样做，但是在控制台或终端窗口中什么也没有出现。

std::cout < < "运行 1 .. "；

这可能是因为 std::cout 正在写入等待刷新的输出缓冲区。
如果没有冲洗，将不会打印任何内容。

因此，您可能必须通过执行以下操作来手动刷新缓冲区:

```
        std::cout.flush();

```

但是，如果您在最后调用 std::endl，那么它将调用 flush，因此您不必手动调用它。

如果你写 std::cout < < "运行 1 .. "< < STD::endl；

您会看到输出现在显示在控制台或终端窗口中。

感谢 https://dev.to/cnskn 指出这一点。
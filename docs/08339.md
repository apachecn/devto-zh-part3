# 从 x86-64 为 ARM 编译

> 原文：<https://dev.to/aaron_rubesh/compiling-for-arm-from-x86-64-327p>

与低级 Linux 库交互可以使应用程序的逆向、调试和分析更加简单。

库，比如`ptrace`，可以让我们附加到正在运行的进程上，从 tracee(我们附加到的进程)中执行函数。

由于 android 基本上是一个 Linux 操作系统，我们可以调用这些相同的库来跟踪 Android 应用程序的进程。

例如，可以编译并运行以下代码来转储附加进程的寄存器值:

```
//main.c
#include <stdio.h>
#include <stdlib.h>
#include <sys/user.h>
#include <sys/types.h>
#include <sys/ptrace.h> 
int main(int argc, char **argv) {
    pid_t traced_process;
    long rt;
    struct user_regs_struct regs;

    if(argc != 2) {
            printf("usage: %s PID\n", argv[0]);
        return -1;
    }

    traced_process = atoi(argv[1]);
    rt = ptrace(PTRACE_SEIZE, traced_process, NULL, NULL);

    if(rt != 0) {
        printf("Failed to attach to process. Are you root?\n");
        return -1;
    }

    ptrace(PTRACE_GETREGS, traced_process, NULL, &regs);

    printf("RBP: %llx\nRBX: %llx\nRSI: %llx\nRIP: %llx\n", regs.rbp, regs.rbx, regs.rsi, regs.rip);

    ptrace(PTRACE_DETACH, traced_process, NULL, NULL);
    return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你用 gcc 编译它:`gcc main.c -o dump-regs`，然后在你的机器上运行的随机进程上执行它:`sudo ./dump-regs $PID`

我们将得到进程的一些寄存器值的转储:

```
$ sudo ./dump-regs 10245                                                                             
RBP: 7f7be53dda98
RBX: 0
RSI: 0
RIP: 9 
```

Enter fullscreen mode Exit fullscreen mode

这工作完美。问题是这个程序是编译运行在 x86_64 系统上的，不是 ARM。所以我们不能简单地把这个程序转移到我们的 Android 设备上运行。

```
$ file dump-regs
dump-regs: ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/l, for GNU/Linux 3.2.0, BuildID[sha1]=36122682e0cf5cac23820af5c691d3b2c2aa374b, not stripped 
```

Enter fullscreen mode Exit fullscreen mode

幸运的是，我们可以通过构建支持 ARM 交叉编译的 GCC 来解决这个问题。

在基于 Debian 的发行版上，你应该能够安装软件包`gcc-arm-linux-gnueabi`和`binutils-arm-linux-gnueabi`。

```
sudo apt install gcc-arm-linux-gnueabi and binutils-arm-linux-gnueabi 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了一个支持编译到 ARM 的 GCC 版本。然而，ARM 上包含的一些头文件与 x86_64 Linux 上的不同，所以我们需要在编译之前对代码做一些修改。这里要注意的主要区别是`user_regs`结构。

我不打算在这里解释两种不同 CPU 架构的寄存器之间的差异，但是如果你好奇，我会阅读 ARM CPU 架构。当我们更多地逆转一些 Android 进程时，这些知识将会派上用场。

您还可以查看由每个`gcc`和`arm-linux-gnueabi-gcc`编译的二进制文件包含的头文件，以了解一些差异，并了解为什么我们的代码必须更改。

```
//main.c
#include <stdio.h>
#include <stdlib.h>
#include <sys/user.h>
#include <sys/types.h>
#include <sys/ptrace.h> 
int main(int argc, char **argv) {
    pid_t traced_process;
    long rt;

#ifdef __arm__
    struct user_regs regs;
#endif
#ifdef __x86_64__
    struct user_regs_struct regs;
#endif 
    if(argc != 2) {
        printf("usage: %s PID\n", argv[0]);
        return -1;
    }

    traced_process = atoi(argv[1]);
    rt = ptrace(PTRACE_SEIZE, traced_process, NULL, NULL);

    if(rt != 0) {
        printf("Failed to attach to process. Are you root?\n");
        return -1;
    }

    ptrace(PTRACE_GETREGS, traced_process, NULL, &regs);

#ifdef __x86_64__
    printf("RBP: %llx\nRBX: %llx\nRSI: %llx\nRIP: %llx\n", regs.rbp, regs.rbx, regs.rsi, regs.rip);
#endif 
#ifdef __arm__
    for(int i = 0; i < 18; i++) {
        printf("REG[%d]: %lx\n", i, regs.uregs[i]);
    }
#endif 
    ptrace(PTRACE_DETACH, traced_process, NULL, NULL);
    return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们为 ARM 编译:`arm-linux-gnueabi-gcc main.c -o arm-dump-regs -static`

我们现在已经有了一个可以在我们的 Android 设备上运行的 ARM 二进制文件！

```
$ file arm-dump-regs                                                                                 
arm-dump-regs: ELF 32-bit LSB executable, ARM, EABI5 version 1 (SYSV), statically linked, for GNU/Linux 3.2.0, BuildID[sha1]=8b02c787af1e98e974720d1e6822c7ba99dd2e59, not stripped 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们可以通过 ADB 推送到我们的 Android 设备:`adb push arm-dump-regs /data/local/tmp`

[注意:运行此应用程序需要您的设备上有 Root 用户！]
并运行:

```
OnePlus3:/data/local/tmp # ./arm-dump-regs 2206
REG[0]: 888f4
REG[1]: 88914
REG[2]: 88908
REG[3]: 0
REG[4]: ffd3da73
REG[5]: 2f
REG[6]: ffd3da72
REG[7]: 10158
REG[8]: 4990c
REG[9]: 0
REG[10]: 87098
REG[11]: 88448
REG[12]: 10b80
REG[13]: 10b28
REG[14]: 10158
REG[15]: 88460
REG[16]: 10158
REG[17]: 0
O 
```

Enter fullscreen mode Exit fullscreen mode

你会看到寄存器转储！
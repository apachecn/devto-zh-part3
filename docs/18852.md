# #ifndef 卫士与#pragma 对决一次

> 原文：<https://dev.to/fluffy/ifndef-guards-vs-pragma-once-m6k>

在深入讨论了`#pragma once`和`#ifndef`保护之间的性能权衡与正确性与否的争论之后(我是基于最近的一些灌输来站在`#pragma once`一边的)，我决定最终测试一下理论，即`#pragma once`更快，因为编译器不必尝试重新`#include`一个已经被包含的文件。

为了测试，我自动生成了 500 个具有复杂相互依赖关系的头文件，并用一个`.c`文件将它们全部包含在内。我用三种方式运行测试，一次只使用`#ifndef`，一次只使用`#pragma once`，还有一次两者都使用。我在一个相当现代的系统上进行测试(一台运行 OSX 的 2014 款 MacBook Pro，使用 XCode 的捆绑 Clang，内置 SSD)。

一、测试代码:

```
#include <stdio.h> 
//#define IFNDEF_GUARD
//#define PRAGMA_ONCE

int main(void)
{
    int i, j;
    FILE* fp;

    for (i = 0; i < 500; i++) {
        char fname[100];

        snprintf(fname, 100, "include%d.h", i);
        fp = fopen(fname, "w");

#ifdef IFNDEF_GUARD
        fprintf(fp, "#ifndef _INCLUDE%d_H\n#define _INCLUDE%d_H\n", i, i);
#endif
#ifdef PRAGMA_ONCE
        fprintf(fp, "#pragma once\n");
#endif 

        for (j = 0; j < i; j++) {
            fprintf(fp, "#include \"include%d.h\"\n", j);
        }

        fprintf(fp, "int foo%d(void) { return %d; }\n", i, i);

#ifdef IFNDEF_GUARD
        fprintf(fp, "#endif\n");
#endif 
        fclose(fp);
    }

    fp = fopen("main.c", "w");
    for (int i = 0; i < 100; i++) {
        fprintf(fp, "#include \"include%d.h\"\n", i);
    }
    fprintf(fp, "int main(void){int n;");
    for (int i = 0; i < 100; i++) {
        fprintf(fp, "n += foo%d();\n", i);
    }
    fprintf(fp, "return n;}");
    fclose(fp);
    return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我的各种测试运行:

```
#  gcc pragma.c -DIFNDEF_GUARD
#  ./a.out
#  time gcc -E main.c  > /dev/null
 real    0m0.164s
user    0m0.105s
sys 0m0.041s #  time gcc -E main.c  > /dev/null
 real    0m0.140s
user    0m0.097s
sys 0m0.018s #  time gcc -E main.c  > /dev/null
 real    0m0.193s
user    0m0.143s
sys 0m0.024s #  gcc pragma.c -DPRAGMA_ONCE
#  ./a.out
#  time gcc -E main.c  > /dev/null
 real    0m0.153s
user    0m0.101s
sys 0m0.031s #  time gcc -E main.c  > /dev/null
 real    0m0.170s
user    0m0.109s
sys 0m0.033s #  time gcc -E main.c  > /dev/null
 real    0m0.155s
user    0m0.105s
sys 0m0.027s #  gcc pragma.c -DPRAGMA_ONCE -DIFNDEF_GUARD
#  ./a.out
#  time gcc -E main.c  > /dev/null
 real    0m0.153s
user    0m0.101s
sys 0m0.027s #  time gcc -E main.c  > /dev/null
 real    0m0.181s
user    0m0.133s
sys 0m0.020s #  time gcc -E main.c  > /dev/null
 real    0m0.167s
user    0m0.119s
sys 0m0.021s #  gcc --version
Configured with: --prefix=/Applications/Xcode.app/Contents/Developer/usr --with-gxx-include-dir=/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.12.sdk/usr/include/c++/4.2.1
Apple LLVM version 8.1.0 (clang-802.0.42)
Target: x86_64-apple-darwin17.0.0
Thread model: posix
InstalledDir: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，带有`#pragma once`的版本在预处理方面确实比`#ifndef`稍快——只有一个版本*，但是*的差异是可以忽略的，并且会被实际构建和链接代码所花费的时间所掩盖。也许有了足够大的代码库，它实际上可能会导致几秒钟的构建时间差异，但在现代编译器能够优化`#ifndef`防护、操作系统具有良好的磁盘缓存的事实以及存储技术不断提高的速度之间，性能争论似乎没有意义，至少在当今这个时代的典型开发人员系统上是如此。旧的和更奇特的构建环境(例如，托管在网络共享上的头文件、从磁带构建等。)可能会在某种程度上改变这个等式，但是在这种情况下，首先简单地创建一个不那么脆弱的构建环境似乎更有用。

事实是，`#ifndef`被标准化为标准行为，而`#pragma once`不是，并且`#ifndef`也处理奇怪的文件系统和搜索路径拐角情况，而`#pragma once`可能被某些事情弄得非常混乱，导致程序员无法控制的不正确行为。`#ifndef`的主要问题是程序员为他们的守卫选择了不好的名字(有名字冲突等等)，即使这样，API 的消费者也很有可能使用`#undef`来覆盖那些不好的名字——也许不是一个完美的解决方案，但是有可能是*，而如果编译器错误地剔除了`#include`，那么`#pragma once`就没有办法了。*

 *因此，*虽然* `#pragma once`明显(稍微)快了些，但我不认为这本身就是用它胜过`#ifndef`守卫的理由。*
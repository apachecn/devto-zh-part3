# golang 中的连接字符串快速基准:+或 fmt。Sprintf？

> 原文：<https://dev.to/pmalhaire/concatenate-strings-in-golang-a-quick-benchmark-4ahh>

# 在`golang`中串接字符串进行快速基准测试

## 简介

当我开始与不同资历级别的开发人员一起输入`golang`代码库时。我很惊讶地看到了许多连接字符串的方法。用`one`和`two`制作`onetwo`在任何编程语言中都被大量使用。

我决定做一个快速基准测试。从`C/C++`世界来到`golang`的结果让我大吃一惊(好的方面)。

## 起源

让我们使用 sprintf 以 c 语言的方式来完成它。

我在这里使用指针来模拟我们在函数中的事实。

```
#include <stdlib.h>
#include <stdio.h> 
int main(){
    char str[] = "my_string";
    char *s = malloc(sizeof(str) + sizeof(str));
    sprintf(s, "my_string%s", str);
    printf("%s\n", s);
} 
```

Enter fullscreen mode Exit fullscreen mode

## 我们去戈朗吧

让我们像在 c 语言中那样做。这对我来说是一种本能的方式(至少当我开始使用 golang 时)。

```
package main

import "fmt"

func main() {
    str := "my_string"
    fmt.Println(fmt.Sprintf("my_string%s", str))
} 
```

Enter fullscreen mode Exit fullscreen mode

## 让我们试试另一种方式`strings.Join`

与他人讨论后，我意识到`strings.Join`很受欢迎。

```
package main

import (
    "fmt"
    "strings"
)

func main() {
    str := "my_string"
    fmt.Println(strings.Join([]string{"my_string", str}, ""))
} 
```

Enter fullscreen mode Exit fullscreen mode

## 让我们用 C++的方式试试`strings.Builder`

在`C++`和`Java`体验过 string builder 之后，我想我可以试一试。

```
package main

import (
    "fmt"
    "strings"
)

func main() {
    str := "my_string"
    var b strings.Builder
    b.WriteString("my_string")
    b.WriteString(str)
} 
```

Enter fullscreen mode Exit fullscreen mode

## 让我们试试 Cpp 方式使用`+`

Plus 有点显而易见，但感觉并不聪明。

```
package main

import "fmt"

func main() {
    str := "my_string"
    fmt.Println("my_string" + str)
} 
```

Enter fullscreen mode Exit fullscreen mode

## 板凳告诉我们

Golang 有大量的测试工具。我们可以非常容易地进行基准测试。

```
$go test -bench=. > bench.result
goos: linux
goarch: amd64

BenchmarkSprintf-4          10000000           156 ns/op
BenchmarkLongSprintf-4      10000000           186 ns/op
BenchmarkConstSprintf-4     10000000           158 ns/op

BenchmarkJoin-4             20000000            68.8 ns/op
BenchmarkLongJoin-4         20000000            86.9 ns/op
BenchmarkConstJoin-4        20000000            66.1 ns/op

BenchmarkBuilder-4          20000000           104 ns/op
BenchmarkLongBuilder-4      20000000           101 ns/op
BenchmarkConstBuilder-4     20000000           102 ns/op

BenchmarkPlus-4             50000000            25.9 ns/op
BenchmarkLongPlus-4         20000000            74.4 ns/op
BenchmarkConstPlus-4        2000000000           0.39 ns/op 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

令人惊讶的是(至少对我来说)一个简单的`+`是最快的。

## 幕后

写这篇文章的时候，我注意到`golang`和`C`的二进制文件大小非常不同:

*   `golang`:1.9 米
*   `c`:83k

这并不奇怪，因为`C`不包括任何运行时，但这让我很好奇。

为了了解 golang 和`C`程序的不同之处，我使用`strace`查看了生成的系统调用。在 [`strace`](https://jvns.ca/blog/2016/05/13/erlang-seems-really-complicated/) 上看到这个有趣的帖子。

注意:正如评论中所说，它与字符串连接无关，但值得在以后的文章中讨论。

### C 版本

```
#include <stdlib.h>
#include <stdio.h> 
int main(){
    char str[] = "my_string";
    char *s = malloc(sizeof(str) + sizeof(str));
    sprintf(s, "my_string%s", str);
    printf("%s\n", s);
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们看看斯特拉斯。

```
gcc -o sprintf sprintf.c
strace -c ./sprintf
my_stringmy_string
% time     seconds  usecs/call     calls    errors syscall
----------- ----------- ----------- --------- --------- ----------------
 24.84    0.000305          23        13           mmap
 23.05    0.000283          28        10           mprotect
 10.18    0.000125          25         5           openat
  8.63    0.000106         106         1           munmap
  8.14    0.000100          17         6         6 access
  4.48    0.000055          14         4           read
  4.15    0.000051           9         6           fstat
  3.83    0.000047          47         1           write
  3.34    0.000041          41         1           arch_prctl
  2.44    0.000030           6         5           close
  2.12    0.000026           9         3           brk
  1.47    0.000018          18         1           execve
  1.14    0.000014           7         2           rt_sigaction
  0.65    0.000008           8         1           prlimit64
  0.57    0.000007           7         1           set_tid_address
  0.49    0.000006           6         1           rt_sigprocmask
  0.49    0.000006           6         1           set_robust_list
----------- ----------- ----------- --------- --------- ----------------
100.00    0.001228                    62         6 total 
```

Enter fullscreen mode Exit fullscreen mode

### 戈朗版

```
package main

import "fmt"

func main() {
    str := "my_string"
    fmt.Println("my_string" + str)
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们看看斯特拉斯。

```
$ go build plus.go
$ strace -c ./plus
my_stringmy_string
% time seconds  usecs/call     calls    errors syscall
----------- ----------- ----------- --------- --------- ----------------
 69.80    0.001900          17       114           rt_sigaction
  8.74    0.000238          79         3           clone
  5.95    0.000162          20         8           rt_sigprocmask
  4.70    0.000128          32         4           futex
  4.41    0.000120         120         1           readlinkat
  2.24    0.000061           8         8           mmap
  1.84    0.000050          50         1           write
  1.69    0.000046          15         3           fcntl
  0.62    0.000017          17         1           gettid
  0.00    0.000000           0         1           execve
  0.00    0.000000           0         2           sigaltstack
  0.00    0.000000           0         1           arch_prctl
  0.00    0.000000           0         1           sched_getaffinity
----------- ----------- ----------- --------- --------- ----------------
100.00    0.002722                   148           total 
```

Enter fullscreen mode Exit fullscreen mode

### `strace`告诉我们什么

Go 比 c 做更多的系统调用，这看起来是合理的，因为它有一个 gc。

为了更好地理解，我邀请你分解结果二进制文件。

事实上，我们还必须查看一下`C`和`golang`的默认编译参数，以便进行适当的比较。

## 代码参考

下面是用于测试不同选项的代码。

```
package main_test

import (
    "fmt"
    "strings"
    "testing"
)

var str, longStr string = "my_string", `qwertyuiopqwertyuiopqwertyuio
qwertyuiopqwertyuiopqwertyuiopqwertyuiopqwertyuiopqwertyuiopqwertyuiop`

const cStr = "my_string"

func BenchmarkPlus(b *testing.B) {
    for n := 0; n < b.N; n++ {
        _ = "my_string" + str
    }
}

func BenchmarkLongPlus(b *testing.B) {
    for n := 0; n < b.N; n++ {
        _ = "my_string" + longStr
    }
}

func BenchmarkConstPlus(b *testing.B) {
    for n := 0; n < b.N; n++ {
        _ = "my_string" + cStr
    }
}

func BenchmarkJoin(b *testing.B) {
    for n := 0; n < b.N; n++ {
        _ = strings.Join([]string{"my_string%s", str}, "")
    }
}

func BenchmarkLongJoin(b *testing.B) {
    for n := 0; n < b.N; n++ {
        _ = strings.Join([]string{"my_string%s", longStr}, "")
    }
}

func BenchmarkConstJoin(b *testing.B) {
    for n := 0; n < b.N; n++ {
        _ = strings.Join([]string{"my_string%s", cStr}, "")
    }
}
func BenchmarkSprintf(b *testing.B) {
    for n := 0; n < b.N; n++ {
        _ = fmt.Sprintf("my_string%s", str)
    }
}

func BenchmarkLongSprintf(b *testing.B) {
    for n := 0; n < b.N; n++ {
        _ = fmt.Sprintf("my_string%s", longStr)
    }
}

func BenchmarkConstSprintf(b *testing.B) {
    for n := 0; n < b.N; n++ {
        _ = fmt.Sprintf("my_string%s", cStr)
    }
}

func BenchmarkBuilder(b *testing.B) {
    for n := 0; n < b.N; n++ {
        var b strings.Builder
        b.WriteString("my_string")
        b.WriteString(longStr)
    }
}

func BenchmarkLongBuilder(b *testing.B) {
    for n := 0; n < b.N; n++ {
        var b strings.Builder
        b.WriteString("my_string")
        b.WriteString(longStr)
    }
}

func BenchmarkConstBuilder(b *testing.B) {
    for n := 0; n < b.N; n++ {
        var b strings.Builder
        b.WriteString("my_string")
        b.WriteString(longStr)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode
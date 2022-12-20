# 越快越好

> 原文：<https://dev.to/tonymet/getting-to-yes-as-quickly-as-possible-1263>

一年前[有一场关于 gnu 版本的“是”有多快的大讨论](https://www.reddit.com/r/unix/comments/6gxduc/how_is_gnu_yes_so_fast/)。如果你不熟悉的话，`yes`无限输出`y`。

```
yes |head -5
y
y
y
y
y 
```

关键的一点是`write`很昂贵，而写页面对齐的缓冲区要快得多。跨语言都是如此，所以让我们看看如何在 go 中正确地做到这一点。

如果你对结果感到震惊或印象深刻，让我们看看你是如何用自己的语言做到这一点的——在评论中发布你的结果。

首先，我们去年文章中的基准 C 代码。

```
/* yes.c - iteration 4 */
#define LEN 2
#define TOTAL 4096 * 4
int main() {
    char yes[LEN] = {'y', '\n'};
    char *buf = malloc(TOTAL);
    int bufused = 0;
    while (bufused < TOTAL) {
        memcpy(buf+bufused, yes, LEN);
        bufused += LEN;
    }
    while(write(1, buf, TOTAL));
    return 1;
} 
```

现在是吞吐量基准

```
$ gcc yes.c -o yes_c
$ ./yes_c |pv >/dev/null
...[3.21GiB/s] ... 
```

所以我们的基准速率= **3.21 GB/s**

## 回合 1 -队长明显

假设:这将是缓慢的。我猜比基准测试快 20%。

```
package main

import (
    "fmt"
    "os"
)

func main() {
    for {
        fmt.Fprintln(os.Stdout, "y")
    }
} 
```

```
$ go run yes.go |pv > /dev/null 
$ ...[1.07MiB/s] ... 
```

呀！1.07 MB/s，仅为我们基准测试的 **0.03%** 。💩假设成立！

## 循环 2 页对齐的缓冲区

注意在 Darwin Pagesize X 4 上产生了最好的结果——我会让读者去猜测为什么。

```
package main

import (
    "os"
)

func main() {
    buf := make([]byte, os.Getpagesize()*4)
    for i := 0; i < len(buf); i += 2 {
        buf[i], buf[i+1] = 'y', '\n'
    }
    for {
        os.Stdout.Write(buf)
    }
} 
```

结果:

```
$ go run yes.go |pv > /dev/null 
$ ...[3.15GiB/s] ... 
```

结果:3.15 GB/s 或基准测试的 98.13%。我们有一个赢家🏁

➡️:用你最喜欢的语言，你多快能说“是”？
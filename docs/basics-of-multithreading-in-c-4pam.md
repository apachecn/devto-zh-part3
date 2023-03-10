# C 语言多线程基础

> 原文：<https://dev.to/quantumsheep/basics-of-multithreading-in-c-4pam>

c 是一种默认运行在一个线程上的语言，这意味着代码一次只会运行一条指令。在某些情况下，你需要一次执行多个指令，比如一个图形界面，当它执行一个与按钮点击相关的动作时，它不会停止。这就是我们所说的多线程，不要与异步操作混淆，异步操作只能在一个执行多个任务的线程中执行。

# 什么是线程？

线程是链接到进程运行的任务。一个进程我们可以有多个线程，线程可以运行其他线程等等。

默认情况下，一个进程在一个线程上运行。每个线程都是新任务，可以无限期运行，并与其他线程并行运行。

# 创建线程

就像题目里说的，这个帖子会讲 C 里面的多线程，所以我们就做 C 吧！

在 POSIX 操作系统上，有一个名为`pthread.h`的库，正如它所说的那样，创建线程！要在编译器下使用它，你需要用`-lpthread`参数链接它(例如:`gcc -lpthread main.c`)。

```
#include <pthread.h>
#include <unistd.h>
#include <stdio.h> 
void
wait(void)
{
    sleep(2);

    printf("Done.\n");
}

int
main(void)
{
    pthread_t thread;
    int err;

    err = pthread_create(&thread, NULL, wait, NULL);

    if (err)
    {
        printf("An error occured: %d", err);
        return 1;
    }

    printf("Waiting for the thread to end...\n");

    pthread_join(thread, NULL);

    printf("Thread ended.\n");    

    return 0;
} 
```

输出将是:

```
Waiting for the thread to end...
Done. (~2 seconds after)
Thread ended. 
```

当创建的线程运行时，主线程继续运行并输出一条消息，在一个新线程中调用一个函数只需要一行代码，对 C 来说很容易？也许你有点迷路了，我来解释代码。

首先，我们包含了`pthread.h`库，就像上面说的，它包含了执行多线程任务所需的所有函数。然后我们包含了包含了`sleep()`函数的`unistd.h`。然后是代表`printf()`的`stdio.h`。

`pthread_create`函数被调用来创建线程。它需要一个`pthread_t`，这是一个线程描述符，一个指向`void`函数的指针，还有一些我在这里不会描述的其他参数，比如函数的参数。

`pthread_join`用于将当前线程进程链接到另一个线程。它实际上使程序停止，以等待所选线程的结束。

* * *

# 互斥体

同时在一个目标上进行多个操作是非常危险的，我能给出的最好的例子就是数据库。如果三个线程想同时写一个文件，这将是一个问题，因为硬盘驱动器不能像 CPU 那样快。在这种情况下，我们应该锁定其他线程，以免硬盘上的任务过载，损坏文件。互斥体可以用来锁定其他线程。

在实践中，我们使用互斥来判断任务是锁定还是解锁。

```
#include <pthread.h>
#include <unistd.h>
#include <stdio.h> 
pthread_mutex_t lock;
int j;

void
do_process()
{
    pthread_mutex_lock(&lock);
    int i = 0;

    j++;

    while(i < 5)
    {
        printf("%d", j);
        sleep(1);

        i++;
    }

    printf("...Done\n");

    pthread_mutex_unlock(&lock);
}

int
main(void)
{
    int err;
    pthread_t t1, t2;

    if (pthread_mutex_init(&lock, NULL) != 0)
    {
        printf("Mutex initialization failed.\n");
        return 1;
    }

    j = 0;

    pthread_create(&t1, NULL, do_process, NULL);
    pthread_create(&t2, NULL, do_process, NULL);

    pthread_join(t1, NULL);
    pthread_join(t2, NULL);

    return 0;
} 
```

输出将是:

```
111111...Done
222222...Done 
```

所以，正如你所看到的，线程`t2`完全是在`t1`之后完成的，所以互斥锁起作用了！这不是`pthread_join`调用的结果，因为它们只改变了当前线程(这里是主线程)的一些东西，你可以尝试在 t1 之前加入 T2，它会执行同样的事情。

# 信号量

信号量和互斥量非常相似，可以被任何线程控制。所有权的概念在信号量的情况下是不存在的，所以它们可以被程序的任何部分锁定和解锁。

* * *

那很容易，你不觉得吗？执行这些行动的职能是非常强大的，强大的权力意味着巨大的责任。

[![SuperMan](img/dcef9123ae18f62331266f1122611cea.png)](https://i.giphy.com/media/DQ100zzcakfBe/giphy.gif)
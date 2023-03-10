# select()与 poll()的问题

> 原文：<https://dev.to/fluffy/the-problem-with-select-vs-poll-2k1h>

UNIX `select()` API 早在几年前就应该被弃用了。虽然像`sscanf()`、`sprintf()`、`gets()`等不安全操作都提供编译时弃用警告，但`select()`也非常危险，有了更现代、更安全的替代(`poll()`，但人们仍在继续使用它。

问题是它不能扩展。在这种情况下，“不可伸缩”不仅仅意味着它对性能不利，“不可伸缩”意味着它会破坏您的调用堆栈，使您的进程崩溃，并使其处于难以调试的状态。

实际上有两个问题，而且是密切相关的。对这两者的提示来自 select 函数的实际签名:

```
void FD_CLR(fd, fd_set *fdset);

void FD_COPY(fd_set *fdset_orig, fd_set *fdset_copy);

int FD_ISSET(fd, fd_set *fdset);

void FD_SET(fd, fd_set *fdset);

void FD_ZERO(fd_set *fdset);

int select(int nfds, fd_set *restrict readfds, fd_set *restrict writefds,
           fd_set *restrict errorfds, struct timeval *restrict timeout); 
```

你看到那个 **`nfds`** 参数了吗？以下是 BSD libc 文档对此的描述:

> 在每个集合中检查第一个`nfds`描述符；即，检查描述符集中从 0 到 nfds-1 的描述符。(例如:如果您设置了两个文件描述符“4”和“17”，那么 nfds 不应该是“2”，而应该是“17 + 1”或“18”。)

这意味着 fd_set 的实现不是一个包含最大数量稀疏填充的文件描述符的容器；相反，它是一个位向量，表示是否在给定的偏移处检查 fds。也就是说，在伪代码中，人们期望它类似于:

```
struct fd_set {
    int count;
    struct fd_record {
        int fd;
        bool check;
    } fds[FD_SETSIZE];
}; 
```

但是真的更像:

```
struct fd_set {
    bool check[FD_SETSIZE];
}; 
```

因此，这立即显示了这两个问题是什么，但我仍将进一步解释它(因为当我帮助诊断一些关键生产代码中断的问题时，这还不够)。这两者都涉及到当您的进程打开大量文件描述符时会发生什么(这在大规模服务中非常常见)。

### 第一题:性能

假设您正试图检查 fd 为 907 的单个套接字的状态——在现代 Linux 上，FD 恰好在`FD_SETSIZE`之内。所以你的代码看起来像这样:

```
fd_set fds;
FD_ZERO(&fds);
FD_SET(907, &fds);
select(908, &fds, NULL, NULL, NULL); 
```

下面是 select()在内部做的事情(同样是伪代码):

```
for (int i = 0; i < 908; i++) {
    if (readfds && readfds->check[i]) {
        readfds->check[i] = check_readable(i);
    }
    if (writefds && writefds->check[i]) {
        writefds->check[i] = check_writeable(i);
    }
    if (errorfds && errorfds->check[i]) {
        errorfds->check[i] = check_errored(i);
    }
} 
```

在这种情况下，因为实际上只检查了 fd 907，所以它在不需要检查的 906 个条目上徒劳地循环。如果你做了很多，这可能会增加(记住上面描述的结构只是伪代码，实际上有很多位篡改/屏蔽/等等。也是如此)。

但这不是可怕的问题。

### 问题 2:它如何破坏你的堆栈

所以让我们来看看和上面一样的东西，除了现在讨论的 fd 是，比如说，2048，比`FD_SETSIZE`大很多。理论上这是不可能的，但实际上是可能的——在需要打开大量连接的情况下，提高 socket ulimit 非常简单。(例如，在大规模分布式缓存中。再说一次，从这方面的经验来看。)

先来注释一下调用它的代码:

```
fd_set fds; // allocates a 1024-bit vector on the stack
FD_ZERO(fds); // clears the 1024-bit vector
FD_SET(2048, &fds); // Sets the 2048th bit; congratulations, you've corrupted your stack a little bit
select(2049, &fds, NULL, NULL, NULL); // congratulations, you've just BLOWN AWAY your stack 
```

为什么这会吹走堆栈？嗯，请记住`select()`在内部做什么:

```
for (int i = 0; i < 2049; i++) {
    if (readfds && readfds->check[i]) {
        readfds->check[i] = check_readable(i);
    }
    if (writefds && writefds->check[i]) {
        writefds->check[i] = check_writeable(i);
    }
    if (errorfds && errorfds->check[i]) {
        errorfds->check[i] = check_errored(i);
    }
} 
```

也就是说，对于到 2048 为止的所有值(请记住，这些值在`fd_set`之外，此时已经在您的堆栈中)，它会进行一点检查，看看是否需要检查一个垃圾 fd(这相对无害)，检查那个 fd(这相对无害，但会影响您的性能)，然后根据 fd 是否处于该特定状态，在内存中设置该值(无论如何，这实际上是将堆栈随机化)。

恭喜你，现在你不仅会得到一个奇怪的错误，你甚至不能告诉它来自哪里，因为你的调试器会给你一个完整的调用跟踪垃圾。

### 简单的解决方法:使用 poll()

当然，后见之明是 20/20，但`poll()`是`select()`一开始就应该有的 API。在设计`select()`的时候，UNIX 根本没有异步 I/O 机制，整个大规模网络服务器的概念闻所未闻。一切都是它自己的过程，你可以做一些类似`accept(8)`的事情，一次只接受 8 个同时连接，每个连接可能会打开一些文件来提供静态内容或其他内容。在早期，`FD_SETSIZE`才 16 岁；Winsock 提高到 64，早期 Linux 提高到 256。对于当时规模合理的服务器来说，这已经足够了，而且通常情况下，您会在一个单线程事件循环中同时检查所有的 FD(见鬼，在当时,“thread”的整个概念只是对`vfork()`的滥用，任何有理智的程序员都不会期望使用它),并且不会有那么多要检查的；管理可变大小缓冲区的开销将大于没有条件循环所节省的开销。

但如今，任何给定的互联网服务器都可以轻松地同时处理数千个(如果不是数万个)连接，而`FD_SET` / `select()` API 从根本上被破坏了，这种方式永远不会让它变得安全。

幸运的是，还有另一个 API，`poll()`，它更像是人们最初对`select()`的期望。

```
struct pollfd {
    int    fd;       /* file descriptor */
    short  events;   /* events to look for */
    short  revents;  /* events returned */
};

int poll(struct pollfd fds[], nfds_t nfds, int timeout); 
```

本质上，你准确地告诉它你要询问多少个 fd，以及对于每个 FD，你想知道哪些事件。所以现在`poll()`调用只需要扫描实际指定的 FD，并且只检查实际指定的事件——不检查所有 FD 的可读、可写和错误条件，这些 FD≤您询问的最高 FD。

在常见的情况下，`poll()`是一个简单的插入替换，通常更简单，更容易阅读；此代码:

```
fd_set fds;
FD_ZERO(fd_set);
FD_SET(500, &fds);
struct timeval tv;
tv.tv_sec = 5;
tv.tv_usec = 0;
if (select(501, &fds, NULL, NULL, &tv)) {
    if (FD_ISSET(500, &fds)) { ... }
} 
```

变成:

```
pollfd pfd;
pfd.fd = 500;
pfd.events = POLLIN;
if (poll(&pfd, 1, 5000)) {
    if (pfd.revents & POLLIN) { ... }
} 
```

而且，很明显，如果您想在一个套接字上检查多种类型的事件，它会变得更简单，因为您只需要一组 pollfd 结构和调用方的简单位屏蔽。

### 那为什么现在还有人用`select()`写代码呢？

我有几个理论。`select()`是所有经典教材中提供的遗留 API 大学可能仍然使用它来教授网络，并且有很多使用`select`的示例代码，即使是最好的程序员有时也仍然倾向于复制粘贴。

此外，事实上 gcc 并没有发出反对警告，这意味着人们没有任何理由改变他们的做法。手册页往往包含警告，但人们通常只是阅读签名和返回代码。

如果 nfds ≥ `FD_SETSIZE`，某些 libc 变体的当前版本将返回一个错误(例如，OSX 10.8 上的版本将返回，尽管即使在当前的 Linux glibc 上也没有这种影响的警告)，但在这一点上，堆栈已经损坏，如果您的服务器已经到达一个微妙的难以理解的 glibc 错误正在发生的点，您已经度过了糟糕的一天，并且可能没有意识到问题来自哪里。

值得一提的是，`select()`的一个版本的 Linux 联机帮助页是这样说的:

> 一个`fd_set`是固定大小的缓冲区。执行`FD_CLR()`或`FD_SET()`的值`fd`为负或等于或大于`FD_SETSIZE`将导致未定义的行为。此外，POSIX 要求`fd`是有效的文件描述符。

“未定义的行为”说得太轻了。(它没有提到在那种情况下`select()`本身也是一个定时炸弹。)

### 那`epoll`和 libEvent 呢？

我故意不在本文中讨论它们。虽然两者都远远优于`poll`，但它们不是`select`的简单替代，至少在互联网服务器的基本情况下，更重要的事情是不会神秘地崩溃。很明显，有了更好的异步事件机制，你就可以更好地伸缩，但是更好的伸缩通常是一个比伸缩更长期的目标。
# linux 上的程序是如何执行的？

> 原文：<https://dev.to/k4ml/how-program-on-linux-executed-50oc>

只是一个自我提醒，让我对这个问题有个清醒的认识，因为这个问题时不时会冒出来，我不得不去谷歌一下来回答这个问题。

*   [程序如何执行？](https://linuxgazette.net/84/hawk.html) -关键字“main()如何执行”。
*   维基百科[执行(计算)](https://en.wikipedia.org/wiki/Execution_(computing)) -没有多少具体的信息，但仍然有助于触发你大脑中的长期记忆部分；)
*   [了解 Linux 可执行文件的内存布局](https://gist.github.com/CMCDragonkai/10ab53654b2aa6ce55c11cfc5b2432a4) -关键字——“Linux 起始内存地址”。
*   这篇关于[用 Rust](https://os.phil-opp.com/minimal-rust-kernel/) 构建操作系统的文章也提供了操作系统如何加载和执行程序的信息。

## 注入运行过程

*   通过收集当前正在运行的进程的 PID，将其附加到该进程。
*   向程序发送一个 SIGSTOP 来停止它的执行
*   转储其寄存器(特别是 rip/eip)
*   把你的代码写到 rip 指向的栈里。
*   向程序发送 SIGCONT 以返回控制。
*   利润。

[https://www . real 0 day . com/hacking-tutorials/2017/11/6/injecting-a-running-process-Linux](https://www.real0day.com/hacking-tutorials/2017/11/6/injecting-a-running-process-linux)

也看看 strace 是如何工作的。

## 总结

*   main()是在 libc 中定义的——libc 中假定 C 程序中第一个要调用的函数被命名为`main()`。
*   ELF 头中定义了包含程序开始的加载地址。

[https://stack overflow . com/questions/7187981/whats-the-memory-before-0x 08048000-used-in-32-bit-machine](https://stackoverflow.com/questions/7187981/whats-the-memory-before-0x08048000-used-for-in-32-bit-machine)

## 其他类似话题

*   当你在浏览器的地址栏中输入 google.com 并按回车键时会发生什么？
*   当你打开电脑时会发生什么？与上面的问题不同，我找不到一篇好的文章，只是太多了
    *   [https://bhavyanshu . me/what-happens-when-you-switch-on-your-computer-facts-the-most computer-science-engineers-would-know/10/20/2014/](https://bhavyanshu.me/what-happens-when-you-switch-on-your-computer-facts-that-most-computer-science-engineers-wouldnt-know/10/20/2014/)
    *   [https://medium . com/10 xcod3r/what-really-happens-when-you-switch-on-a-computer-defe 74 ccab 4](https://medium.com/10xcod3r/what-really-happens-when-you-switch-on-a-computer-defe74ccaba4)
    *   [https://www . quora . com/What-exact-happens-when-we-switch-on-the-computer](https://www.quora.com/What-exactly-happens-when-we-switch-on-the-computer)
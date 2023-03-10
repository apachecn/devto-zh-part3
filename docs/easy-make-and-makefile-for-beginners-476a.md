# 初学者的简易制作和 Makefile

> 原文：<https://dev.to/nerfur/easy-make-and-makefile-for-beginners-476a>

*make* 是编程中最重要的工具之一。无论是过去还是现在。但也很可怕，因为许多初学者只看到已经建立的大型项目的 Makefile 文件，里面有大量的行。

不要害怕！完成这个简短的“技巧”后，基本的 Makefile 就变得相当容易了。

例如，让我们假设您有命令行来编译您的小 C 代码，如下所示:

gcc-I/usr/local/include-L/usr/local/lib-Wall-o my-c-app . out my-c-app . c

只需打开您最喜欢的文本编辑器，将此命令粘贴到里面

现在你只需要把它拆分成对 make 进行排序的变量，你可以选择你想要的名字(这里有一些常见的做法，顺便提一下)
让我们来拆分它

CC = gcc

OBJECTS = my-c-app.c

OBJECT_OUT = my-c-app.out

CFLAGS = -Wall

包括=-I/usr/local/include-L/usr/local/lib

并将命令中的值与这些变量交换

$(CC)$(INCLUDES)$(CFLAGS)-o $(OBJECT _ OUT)$(OBJECTS)

最后一点——我们需要为**制造**添加*目标*——默认为*全部*

所以我们将命令行转换为
all:$(OBJECTS)
$(CC)$(INCLUDES)$(CFLAGS)-o $(OBJECT _ OUT)$(OBJECTS)(这一行必须始终以 Tab 开头！！！)

所以，现在我们有这样的文件

```
CC = gcc

OBJECTS = my-c-app.c

OBJECT_OUT = my-c-app.out

CFLAGS = -Wall

INCLUDES = -I/usr/local/include -L/usr/local/lib

all: $(OBJECTS)
    $(CC) $(INCLUDES) $(CFLAGS) -o $(OBJECT_OUT) $(OBJECTS) 
```

现在您可以运行 *make* 并开始享受 Makefile 的魔力了！

祝你好运！
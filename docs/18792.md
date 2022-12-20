# 开始使用断言！

> 原文：<https://dev.to/noah11012/start-using-asserts-452c>

我见过有人这样编码，因为他们不知道一个重要的细节。新 C++程序员(或者一些 C 程序员)中常见的一个就是检查`new`的返回值。

```
int *array_of_ints = new int[100];

if(!array_of_ints)
{
    // handle error here
} 
```

这完全没有用，因为当内存分配失败时`new`会抛出一个异常。

另一个问题，这次是所有程序员都有的，就是他们的代码中缺少断言。

如果你碰巧不知道的话，断言是一个必须为真或者程序执行预定动作的语句，通常是退出整个应用程序。在退出之前捆绑了一条详细说明问题的消息，现在您可以清理您的代码库了。

我所有的例子都将在 C++中实现，但是这些概念可以应用于任何语言。

目前，我正在运行名为在 C++中使用 SDL2 的系列文章。本系列主要关注名为 SDL2 的库，它可以处理图形、音频、键盘和鼠标输入等。例如，我们可以使用断言，让我们看看如何初始化 SDL2。

在 SDL2 中，一个名为`SDL_Init()`的函数，它将标志进行“或”运算并返回一个值。如果这个值小于 0，那么就发生了错误，我们必须处理它。SDL2 还提供了一个名为`SDL_GetError()`的函数，它从最近失败的函数中获取错误消息。

```
if(SDL_Init(SDL_INIT_VIDEO | SDL_INIT_AUDIO) < 0)
{
    std::cout << "SDL2 Error: " << SDL_GetError() << "\n";
    exit(1);
}

// Continue on if successful 
```

这不仅仅是 use 断言有用的唯一地方。在进行任何图形操作之前，您必须创建一个窗口和一个渲染器。创建它们的两个函数都在失败时返回 0。

```
SDL_Window *window = SDL_CreateWindow(/* Arguments */);

if(window == 0)
{
    // print an error message and exit
}

SDL_Renderer *renderer = SDL_CreateRenderer(/* Arguments */);

if(renderer == 0)
{
    // again, print an error message and exit
} 
```

呃，已经失控了。如果能在退出之前检查结果并打印一条错误消息，那不是很好吗？现在是开始创建它的时候了。

我们将使用宏，因为它是一行程序。

```
#define assert(expression, message) if(expression == false) std::cout << message; exit(1) 
```

现在我们可以使用它了:

```
assert(SDL_Init(SDL_INIT_VIDEO | SDL_INIT_AUDIO) > -1, SDL_GetError());

SDL_Window *window = SDL_CreateWindow(/* Arguments */);
assert(window != 0);

SDL_Renderer *renderer = SDL_CreateRenderer(/* Arguments */);
assert(renderer != 0); 
```

干净多了，对吧？我们可以通过添加它出现在哪个行、函数和文件中来增强这一点。在 C++中，我们将使用标准/编译器提供的名为`__LINE__`、`__func__`和`__FILE__`的宏来实现这个目的。我们将不得不修改宏，在宏中填充我们稍后创建的函数的参数，该函数将实际打印消息以及其他信息并退出。

```
#define assert(expression, message) if(expression == false) assert_func(message, __LINE__, __func__, __FILE__) 
void assert_func(char const *message, int line, char const *function, char const *file)
{
    std::cout << "ASSERTION FAILED: \n" <<
                 "Message: " << message << "\n" <<
                 "Line: " << line << "\n <<  "Function: " << function << "\n" <<  "File: " << file << "\n";  } 
```

我们做到了！如此简单的东西现在有望使代码库更干净。

当然，断言不应该到处使用。例如，如果有人正在玩一个游戏，他们需要加载一个图像，但是失败了，他们可能会选择加载一个默认的图像，而不是退出整个游戏。

每个人对任何事情都有自己的偏好和看法，编程也不例外。也许你可以留言告诉我你喜欢如何处理错误？
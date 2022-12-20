# 如何使用 io？阅读器和 io。作者

> 原文：<https://dev.to/dayvonjersen/how-to-use-io-reader-and-io-writer-ao0>

当我从其他语言过来开始使用 Go 时，这让我很困惑。

我遇到的大多数技术文章和教程都多次提到了 [io。读者](https://godoc.org/io#Reader)和[木卫一。作者](https://godoc.org/io#Writer)，由 [io 包](https://godoc.org/io)提供的接口，描述它们优秀且易于使用，但似乎假设阅读的人已经知道如何使用它们。

这些抽象确实强大而方便，所以下面是如何在一些常见的场景中实际使用它们。

## 木卫一。读者

假设你有一些实现 io 的`reader`。阅读器接口，你想从中获取数据。

你可以制作一个容量为的字节片**，然后将这个片传递给`Read()`。** 

```
b := make([]byte, 512)
reader.Read(b) 
```

Enter fullscreen mode Exit fullscreen mode

Go 切片是引用类型，这意味着当调用`Read()`时，`b`将被*修改*，以包含来自`reader`的数据。

*但是，`Read()`只会复制所传递的片所能容纳的字节数。*

大多数时候，地鼠会伸手去抓 ioutil。ReadAll 当他们只想一次读取所有数据时。

你也可以使用一个[字节缓冲区](https://godoc.org/bytes#Buffer)和 [io。复制](https://godoc.org/io#Copy)。

例如，我做了一个函数，并一直使用它以字符串的形式获取文本文件的内容(受 php 的 [file_get_contents()](https://php.net/file-get-contents) )
的启发，也可能被破坏了)

```
func fileGetContents(filename string) string {
    contents := new(bytes.Buffer)
    f, err := os.Open(filename)
    checkErr(err)
    _, err = io.Copy(contents, f)
    if err != io.EOF {
        checkErr(err)
    }
    checkErr(f.Close())
    return contents.String()
} 
```

Enter fullscreen mode Exit fullscreen mode

> 上面的`checkErr()`只是`if err != nil { panic(err) }`

但是如果`reader`是一个长度不确定(或无限长)的数据流呢？

在这种情况下，你应该伸手去拿一个 bufio。扫描仪 :

```
scanner := bufio.NewScanner(os.Stdin)
for scanner.Scan() {
    fmt.Println(scanner.Text())
} 
```

Enter fullscreen mode Exit fullscreen mode

## 木卫一。作者

写数据非常简单:

```
someBytes := []byte("Hello world")
f, err := os.Open("someFile.txt")
checkErr(err)
f.Write(someBytes)
f.Close() 
```

Enter fullscreen mode Exit fullscreen mode

当然是木卫一。WriteString 是少打字:

```
f, err := os.Open("someFile.txt")
checkErr(err)
io.WriteString(f, "Hello world")
f.Close() 
```

Enter fullscreen mode Exit fullscreen mode

## 一个简单的字节缓冲

没有什么能阻止你实现 io。阅读器和 io。您自己的用户定义类型中的编写器。

例如，这里有一个非常有用的简单的字节缓冲:

```
type buf []byte

func (b *buf) Read(p []byte) (n int, err error) {
    n = copy(p, *b)
    *b = (*b)[n:]
    if n == 0 {
        return 0, io.EOF
    }
    return n, nil
}

func (b *buf) Write(p []byte) (n int, err error) {
    *b = append(*b, p...)
    return len(p), nil
}

func (b *buf) String() string { return string(*b) } 
```

Enter fullscreen mode Exit fullscreen mode

我最初做这个只是为了[在 go](https://github.com/generaltso/go-git-em-tiger/blob/e5ff73373269dd604a0cc6b5255f59c63d655636/cmd/tiger/cmd.go#L9-L41) 中捕获 shell 命令的文本输出。

## 

io。阅读器和 io。从 [shell 命令](https://godoc.org/os/exec#Cmd)到[联网](https://godoc.org/net#Conn)甚至 [http 包](https://godoc.org/net/http#Response)所有的标准库都使用了 Writer！

希望现在你知道了如何使用读取器和写入器的基本知识，也许你甚至可以尝试实现 io。阅读器和 io。您自己的用户定义类型的编写器。
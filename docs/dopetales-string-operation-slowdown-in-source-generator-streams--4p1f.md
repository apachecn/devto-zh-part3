# [DopeTales]源生成器流中的字符串操作速度减慢

> 原文：<https://dev.to/maheshattarde/dopetales-string-operation-slowdown-in-source-generator-streams--4p1f>

与其他软件相比，编译器更倾向于处理可读的字符串。十年前和六个月前写的源代码生成器没有太大区别。这里是试图改善这一点。

源生成器倾向于通过连接字符串来构建字符串。通常解决这个问题的方法是尝试 1

#### 尝试 1

```
std::string oBuffer;
oBuffer.append("ContextPrefix");
oBuffer.append("_");
oBuffer.append(pClassName.c_str());
Stream<<oBuffer.c_str(); 
```

Enter fullscreen mode Exit fullscreen mode

这种解决方案在内存中创建不同的字符串，导致内存分配，调整大小等。此外，还会降低整个软件堆栈的速度。用“+”运算符连接字符串也没有什么好处。

#### 尝试 2

```
StringBuffer  sBuffer;
sBuffer.append("ContextPrefix");
sBuffer.append("_");
sBuffer.append(pClassName.c_str());
Stream<<sBuffer.c_str(); 
```

Enter fullscreen mode Exit fullscreen mode

这个解决方案的灵感来自于 Java/CSharp 的后续工作和 Stackoverflow 的帮助。虽然 Cpp 不支持这一点，但编写自己的 StringBuffer 实现可以更加灵活地优化整个操作。尽管如此，我们将结束单独的缓冲区。

#### 尝试 3

```
typedef std::pair<const char *,const char *> PrefixString;
inline std::ostream& operator<< (std::ostream& pOut,PrefixString& pPair){
    pOut<<pPair.first<<"_"<<pPair.second;
    return pOut;
}
/*Using PrefixString in Source Generator code */
void SourceWriter::ClassBegin(const char * pQualifiedName){
    PrefixString oStr =  std::make_pair(Constants::ContextPrefix,pQualifiedName);
    mOut << oStr;
} 
```

Enter fullscreen mode Exit fullscreen mode

考虑到字符串总是存在于对象中，进行了第三次尝试。我们不需要单独的缓冲区来创建输出字符串。在上面的例子中，
PrefixString 是一对字符字符串指针的占位符。它可以直接写入前缀字符串，无需任何中间缓冲。而且运算符< <是内联的。大多数运行时决策都转移到了编译时。因此是更快的选择。

希望这有所帮助。
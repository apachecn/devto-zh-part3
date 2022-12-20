# C/C++指针对齐方式:一种对齐方式

> 原文：<https://dev.to/codemouse92/cc-pointer-alignment-style-a-justification-13po>

几乎所有的编码风格问题都是主观意见和个人品味的问题。制表符对空格，奥尔曼对 K&R，运算符填充对无；无论你站在哪里，你几乎肯定有一个你选择的风格的理由列表，你可以保证其他阵营也有。

有些语言，比如 Python，甚至定义了一大堆标准样式约定(PEP-8)。C 和 C++没有这样官方的、统一的标准。因此，在这些语言中有无数的风格转换。只要看看 AStyle 的文档有多庞大就知道了！

没关系！每个开发人员、项目和团队都能够找到适合他们的风格惯例，这样大家都是赢家。就我个人而言，我甚至会根据项目和我的心情在奥尔曼、K&R 和 Linux 内核包围式风格之间切换(尽管每个项目我只使用一种风格)。

但是，C 和 C++编码约定有一点我认为是主观意见之外的:**指针对齐**。

(P.S .对，题目是排版笑话。)

# 指针对齐:选项

如果您需要复习，通常有三种方法可以在 C 和 C++中对齐指针和引用标记:

```
// LEFT ALIGNMENT
int aVal;
int* aPtr;
int& aRef;
int& someFunc(int byVal, int* byPtr, int& byRef);

// CENTER ALIGNMENT
int aVal;
int * aPtr;
int & aRef;
int & someFunc(int byVal, int * byPtr, int & byRef);

// RIGHT ALIGNMENT
int aVal;
int *aPtr;
int &aRef;
int &someFunc(int byVal, int *byPtr, int &byRef);

// RELATED: Use of the * and & operators:
passByVal(*aPtr);
passByVal(*(aPtr+1));
passByPtr(&aVal);
passByRef(&aVal); 
```

Enter fullscreen mode Exit fullscreen mode

那么，我们应该使用左对齐、居中对齐还是右对齐呢？我给你一点时间选择立场。

[![Final Jeopardy](img/a601177e8df86b7c9e78c9eaf98e9274.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_-Qc6qsV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/18qq5a56b6s0kcufxss4.jpg)

...准备好了吗？

# 左是右

我认为**左对齐**总是(几乎)最好的选择！当然，我不指望你会相信我的话，所以这是我的逻辑...

## 你是什么类型？

变量是指针还是引用是类型的属性。换句话说，这个跟随是违法的。

```
int aVal = 5;
int *aPtr = aVal + 6;
std::cout << aPtr << std::endl; 
```

Enter fullscreen mode Exit fullscreen mode

我故意使用正确的排列来证明我的观点。你能看出它是如何混淆问题的吗？

> 错误:从“int”到“int*”的转换无效

变量`aPtr`被静态类型化为指向`int`的*指针*。这是变量本身的一个属性，必须像考虑一个`bool`而不是`int`一样考虑。事实上，它是一个指针，是变量数据类型的一部分！

### 对位:多次声明

(感谢 [@bluma](https://dev.to/bluma) 给我指明了这个方向。)

右对齐的支持者指出，下面的情况并不像你预期的那样

```
int* a, b, c; 
```

Enter fullscreen mode Exit fullscreen mode

`a`是指向一个`int`的指针，而`b`和`c`只是整数。为了达到预期的效果，你需要:

```
int *a, *b, *c; 
```

Enter fullscreen mode Exit fullscreen mode

他们说，这是右对齐的一个论据。这是我承认的一点，*然而*，**无论如何，在一行声明多个变量通常被认为是不好的做法**！除此之外，`a`将是未初始化的，你应该习惯性地将指针初始化为`nullptr`(或者至少是`0`)以防止未定义的行为潜入你的代码。

所以，虽然上面的例子是右对齐指针的一个论据，我认为你不应该在你的代码中有这个例子。每行初始化一个变量。你以后会感谢自己的。

## 名字里有什么？

让我们考虑另一个例子:

```
int *aPtr;
std::cout << *aPtr << std::endl; 
```

Enter fullscreen mode Exit fullscreen mode

当然，对你来说，结果可能看起来很明显，但请考虑以下人员:

*   一个还没有吸收 C++精髓的人。
*   凌晨 2 点，体内没有足够的咖啡因。
*   你自己写了四个月的用户文档，这时你的 C++知识已经开始泄露了。

当我们右对齐一个指针时，它使星号看起来像是名字的一部分。是的，那显然是死了一个*错了*的结论才得出的，但是当你累了或者疏于练习的时候，大脑故障比比皆是。我们可以忘记星号既是数据类型*的一部分，也是一元运算符*的一部分。

这一点在与符号(`&` ):
中更加明显

```
int aVal = 5;
int &aRef = aVal;
std::cout << &aRef << std::endl; 
```

Enter fullscreen mode Exit fullscreen mode

会打印出什么呢？不是`5`！它将打印出`aRef`所引用的变量的*地址*。

> 0x76178327ee0c

想象一下，在一个混乱的数学中的某个地方犯了这个错误，在正确的地方用一些类型转换来掩盖编译器的错误。你可能会追你的尾巴几个小时！

另外，考虑一下这个:

```
int &someFunc(int byVal, int *byPtr, int &byRef); 
```

Enter fullscreen mode Exit fullscreen mode

即使你知道这个函数被称为`someFunc`，而不是`&someFunc`，你也可以看到在混乱中视觉上“丢失”返回类型的`&`(或`*`)是多么容易。

通过将指针和引用符号与数据类型对齐，我们可以直观地阐明它在类型中的使用和作为操作符的使用之间的区别。

# 居中对齐呢？

太好了，所以有一个选择。那么，中心对齐呢？

```
int aVal = 5;
int * aPtr = & aVal;
int & aRef = aVal;
std::cout << * aPtr << std::endl;
std::cout << & aPtr << std::endl;
std::cout << aRef << std::endl; 
```

Enter fullscreen mode Exit fullscreen mode

两个问题。首先，也是最不重要的一点，对于许多开发人员来说，这实在是太难看了。

第二，更重要的是，你刚刚给你的大脑制造了一个绊倒的危险。**每次你看到一个`*`或`&`时，你必须解析上下文来确定它是被用作操作符还是数据类型的一部分。**

所以，你*可以*这样做，但是你没有必要为自己和他人创造更多的脑力劳动。更多的脑力劳动意味着更高的出错概率。

# 那么，一个组合怎么样？

风格的第一条规则是一致性！如果你的惯例充满了例外，那么它将会变得更加难以维持。

为了理解我的意思，试着找出这种风格的规则

```
int aVal;
int *aPtr;
int &aRef;
int& someFunc(int byVal, int *byPtr, int & byRef);

passByVal(* aPtr);
passByPtr(& aVal);
passByRef(& aVal); 
```

Enter fullscreen mode Exit fullscreen mode

看起来很可笑？实际上，我可以为每篇文章提供一个简短的逻辑，尽管我不会用它来炫耀你的智慧。关键是，除非我在我的风格指南中对每条规则提供了清晰的解释(假设有人会读它！)，我的代码的新开发人员将不得不在比这大得多的代码库中自己解决所有这些问题！

换句话说，**保持简单**。

# 总结

我现在能听到第二排的一个人说:“这很好，但是我已经用 C 和 C++编程二十多年了。我现在知道区别了。”

我相信你会的。但是编码风格与你无关！**编码风格，像大多数编程惯例一样，是关于其他人的，**包括你未优化的自我。代码的行为应该尽可能的清晰。我相信我已经演示了左对齐指针是如何做到这一点的。

*   在声明中，将`*`或`&`与类型对齐(左)。这澄清了数据类型*是*指针或引用。这条规则不允许有例外。
*   以上也让显而易见的事情变得清晰起来:`*`或`&`绝不是*和*名字的一部分！
*   当`*`或`&`作为操作符时，对齐其操作数(右)，如变量名。
*   附加规则:不要填充一元运算符！这澄清了它们只有一个操作数——连接的操作数。(`i++`一般比`i ++`好)。

(如果你真的真的想填充一元运算符，一定要把它也应用到`&`和`*`上，但是当*被用作运算符时，只有*！)

让我再次演示一下这在实践中是怎样的

```
int aVal;
int* aPtr;
int& aRef;
int& someFunc(int byVal, int* byPtr, int& byRef);

passByVal(*aPtr);
passByVal(*(aPtr+1));
passByPtr(&aVal);
passByRef(&aVal); 
```

Enter fullscreen mode Exit fullscreen mode

我希望我已经为左指针对齐做了清楚的论证！如果我忽略了一些实用的角度，一些比美学更客观的东西，请在下面评论。
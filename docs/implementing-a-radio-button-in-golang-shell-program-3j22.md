# 在 Golang shell 程序中实现“单选按钮”

> 原文：<https://dev.to/adil_w3nomad/implementing-a-radio-button-in-golang-shell-program-3j22>

我在 Gopher slack 频道偶然发现了这个问题，我想我可能有机会回答它。地鼠新手，所以一如既往 C&C 欢迎和赞赏！

[![](img/fc1ea5c50ca23087e2ba563465a41548.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9OzXtwar--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n12ddg2x6krihevcq1lv.png)

@J 所以你要先用一个问题来提示。这方面的代码非常简单；

```
reader := bufio.NewReader(os.Stdin)
fmt.Print("? How would you like to use ESLint??") 
```

现在下一部分有点含糊，因为当你提到单选按钮的行为时，你提供的屏幕截图实际上是一个选择器，你可以使用方向键导航。如果它是一个实际的单选按钮，您可以简单地为每个选项附加一个值，然后根据答案执行您想要的代码。例如

```
? How would you like to use ESLint?
> To check syntax only (1)
> To check syntax and find problems (2)
> To check syntax, find problems, and  enforce code style (3)
$ > 3
! You have selected the syntax only mode 
```

实现这一点也很简单。

```
reader := bufio.NewReader(os.Stdin)
fmt.Print("? How would you like to use ESLint??")
fmt.Print("To check syntax only (1) ")
fmt.Print("To check syntax and find problems (2) ")
fmt.Print("To check syntax, find problems, and enforce code style (3) ")

answer, _ := reader.ReadString('\n') // Allow user input up until and including \n
answer = strings.TrimSuffix(answer, "\n") // Remove trailing \n from string
if answer == "1" {
  // check syntax code execution
} else if answer == "2" {
  // check syntax and find problems code execution
} else if answer == "3" {
  // check syntax find problems and enfore style code code execution
} else {
  fmt.Print("Answer isn't recognised")
  // start again
} 
```

我可能会使用一个 switch 语句来使它变得更加简洁，并且还会将问题打印和答案收集打包到一个函数中，当答案未被识别时将调用该函数。但是话说回来，你可以做很多重构的工作。

如果你想实现选择器的行为，你可以用方向键来导航选项，这将会更复杂(我可以想象)。你得想办法。

1.  打印可以导航的多行
2.  允许用户导航这些行(可能使用与上面相同的方法，但是在按下箭头键时触发导航)
3.  突出显示当前选中的行
4.  按下 enter 键时，根据所选选项运行行为。

就我个人而言，我觉得在 UX 这是一件劳而无获的事情，但在围棋中这是完全可能的，因为只要你用心去做，任何事情都是可能的。
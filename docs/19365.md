# 编码字节:第 4 部分—条件语句

> 原文：<https://dev.to/waqardm/coding-bytes-part-4conditional-statements-3dj0>

# 什么是条件语句？

当一个人想要根据不同的输入/标准执行不同的动作时，可以使用。最简单的例子就是一个是非题。让我们看一个`if`语句的例子。

### 如果

```
 // Is the sun out today?
    let sun = true;

    if(sun) {
        return 'Yes the sun is shining ☀️';
    }

    // Output will be: Yes the sun is shining ☀️

    // Syntax of if statement
    /*
    if(condition) {
        code that will be executed if condition is true
    }
    */ 
```

Enter fullscreen mode Exit fullscreen mode

因此，我们在上面询问太阳是否出来(测试条件)，因为它是真的，花括号之间的代码(参见上面注释中的语法示例)正在执行。如果太阳没有出来会发生什么？

### 目不斜视

```
 // Is the sun out today?
    let sun = false;

    if(sun) {
        return 'Yes the sun is shining ☀️';
    } else {
        return 'No, sorry!'
    }

    // Output will be: No, sorry!

    // Syntax of else statement
    /*
    if(condition) {
        code that will be executed if condition is true
    } else {
        execute this code if the first condition is false
    }
    */ 
```

Enter fullscreen mode Exit fullscreen mode

我们的`else`语句允许回退。如果条件不满足，`else`关键字只是通过提供备份来增加`if`语句。

### 否则/如果

您可能会猜到`else/if`语句是做什么的🤔。

```
 // Is the sun out today?
    let sun = false;
    let rain = false;

    if(sun) {
        return 'Yes the sun is shining ☀️';
    } elseif (rain) {
        return 'No, sorry it is raining today 🌧️';
    } else {
        return 'It is mild'
    }

    // Output will be: It is mild

    // Syntax of esle/if statement
    /*
    if(condition1) {
        code that will be executed if condition 1 is true
    } elseif (condition 2) {
        execute this code if the condition 1 is false and condition 2 is true
    } else {
        execute this code if all of the above are false
    }
    */ 
```

Enter fullscreen mode Exit fullscreen mode

正如我们所见，`else/if`继续给我们额外的选择。语句是自上而下处理的，所以在使用`else/if`时，总要有一个`else`。对于一个`if`语句来说,`else`是不必要的。

### 开关

```
var day = 'thursday';

switch (day) {
    case 'sunday' :
        day = "Sunday";
        break;
    case 'monday':
        day = "Monday";
        break;
    case 'tuesday':
        day = "Tuesday";
        break;
    case 'wednesday':
        day = "Wednesday";
        break;
    case 'thursday' :
       day = "Thursday";
        break;
    case friday:
        day = "Friday";
        break;
    case  'saturday':
        day = "Saturday";

}

// Syntax of switch statement
    /*
    switch(expression) {
    case x:
        code block
        break;
    case y:
        code block
        break;
    case z:
        code block
        break;
    default:
        code block
}
    */ 
```

Enter fullscreen mode Exit fullscreen mode

`switch`语句执行代码，并返回匹配的值。一旦找到匹配，关键字`break`停止执行代码，因为不需要进一步执行。在这个例子中，没有使用默认值，但是如果需要的话可以添加。

### else/if 与 switch 的区别

两者之间有一些较小的差异，可以在稍后阶段讨论。

### 进一步学习

要练习，请为以下场景创建一个条件语句。根据时间的不同，一天有 4 种问候语。

*   在 00.00 和 12.00 之间，问候语将是“早上好”
*   在 12.01 和 17.00 之间，问候语将是“日安”
*   在 17:00 到 20:00 之间，问候语将是“晚上好”
*   在 20.01 和 23.59 之间，问候语将是“晚上好”

你可以使用`console.log()`来输出你的结果，或者尝试 [CodePen](https://codepen.io) 来使用更好的语法高亮。

如果你需要卡住了，给我发微博😃。祝你好运，编码快乐！

* * *

*感谢阅读。为了跟上我的编码之旅，来打个招呼吧👋在 [twitter](https://twitter.com/lawyerscode) 或我们的#devNewbie [Discord](https://discordapp.com/invite/mBsMP2H) 服务器上，我们有一群友好的学习者分享他们的经验。*
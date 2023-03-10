# 在编码面试中询问递归，以识别优秀人才

> 原文：<https://dev.to/codeguppy/ask-about-recursion-during-coding-interviews-to-identify-good-talent-3h3i>

让我们从陈述显而易见的事实开始:优秀的人才是一个非常相对的概念。

对某些项目有益的东西对其他项目可能完全不够。作为应用程序开发人员，开发人员的表现可能令人钦佩，但作为框架或系统开发人员，可能就不那么出色了。与此同时，一些项目可能会从具有特定领域扎实业务知识的资源中受益更多，而其他项目可能需要具有良好编码或算法技能的开发人员。

由你来决定哪些技能对你的项目更有用。

假设你正在寻找一个好的开发者，你想在你的应用程序的核心组件上写代码。

> 你不应该太在意他是否知道某个框架，而是看他是否有良好的编程背景。你可以问他一个关于递归的问题来验证这一点。

我们最近需要选择一些优秀的 JavaScript 开发人员。由于候选人位置偏远，我通过 webex 进行了面试。候选人共用相机和屏幕，所以我可以看到他们打字。

> 这是我问过考生的问题之一:在一个交错的数组中找出最大的数。数组中的每个元素可以是一个数字，也可以是其他数字的数组，级数不定，如下:

```
var ar = [2, 4, 10, [12, 4, [100, 99], 4], [3, 2, 99], 0]; 
```

由于这是一次 JavaScript 面试，候选人使用记事本键入代码，并使用 Chrome 进行测试。

令我惊讶的是，许多看起来不错的候选人在创建一个函数来寻找一个交错数组中的最大值时遇到了问题！！！

即使他们考虑过使用递归，他们也很难将其付诸实践。他们花了将近一个小时试图创建递归函数，但没有成功。

有些人可能会说:这个测试有什么关系？如果你问这种问题，你可能会错过一些人才。

这又回到了我在文章开头的第一个陈述。然而，对于大多数编程工作，我认为了解递归是一项基本技能。你同意我的观点吗？如今，您比以往任何时候都更需要处理分层结构，比如 XML 文档、JSON 对象或控件的层次结构。

虽然我对优秀的开发人员不懂递归不再感到惊讶，但在过去的一次面试中，我从一位候选人那里得到了一个惊喜。当面对这个问题时，他问我:你想用递归还是不用递归来做这个？这就是你想要雇佣的候选人。

现在，只是为了好玩和保持文章简短，我将包括我提到的问题的两个解决方案。第一种解决方案使用递归，而第二种解决方案使用堆栈。事实上，所有这些递归问题也可以通过使用堆栈方法来解决。

## 解决方案一:使用递归找到最大值

```
// Use recursion to find the maximum numeric value in an array of arrays
function findMax1(ar)
{
    var max = -Infinity;

    // Cycle through all the elements of the array
    for(var i = 0; i < ar.length; i++)
    {
        var el = ar[i];

        // If an element is of type array then invoke the same function
        // to find out the maximum element of that subarray
        if ( Array.isArray(el) )
        {
            el = findMax1( el );
        }

        if ( el > max )
        {
            max = el;
        }
    }

    return max;
} 
```

## 解决方案二:使用堆栈查找最大值

```
// Use a stack to find the maximum numeric value in an array of arrays
function findMax2(arElements)
{
    var max = -Infinity;

    // This is the stack on which will put the first array and then 
    // all the other sub-arrays that we find as we traverse an array 
    var arrays = [];

    arrays.push(arElements);

    // Loop as long as are arrays added to the stack for processing
    while(arrays.length > 0)
    {
        // Extract an array from the stack
        ar = arrays.pop();

        // ... and loop through its elements
        for(var i = 0; i < ar.length; i++)
        {
            var el = ar[i];

            // If an element is of type array, we'll add it to stack
            // to be processed later
            if ( Array.isArray(el) )
            {
                arrays.push(el);
                continue;
            }

            if ( el > max )
            {
                max = el;
            }
        }
    }

    return max;
} 
```

可以随意优化上面的代码。你也可以在 [CodeGuppy 编辑器](https://codeguppy.com/code.html?t=find_max)中找到这段代码。
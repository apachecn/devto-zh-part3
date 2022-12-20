# 停止使用 ngOptions

> 原文：<https://dev.to/trezy/stop-using-ngoptions-pa9>

**本文最初发表于 2015 年 9 月 2 日 [Codepen.io](https://codepen.io/trezy/post/stop-using-ngoptions) 。**

糟糕透了。这很难写，更难理解。如果你在看别人写的*表达式？*愿上帝帮助你。幸运的是，在 Angular 中创建`select`元素非常非常容易。试试这个:

```
var selectedState, availableStates;

selectedState = ‘’;
availableStates = [
  {
     name: ‘Alabama’,
     code: ‘AL’
  },

  // ...

  {
    name: ‘Wisconsin’,
    code: ‘WI’
  }
];

<!-- `ng-model` represents the home for our selected value. -->
<select ng-model=”selectedState”>
  <!-- This is the default option -->
  <option value=””>Select a state</option> 
  <!-- The list of states. -->
  <option
    value=”{{ state.code }}”
    ng-selected=”state.code === selectedState”
    ng-repeat=”state in availableStates”>
    {{ state.name }}
  </option> </select> 
```

Enter fullscreen mode Exit fullscreen mode

就 Angular 而言，这是有意义的，对吗？

```
option[value] is what will be set in the model if the option is selected.
option[ng-selected] sets this option as selected if selectedState is set to a state code.
option[ng-repeat] loops through the states to create an option element for each one. 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们看看 ng 选项:

ng-model = " selected state "
ng-options = " state . name as state . code for state in available States ">
<！>-默认选择-
选择一个状态

(≧△≧△≧△≧△≧)

我承认，它很短，但是即使我写了它们，当我回去看它们的时候，我也不知道这些东西是什么意思。好吧，我们还是把它分解一下吧。为了学术目的，对吗？

```
… as … basically says, “Use state.name as a facade for state.code.” That’s kinda cool, as it lets you use an object or an array or a goat or whatever the hell you want for the value and sets the facade as the displayed value. What’s not cool is that you’ll have to debug what the value actually is if things aren’t working the way you’d expect because the value in the markup will be nothing but a single digit. FUUUUUUUUUUUUUU
… in … is your standard looping construct. It’s the same thing as ngRepeat.
thing¹ for thing² may look familiar if you Coffeescript. Here it’s probably the most confusing part for me, though it’s not nearly as bad now that I’m breaking this down. It’s just saying do thing¹ for each thing². 
```

Enter fullscreen mode Exit fullscreen mode

我挣扎了很长时间，试图弄清楚 ngOptions 是如何工作的。然后我挣扎了很长时间，试图找出如何在没有选项的情况下做到这一点，因为这是不可能阅读的。帮自己一个忙，遵循奥卡姆剃刀。
在性能上

一位同事指出，使用 ngOptions 优于 ngRepeat，因为它有性能优势。ngOptions 创建了一个封装了所有选项的单一作用域。ngRepeat 为每个选项元素创建一个新的范围。确实如此。为每个州创建一个新的作用域—导致 50 个新的作用域—肯定会影响性能。但是如果你用的是 Angular，显然你无论如何都不在乎性能。所以。有这个。

:trollface:
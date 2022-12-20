# 也许吧。使用 React 的 Javascript 函数式编程。

> 原文：<https://dev.to/vageez/maybe-functional-programming-in-javascript-4bln>

在我进入这篇文章的主要观点之前，这是为了展示令人惊讶的 Maybe monad。我想涵盖一个高级函数式编程的概念，组成。组合是函数式编程的基础。

```
#Composition
const compose = f => g => x => f(g(x)) 
```

Enter fullscreen mode Exit fullscreen mode

组合允许我们将函数组合在一起。

```
// Add 10%
const addFederalTax = x => x * 1.1
// Add 15%
const addStateTax = x => x * 1.15

// Traditional
// const addTaxes = price => compose(addStateTax)(addFederalTax)(price)

// Point Free
const addTaxes = compose(addStateTax)(addFederalTax)

// Lets add State and Federal taxes to 5 Dollars
addTaxes(5.00) // 6.32 
```

Enter fullscreen mode Exit fullscreen mode

上面使用的另一个很酷的概念是自由风格。

太好了。

我们将使用 Maybe Monad 来遍历一些数据，然后输出可以安全使用的数据。

我对麦当劳的热爱启发我创建了一个数据集，代表一些麦当劳餐厅早餐菜单的一部分。

```
const restaurant = { 
    name: 'McDonalds',
    location: 'Kansas City',
    menu : {
    breakfast : [
            {name: 'Pancakes', options: ['Bacon', 'Sausage']},
            {name: 'McMuffin', options: ['Egg', 'Cheese', 'Sausage', 'Ham', 'Bacon']},
            {name: 'Coffee', sizes: ['Small', 'Medium', 'Large', 'X-Large'], options: ['Milk', 'Creme', 'Sugar']},
        ]
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

当处理不可靠的数据结构时，可能是非常有用的。例如，在我们上面的早餐项目示例中，咖啡是唯一包含尺寸的选项。检查煎饼或松饼的大小会导致运行时错误。更不用说有些地方可能连早餐都不提供！

首先，我们要确保提供早餐。

在我们进入功能之前。让我们来看看命令式的，或者更传统的检查是否提供早餐的方式。

```
function hasBreakfastMenu (restaurant){
    if(restaurant.menu && restaurant.menu.breakfast){
        return restaurant.menu.breakfast
    } else {
        // Do Something
        console.log('Not found')
    }
}

const breakfastMenuItems = hasBreakfastMenu(restaurant) 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们将在函数式风格中做同样的事情。

为此，我们将使用 Pratica 库中的 get。Pratica get 函数返回一个单子。单子是安全的，可以防止运行时错误。

```
// breakfastMenu.js
import { Maybe, get } from 'pratica'

const hasBreakfastMenu = get(['menu', 'breakfast'])

hasBreakfastMenu(restaurant).cata({
    Just: breakfastMenuItems => breakfastMenuItems,
    Nothing: () => console.log('Not found'),
}) 
```

Enter fullscreen mode Exit fullscreen mode

太好了。很简单？对吗？

查看下面的代码。

```
// breakfastMenu.js
import { Maybe, get } from 'pratica'

const hasBreakfastMenu = get(['menu', 'breakfast'])

/**
 *  hasSizes & hasOptions return us a Monad. 
 *  In this exampe we will see how Moands can be implemented in our UI.
 *  Using Monads will free us from using if / else statements in our UI Components.
 * */

const hasSizes = sizes => Maybe(sizes).chain(sizes => get(['sizes'])(sizes)) // Returns a Monad
const hasOptions = options => Maybe(options).chain(options => get(['options'])(options)) // Returns a Monad

const safeBreakfastMenuItems = breakfastMenuItems => breakfastMenuItems.map(
    items => items.map(item => ({
            ...item,
            sizes: hasSizes(item), // Returns a Monad
            options: hasOptions(item) // Returns a Monad
        })
    )
)
// Entry point
export const breakfastMenuItems = compose(safeBreakfastMenuItems)(hasBreakfastMenu) 
```

Enter fullscreen mode Exit fullscreen mode

让我们把它分成 3 个部分。

首先，我们来关注一下`export const breakfastMenuItems`。这是我们的入口点函数，它实现了一个 compose 和一些简洁的自由点语法。我们正在编写两个函数，它们返回给我们一个可以在 UI 组件中使用的安全数据集。如你所见，没有 if 或 else，没有可变性，也没有变量赋值。

其次`hasBreakfastMenu`使用`get`来检查`menu.breakfast`的存在。Get 返回给我们一个 Maybe 单子。如果没有找到菜单或早餐，结果将是`Maybe.Nothing`。其余的代码执行将不会触发。

最后，`safeBreakfastMenuItems`这段代码的目的是检查两个字段`sizes`和`options`，它们可能为空或未定义。我们将字段包装在一个 Maybe 中，这样我们可以安全地检查结果，而不会有任何意外的副作用。

现在，我将展示如何在 React UI 组件中使用上述代码的输出。

```
import { React } from 'react'
import Options from './Options'
import Sizes from './Sizes'
import { breakfastMenuItems } from './breakfastMenu'
import restaurant from './restaurant' // Restaurant object data found above.

 /**
 *  This is not necessarily how I would go about calling this function...
 *  It probably belongs in a reducer. But I think it is important to show how we get the result.
 * */

const breakfastMenu = breakfastMenuItems(restaurant)

const MenuItem = ({ item }) => 
<div>
    <h1>item.name</h1> 
    // Here we avoid using `if else`, instead we unwrap our Monad
    {item.options.cata({
        Just: options => <Options options={optons}/>,
        Nothing: () => false
    })}

    // Here we avoid using `if else`, instead we unwrap our Monad
    {item.sizes.cata({
        Just: sizes => <Sizes sizes={sizes}/>,
        Nothing: () => false
    })}

</div> 
const MenuItems = ({ breakfastMenu }) => breakfastMenu.cata({
    Just : items =>  items.map(item => <MenuItem item={item}/>),
    Nothing : () => <div>No breakfast menu offered</div>, })

const App = () => <div> <MenuItems breakfastMenu={breakfastMenu} /> </div> 
```

Enter fullscreen mode Exit fullscreen mode

所以，我想在这里传递一些我的观点。

1.  作文
2.  没有使用 if / else。
3.  没有强制性代码。
4.  单子单子单子单子。

检查一下 [Pratica](https://github.com/rametta/pratica) ！很整洁！
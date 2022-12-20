# Javascript 中的闭包

> 原文：<https://dev.to/damcosset/closures-in-javascript-5feo>

## 简介

你可能听说过闭包。即使你不完全知道它们是什么，你肯定已经在使用它们了。闭包需要你知道:

# 三个事实:

#### 事实一:在 Javascript 中，可以引用当前函数之外定义的变量。

```
function makeCookies(){
    const secretIngredient = "coconut oil"
    function bake(chocolate){
        return secretIngredient + " and " + chocolate
    }

    return bake("white chocolate")
}

makeCookies() // coconut oil and white chocolate 
```

在这里，我们可以看到内部函数 *bake* 可以访问变量 *secretIngredient* ，尽管它是在外部函数 *makeCookies* 中定义的。

#### 事实二:函数甚至可以引用定义在外层函数中的变量**在**那些外层函数返回之后！

因为函数是一级对象，所以可以将函数存储在变量中，以后再调用它们。我在一篇[https://dev . to/damcosset/higher-order-functions-in-JavaScript-4j8b](https://dev.toprevious%20article)
中讲过高阶函数

```
function cookiesMaker(){
    const secretIngredient = "coconut oil"
    function bake(chocolate){
        return secretIngredient + " and " + chocolate + " chocolate."
    }

    return bake
}

const func = cookiesMaker() // Storing the function in a variable 
```

这里， *cookiesMaker* 被调用，我们将该函数的结果存储在一个变量中。如果你现在打印出*函数*变量，你会看到*烘焙*函数。

*bake* 函数使用了一个在 *bake* 函数( *secretIngredient* )之外声明的变量。即使 *cookiesMaker* 已经返回，函数 *bake* 仍然能够**记住那个变量。** 

```
func("black") // coconut oil and black chocolate.
func("white") // coconut oil and white chocolate. 
```

这怎么可能？嗯，在 Javascript 中，函数值不仅仅存储被调用时需要执行的代码。它们还存储需要执行的变量的引用。像 *bake* 函数这样引用其包含范围中声明的变量的函数被称为**闭包**。

这里的 *bake* 函数跟踪在其包含范围内声明的两个变量: *secretIngredient* 和 *chocolate* 。

当我们后来调用 *bake* 时，它仍然记得这两个变量，因为它们存储在闭包里。

闭包可以引用其范围内的任何变量或参数。看看这个:

```
 function cookiesBaker(cook){
    return function addSecretIngredient(secretIngredient){
        return function bakeCookie(chocolate){
            return `${cook} cooked a ${secretIngredient}  ${chocolate} chocolate cookie.`
        }
    }
} 
```

在这个例子中，内部函数 *bakeCookie* 引用了来自外部 *cookiesBaker* 函数( *cook* )的一个参数、来自外部 *addSecretIngredient* 函数( *secretIngredient* )的一个参数以及来自其自身作用域( *chocolate* )的一个参数。

```
const cook = cookiesBaker("Damien")

const secret = cook("peanut butter")

const result = secret("white")
// Damien cooked a peanut butter white chocolate cookie. 
```

在这里，我们多走了一步。

我们返回内部函数 *addSecretIngredient* 并将其存储在一个变量中。然后，我们调用这个存储函数，结果( *bakeCookie* )存储在另一个变量中。最后，我们调用那个函数。如您所见，最终结果记住了存储在闭包内的所有变量。

我们还可以用这个来制作更多的通用函数。

假设我们想为 Johnny 烤的所有饼干创建一个函数:

```
const bakedByJohnny = cookiesBaker("Johnny")

bakedByJohnny("coconut oil")("black") // Johnny cooked a coconut oil black chocolate cookie.

bakedByJohnny("")("milk") // Johnny cooked a  milk chocolate cookie. 
```

请注意，不是声明一个变量并将中间函数存储在其中。我可以立即调用内部函数因为 *bakedByJohnny("椰子油")*正在返回一个函数！

好，再举个小例子。让我们为莎拉用花生酱烤的所有饼干创建一个函数:

```
const bakedBySarahPeanutButter = cookiesBaker("Sarah")("peanut butter")

bakedBySarahPeanutButter("white")
//Sarah cooked a peanut butter white chocolate cookie.

bakedBySarahPeanutButter("black")
// Sarah cooked a peanut butter black chocolate cookie.

bakedBySarahPeanutButter("milk")
// Sarah cooked a peanut butter milk chocolate cookie. 
```

尽管我们创建的两个函数来自同一个函数定义，但它们是两个不同的对象，并且都存储不同的变量。

*注*:函数可以匿名，比如:

```
let cookiesBaker = function(cook){
    return function(secretIngredient){
        return function(chocolate){
            return `${cook} cooked a ${secretIngredient}  ${chocolate} chocolate cookie.`
        }
    } 
```

这段代码将给出与以前完全相同的结果！

#### 事实三:闭包不仅能记住其包含范围内的变量，还能**更新**它。

考虑下面的例子:

```
const secretIngredient = function(){
    let ingredient = undefined
    return {
        changeIngredient: newIngredient => { ingredient = newIngredient },
        showIngredient: () => ingredient,
        type: () => typeof ingredient
    }
} 
```

这个函数返回 3 个闭包。返回的对象中的每个方法都引用包含范围中定义的变量。

现在，让我们证明闭包不仅可以读取外部变量，还可以更新它们:

```
let i = secretIngredient()

i.showIngredient() // undefined
i.type() // undefined

i.changeIngredient("coconut oil")

i.showIngredient() // coconut oil
i.type() // string 
```

Tadaaaaa！

# 结论

闭包是您最可能经常使用的东西之一。你可能根本不知道这件事！检查您的代码，尝试识别闭包，熟悉它们，并充分利用它们的能力！
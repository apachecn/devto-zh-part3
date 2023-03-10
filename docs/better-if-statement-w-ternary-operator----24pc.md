# 使用三元运算符的 if 语句更好？：💻

> 原文：<https://dev.to/thomasaudo/better-if-statement-w-ternary-operator----24pc>

if 运算符是计算机编程的中流砥柱，我们在任何地方都使用它。

有时我们需要测试一个值，然后执行一些简单的代码:

```
 if (a === b) {
    console.log('true');
} else {
    console.log('false');
} 
```

这个语法用起来确实**清晰**和**简单**，但是它占用了很多地方。

在大型节目中，它可以降低整体的**可见度**和**理解力**。

对于这种简单的情况，使用**三元运算符**通常更简单。

## 语法

```
(condition) ? (operation A) : (operation B) 
```

这个操作符非常有用，但是经常被程序员**未知**或**误解**。

**详情** :
-条件，条件
-操作 A，条件为真时执行的动作
-操作 B，条件为假时执行的动作，具体为 else

## 举例

下面是一个简单的 javascript 例子:

```
// If the age >= 18, then the adult variable takes the value true, otherwise, it's taking the value 
//false

age >= 18 ? adult = true : adult = false

// With a basic if statement

if (age >= 18 ) {
    adult = true
} else {
    adult = false
} 
```

**方法更简单不是吗？**

另一个例子:一个返回 a 和 b 的**GCD**
的函数，然而这根本不是最好的例子。

的确，阅读起来并不容易，一个**普通的 if 语句**在这种情况下会**更好**。

```
function gcd(a, b) {

    return ( (a%b) === 0 ?  b : gcd(b, a%b) )

} 
```

## 结论

总之，这个符号真的**有帮助**，帮助我们**提高**我们**的生产力**。然而，它必须用在真正简单的情况下。

对于复杂的语句或操作，if / else 符号仍然是最好的方式！

感谢你阅读我的文章😇
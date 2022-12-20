# 理解 js 中的迭代器

> 原文：<https://dev.to/machy44/understand-iterators-in-js-5f1j>

迭代器工作是因为 javascript 中的闭包。迭代器可以写成函数内部的函数，这在下一个代码示例中可以看到。

```
function createFunction(array) {
    let i = 0;
    function inner(){
        const element = array[i];
        i++;
        return element;
    }

    return inner;
}

const returnNextElement = createFunction([4,5,6])
const element1 = returnNextElement()
const element2 = returnNextElement() 
```

但是我们知道我们需要使用*。next()* 属性从数组中获取下一个元素。我们可以通过内部函数变成方法的方式来实现。

```
function createFlow(array) {
    let i = 0;
    const inner = {
    next: function(){
            const element = array[i];
            i++;
            return element;
        }
    }
    return inner;
}

const returnNextElement = createFlow([4,5,6]);
const element1 = returnNextElement.next(); // 4
const element2 = returnNextElement.next(); // 5 
```

又短又甜:)
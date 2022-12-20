# JavaScript:。查找()

> 原文：<https://dev.to/terrancecorley/javascript-find-4og1>

如果您需要搜索数组并在数据集中找到唯一的项，那么您应该考虑使用。find()方法，原因如下。

## 例子

### [T1。查找()](#find)

```
 let people = ['sally', 'tom', 'brent', 'james'];  

    let personIWant = people.find((person) => {
        return person === 'brent';
    });
    console.log(personIWant); // 'brent' 
```

的。find()方法搜索我们的数组，寻找与我们的条件匹配的项目的第一个实例，在本例中，我们告诉 JavaScript 搜索`'brent'`。如果在数组中找到匹配项，将返回该匹配项，循环将中断。

### [T1。forEach()](#foreach)

```
 let people = ['sally', 'tom', 'brent', 'james'];

    let personIWant;
    people.forEach((person) => {
        if (person === 'brent') {
            personIWant = person;
        }
    }); 
```

我们可以达到和。通过使用. forEach()方法来查找()方法，但是一旦我们找到了我们正在寻找的项目，就没有直接的方法来打破这种类型的循环。顾名思义，forEach 循环意味着遍历数组中的每一项，所以即使循环找到了我们想要的值(' brent ')，我们仍然继续对数组中的每一项执行循环。

### 传统为循环

```
 let people = ['sally', 'tom', 'brent', 'james'];

    let personIWant;
    for (let i = 0; i < people.length; i++) {
        if (people[i] === 'brent') {
            personIWant = 'brent';
            break;
        }
    }; 
```

另一种类型的循环，它可以具有与。find()方法是传统的 for 循环。然而，您必须添加您自己的 break case，这样做的代码有点长。

### 外卖

## 如果你在一个数组中寻找一个唯一的项，或者只是看看该项是否存在。find()方法可能就是您正在寻找的。

感谢你今天花时间阅读我的帖子，如果你已经做到了这一步！编码快乐！*原发于[https://terrance corley . com/blog/2019-04-17-JavaScript-find-method/](https://terrancecorley.com/blog/2019-04-17-javascript-find-method/)*。
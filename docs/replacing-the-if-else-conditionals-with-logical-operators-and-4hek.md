# 用逻辑运算符&&和||

> 原文：<https://dev.to/akhil_001/replacing-the-if-else-conditionals-with-logical-operators-and-4hek>

*(如果不熟悉 javascript 中短路评估的概念，请通过[本帖](https://dev.to/akhil_001/short-circuit-evaluation-in-javascript-3o5a) )*

好的。所以如果你还在这里，那么我相信你对短路评估概念很熟悉。我们开始吧！

*If-else* 条件通常用于检查真值和假值，并相应地执行逻辑，如下例所示

```
var flag = true;

var printTrue = function()
{
//do something here
console.log('true');
}

if(flag)
{
printTrue();
}
// -> true 
```

让我们用& &操作符替换 *if* conditional，它打印出与上面的
相同的输出

```
flag && printTrue()
// -> true 
```

现在让我们看看如何使用逻辑 or ||运算符

```
var flag = false;
var printFalse = function()
{
//do something here
console.log('false');
}
if(!flag)
printFalse();
//-> false 
//or we can gain the same output using || operator
flag || printFalse();
//-> false 
```

类似概念可以用于

*   使用逻辑 or ||运算符回退到默认值

```
function Car(name)
{
   var _defaultName = "Tesla";
   //if name is not passed, then fallback to default name
   name = name || _defaultName;
   this.getName = function()
   {
    console.log('The name of car is ',name);
   }
}

var car1 = new Car('Beetle');
var car2 = new Car();// no name is passed is here and hence name is undefined

car1.getName();
// The name of car is Beetle
car2.getName();
// The name of car is Tesla 
```

*   检查对象是否被实例化，然后使用逻辑 and &&运算符访问它的方法，如下所示

```
var car3 = new Car('Audi');
car3 && car3.getName();
//The name of car is Audi 
var car4;
car4.getName();//prints Uncaught TypeError: Cannot read property 'getName' of undefined 
//since car4 is not yet instantiated.
//To avoid such type of error the following line can be used
car4 && car4.getName();//checks if car4 is instantiated 
//undefined

/* similarly this can be used for following usecase  */
function Car(name){
var _defaultName = 'Audi';
name = name || _defaultName;
var capitalizeTheName = function(name)
{
return name.toUpperCase();
}
var modifiedName = name && capitalizeTheName(name);
this.getName = function()
{
console.log('The modified name is '+modifiedName);
}
}
var car5 = new Car('Ferrari');
car5.getName();
//The modified name is FERRARI 
```

还有许多其他用例，我们可以使用类似的概念来实现它们

希望这篇文章对你有用。编码快乐！

欢迎提问、建议和讨论

### 学分

封面图片:[克里斯托弗·罗宾·艾宾浩斯的照片](https://unsplash.com/photos/pgSkeh0yl8o)
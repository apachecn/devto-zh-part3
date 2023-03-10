# 如何调试 Javascript 代码

> 原文：<https://dev.to/ankurrai2602/how-to-debug-javascript-code-3fp4>

**Javascript 代码调试技巧。**

**方法 1** :放置断点停止执行控制

使用“debugger”语句创建断点，并使用 F12 键在 chrome 浏览器上打开控制台窗口；

例子

function addIt(x，y){
调试器；
返回 x+y；
}

**方法二**:打开 chrome 浏览器的控制台窗口(可以使用快捷键 F12)

点击 source 选项卡，然后按 Ctrl + P 快捷键
输入您想要调试的脚本文件名。
打开文件后，您可以放置断点，它将一直存在，直到您删除它或在下次运行时禁用它。

**方法三**:边调试边修改 javascript 代码。只需在执行控制到达断点处时设置断点，编辑代码并按 ctrl+s。
你会发现一旦你按 ctrl+s 保存了代码块，它的执行就会重新开始

**方法四**:使用 console.log 语句检查变量值和调试目的。
console.log("********var1 = "，var1，" var2 "，var 2)；

**方法五**:使用 console.time()和 console.timeEnd()获取一段脚本的执行时间。

//示例
函数 my fun(){
console . time()；

```
//your function code here...
//...........

console.timeEnd(); 
```

}
如果使用 multipal console.time()，也可以给时间函数一个标签

//示例
函数 my fun(){
console . time(' myfunbl1 ')；

```
//your function code here...
//...........

//after some line you use
 console.time('myFunLbl2');
//.........
 console.time('myFunLbl2');
//.......
console.timeEnd('myFunLbl1'); 
```

}
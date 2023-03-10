# Dart 中的变量与动态

> 原文：<https://dev.to/nitishk72/var-vs-dynamic-in-dart-29al>

#### 在 Dart 和 JavaScript 中，变量声明都有 var 关键字，但两者并不相同。

Dart 中有变量声明的动态关键字，相当于 JavaScript 的 var 关键字。

Dart var 关键字非常类似于 javascript var 关键字，但并不完全相同。大多数 Dart 开发人员使用 Dart 变量作为 JS 变量，这是不正确的。让我们通过一些例子来理解这一点。

我将给出 4 个不同的例子。

# 例 1

在 JavaScript 和 Dart 中，我们可以在使用 var 关键字定义的变量中存储任何类型的数据。

### 镖

```
// This is valid Example this will work
void main() {
     var x = 'Maths pi';
     var y = 3.14;

     print(x); // Maths pi
     print(y); // 3.14
} 
```

Enter fullscreen mode Exit fullscreen mode

### JavaScript

```
// This is valid Example this will work
 var x = 'Maths pi';
 var y = 3.14;

 console.log(x); // Maths pi
 console.log(y); // 3.14 
```

Enter fullscreen mode Exit fullscreen mode

# 例 2

在 JavaScript 和 Dart 中，我们可以在同一个变量中存储不同种类的数据。

### 镖

```
// This is valid Example this will work
void main() {
    var x ;
    x = 'Math pi';
    print(x); // Maths pi

    x = 3.14;
    print(x); // 3.14
} 
```

Enter fullscreen mode Exit fullscreen mode

### JavaScript

```
// This is valid Example this will work
 var x ;
 x = 'Math pi';
 console.log(x); // Maths pi

 x = 3.14;
 console.log(x); // 3.14 
```

Enter fullscreen mode Exit fullscreen mode

# 例 3

现在我将混合示例 1 和示例 2
现在我将初始化变量，并尝试在同一个变量中存储不同的数据类型。这会给我带来一些错误，因为这在 Dart 中无效，但在 JavaScript 中有效。

###### 这就是`var`在镖中表现与众不同的地方

### 镖

```
// This is invalid Example this will work
void main() {

     var x = 'Math pi';
     print(x);  // Maths pi

     x = 3.14;  // Compilation failed error because
                // you are trying to assign double to String variable.
     print(x);
} 
```

Enter fullscreen mode Exit fullscreen mode

### JavaScript

```
// This is valid Example this will work
 var x ;
 x = 'Math pi';
 console.log(x); // Maths pi
 x = 3.14;
 console.log(x); // 3.14 
```

Enter fullscreen mode Exit fullscreen mode

# 例 4

如果我们在 Dart 代码中使用 dynamic 而不是 var，我们就不会出现错误，因为 dart dynamic 与 JavaScript var 相同。

### 镖

```
// This is invalid Example this will not work
void main() {
     var x = 'Math pi';
     print(x); // Maths pi
     x = 3.14; 
     print(x); // expected 3.14
} 
```

Enter fullscreen mode Exit fullscreen mode

### JavaScript

```
// This is valid Example this will work
 var x ;
 x = 'Math pi';
 console.log(x); // Maths pi
 x = 3.14;
 console.log(x); // 3.14 
```

Enter fullscreen mode Exit fullscreen mode

# 这是我在这里的第一篇帖子。如果你喜欢它，让我，这样我可以在这里张贴的动机
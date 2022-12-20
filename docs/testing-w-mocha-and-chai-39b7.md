# 测试 w/ Mocha 和 Chai

> 原文：<https://dev.to/mcarpenter/testing-w-mocha-and-chai-39b7>

## 摩卡是什么？

Mocha 是一个运行在 Node.js 和浏览器中的 Javascript 测试框架。这里的目标是通过编写您的第一个测试，让您对 Mocha 的工作原理有一个基本的了解。在这个例子中，我们将创建一个函数，它接受两个参数(数字)并返回这两个参数的和。

### 你提到的柴~~拿铁~~？

是的，Chai 是一个用于节点和浏览器的 BDD/TDD 断言库。Chai 可以与任何 Javascript 测试框架配对。Chai 代码使编写测试更具可读性，更易于编写。柴也有伟大的[文献](https://www.chaijs.com/api/bdd/)。

### 向前冲！

好的，首先我们需要安装摩卡和柴。然后创建一个`test.js`文件，这是我们编写测试的地方。

`npm install -g mocha chai`

接下来，我们将在我们的`test.js`文件中需要 Chai。

```
const expect = require('chai').expect; 
```

Enter fullscreen mode Exit fullscreen mode

### 我们到底在测试什么？

我们正在为某个程序创建一个函数，它接受两个数字，并返回这两个数字的和。如果传递的数字是字符串，会发生什么？还是一组数字？如果是负值呢？我们根据这个标准创建案例，然后构建我们的函数来通过测试。

### 阳性测试

我们首先调用用于分组的函数`describe()`。Describe 接受一个字符串和一个回调。在描述中，我们称之为`it()`,这是我们的测试用例。请参见下面的代码示例。

```
describe('add', function() {
  it('should return the sum of two positive numbers', function() {
    expect(add( 2, 4 )).to.equal(6);
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

多亏了柴，现在上面的代码应该是自然的和易于阅读的。我正在描述“添加”功能。它应该返回两个正数的和。我期望参数 2 和 4 等于 6。这是一个阳性测试的例子。

### 阴性测试

接下来，让我们添加一个负面测试用例。

```
describe('add', function() {
  it('should return the sum of two positive numbers', function() {
    expect(add( 2, 4 )).to.equal(6);
  });

  it('should return NaN if passed a word string', function() {
    expect(add( "hello", "pal" )).to.be.NaN;
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 阵列测试

让我们再做一个，如果传递的数字在一个数组里面呢？我们应该如何处理这个问题？我们应该抛出一个错误还是把数字相加？让我们选择对数字求和并返回结果。

```
describe('add', function() {
  it('should return the sum of two positive numbers', function() {
    expect(add( 2, 4 )).to.equal(6);
  });

  it('should return NaN if passed a word string', function() {
    expect(add( "hello", "pal" )).to.be.NaN;
  });

  it('should return the sum of an array of numbers', function() {
    expect(add([1,2,3,4,5])).to.equal(15);
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 建立我们的函数

最后，我们需要构建我们的函数来通过下面的测试。然后我们可以运行我们的`test.js`文件，看看它们是否通过。

```
const add = function (num1, num2) {

  if ( Array.isArray(num1) ){
    let sum = 0;
    for ( let i = 0; i < num1.length; i++){
      sum += num1[i];
    }
    return sum;
  }
  else if ( isNaN(num1, num2) ){
    return NaN
  }

  return parseFloat(num1) + parseFloat(num2);

}

module.exports = add; 
```

Enter fullscreen mode Exit fullscreen mode

### Test.js

我们将导出我们的函数，并在`test.js`中需要它。

```
const expect = require('chai').expect;
const add = require('./add.js');

describe('add', function() {
  it('should return the sum of two positive numbers', function() {
    expect(add(2, 4)).to.equal(6);
  });

  it('should return the sum of an array of numbers', function() {
    expect(add([1,2,3,4,5])).to.equal(15);
  });

  it('should return NaN if passed a string', function() {
    expect(add('hello', 'pal')).to.be.NaN;
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 让我们运行我们的 test.js

在命令行中，您需要键入`mocha test.js`来对我们的函数运行下面的测试，看看它们是否通过。如果操作正确，您应该会看到三个绿色的复选标记，表示有三个通过了测试。

```
 add
    ✓ should return the sum of two positive numbers
    ✓ should return the sum of an array of numbers
    ✓ should return NaN if passed a string

  4 passing (9ms)
  1 pending 
```

Enter fullscreen mode Exit fullscreen mode

# 结论

嘿，你来了！以上是使用摩卡和柴的 TDD/BDD 的基础。查看以下链接，了解更多关于摩卡/茶的信息。

[摩卡](https://mochajs.org/)
T3】柴
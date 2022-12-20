# 用 test.each 简化重复的 Jest 测试用例

> 原文：<https://dev.to/bgord/simplify-repetitive-jest-test-cases-with-test-each-310m>

### 问题

有时，我会遇到这样的情况，我的大多数测试用例都遵循相似的步骤序列。这种情况最常发生在单元测试助手/实用程序函数的时候。给定某些参数，检查实际结果是否等于预期结果。一遍又一遍。随着案例数量的增长，测试套件会变得臃肿。

前面人为的例子:

```
const add = (a, b) => a + b;

describe("'add' utility", () => {
  it("given 2 and 2 as arguments, returns 4", () => {
    const result = add(2, 2);
    expect(result).toEqual(4);
  });
  it("given -2 and -2 as arguments, returns -4", () => {
    const result = add(-2, -2);
    expect(result).toEqual(-4);
  });
  it("given 2 and -2 as arguments, returns 0", () => {
    const result = add(2, -2);
    expect(result).toEqual(0);
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 解

我想了一个抽象来避免这种样板文件，在谷歌搜索了几次后，我找到了 test.each Jest 实用程序。

这个助手鼓励您创建数组`cases`，在其中存储参数和预期结果，然后遍历整个数组来运行测试函数并断言结果。

以`test.each` :
为例

```
const add = (a, b) => a + b;

const cases = [[2, 2, 4], [-2, -2, -4], [2, -2, 0]];

describe("'add' utility", () => {
  test.each(cases)(
    "given %p and %p as arguments, returns %p",
    (firstArg, secondArg, expectedResult) => {
      const result = add(firstArg, secondArg);
      expect(result).toEqual(expectedResult);
    }
  );
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 备注

**好处**:

*   更容易添加新的测试用例
*   较少样板文件

**可能的缺点**:

*   更多的抽象，有些人可能会觉得没有必要

我发现写一个关于`cases`数组的注释来增加可读性和减少脑力劳动是值得的。

```
 // first argument, second argument, expected result
  const cases = [[2, 2, 4], [-2, -2, -4], [2, -2, 0]]; 
```

Enter fullscreen mode Exit fullscreen mode
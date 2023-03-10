# TypeScript 中的方法重载

> 原文：<https://dev.to/tomekbuszewski/method-overloading-in-typescript-25pm>

前段时间，在构建 API 时，我认为根据函数参数堆叠`if`语句会导致复杂的业务逻辑。我发现的解决方案之一是“方法重载”，主要用于 Java(和 C#)。

简而言之，重载方法与众不同，因为它似乎被定义了多次，每一次都有不同的签名。看看这个用 Java 实现的超级简单的:

```
class Car {
  public static String drive(int speed) {
    return "Car is driving with " + speed + " km/h.";
  }

  public static int[] drive(int init, int target) {
    return IntStream.rangeClosed(init, target).toArray();
  }
} 
```

有两种方法被命名为`drive`。一个接受一个参数，另一个接受两个参数。调用这些方法很简单——根据参数编号，执行匹配的方法。

我试图在 TypeScript 中实现[类似的结果。简而言之——**我失败了**。](https://codepen.io/tomekbuszewski/pen/BvwNWG?editors=0010)

我的主要问题是 TypeScript 本身的性质。作为 JavaScript 超集，它不能彻底改变语言功能，只是增强了它们。声明两个同名的类方法本身并不是错误，但是它会导致最后一个方法被实际计算。

我们能做的只有一件事。重载方法声明:

```
class Car {
  public static drive(speed: number): string;
  public static drive(speed: number, target: number): number[];
  public static drive(speed: number, target?: number): string | number[] {
    if (target) {
      const arr: number[] = [];

      for (let i = speed; i <= target; i++) {
        arr.push(i);
      }

      return arr;
    }

    return `Car is driving with ${speed} km/h.`;
  }
} 
```

虽然这肯定有效，甚至看起来很好，但它并没有给我们带来多少。当然，其他人会立刻发现这个方法有两个可能的调用。但是，如果没有超载，会有什么不同吗？删除这两行仍然保留了这样的信息:方法需要一个参数(数字)，有可选的第二个参数(数字)，并返回字符串或数字数组。**唯一的实际增益是输出差异**。但是只要瞥一眼这个方法的主体，它就会暴露出来。如果没有，考虑简化你的代码。

在我看来，虽然这种技术在 Java 中很强大，但在 TypeScript 中却失败了。重载的最大优势在于有可能减少条件逻辑以支持分离的声明。在 TypeScript 中，我们所能做的就是写一个不同的

—

我希望的一件事是 IDE 支持。不幸的是，我唯一的 IntelliJ 并不在乎:

[![IntelliJ and overloads](img/075ebea220f8ac286dc60c9dd332a2e3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7rhmT4ZU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pictr.cimg/2018/12/31/0WcFFX.png)

VS 代码也没有:

[![VS Code and overloads](img/410fd55ecc3f3fca51676b421927bde8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gfiFwon---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pictr.cimg/2018/12/31/0Wcq91.png)

—

*   [Java 实现](https://repl.it/@tomekbuszewski/Method-overloading)
*   [打字稿实现](https://codepen.io/tomekbuszewski/pen/BvwNWG?editors=0010)
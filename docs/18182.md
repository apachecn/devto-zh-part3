# 带闭包的 TypeScript/JavaScript 类

> 原文：<https://dev.to/akashkava/typescriptjavascript-class-with-closure-2fj>

类不过是函数，类的创建可以以一种有趣的方式绑定到闭包，而 build I 需要用 class 附加一个闭包。令我惊讶的是，它确实工作正常。

```
 function ClassWithClosure(parent) {
   return class ClassWith {

       constructor(x) {
          this.x = x;
       }

       compute() {
          return this.x + parent;
       }
   };
}

var ClassWith2 = ClassWithClosure(2);
var ClassWith3 = ClassWithClosure(3);

var cw2 = new ClassWith2(2);
var cw3 = new ClassWith3(2);

cw2.compute(); // 4
cw3.compute(); // 5 
```

Enter fullscreen mode Exit fullscreen mode

基本上它的工作原理是`class`仅仅是一个函数构造器，它是一个可以轻松容纳任何闭包的函数。

```
function ClassWithClosure(parent) {

   function ClassWith(x) {
      this.x = x;
   }

   ClassWith.prototype.compute = function() {
      return this.x + parent;
   }

   return ClassWith;

} 
```

Enter fullscreen mode Exit fullscreen mode

> 注意，这与静态不同，静态变量对于每个新实例都是常量。

当您想要创建像 Java 这样没有父类就无法存在的嵌套类时，这特别有用。

```
class Parent {

    get childClass(): BaseClass {
        const parent = this;

        // wow nested class !!
        return class Child extends BaseClass {
            get parent(): Parent {
                return parent;
            }
        } 
    }

}

const p1 = new Parent();
const p2 = new Parent();

const c1 = new p1.childClass(); // of type BaseClass
const c2 = new p1.childClass(); // of type BaseClass

c1 === c2 // false;
c1.parent === c2.parent// true 
```

Enter fullscreen mode Exit fullscreen mode

在这里，您不能创建没有父级的`childClass`。
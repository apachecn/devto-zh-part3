# Java 继承难题

> 原文：<https://dev.to/devit951/java-inheritance-puzzle-446j>

假设我们有一个接口和这些类:

```
interface SuperType {
    void test();
}

class DefaultSuperType implements SuperType{
    @Override
    void test(){
        System.out.println("0")
    }
}

class ChildOfSuperType extends DefaultSuperType{
    @Override
    void test(){
        System.out.println("1")
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后我们执行这段代码

```
SuperType superType = new ChildOfSuperType();
DefaultSuperType defaultSuperType = (DefaultSuperType) superType;
defaultSuperType.test() 
```

Enter fullscreen mode Exit fullscreen mode

你知道会印什么吗？
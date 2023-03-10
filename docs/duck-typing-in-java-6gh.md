# java 中的鸭子打字

> 原文：<https://dev.to/skuzzle/duck-typing-in-java-6gh>

声明:请不要把这看得太严重。只是好玩和游戏。

*Duck typing* 是一个习语，主要在动态类型语言中为人所知。它声明您可以将 X 类型的不相关对象视为 Y 类型的对象，只要两者具有相同的公共接口。

> 如果它看起来像鸭子，行动像鸭子，发出像鸭子的声音，那么它一定是一只鸭子！

另一方面，Java 的静态类型系统对类型兼容性施加了严格的规则。仅仅拥有相同的方法并不能证明两个类是*“类型兼容的”*。考虑这个例子:

```
public class Duck {
    public String makeNoise() {
        return "Quak Quak";
    }
}

public class Dog {
    public String makeNoise() {
        return "woof woof";
    }
} 
```

两个类都有相同的接口，那么我们可以将实例分配给各自的另一个类型吗？

```
final Duck duck = new Dog(); // fails
assertEquals("woof woof", duck.makeNoise()); 
```

我们当然不能。Java 的类型系统需要使用继承的兼容类的显式定义。

现在，让我们通过对代码进行最简单的修改来让我们的例子工作起来:

```
public class Dog extends Duck {
    @Override
    public String makeNoise() {
        return "woof woof";
    }
} 
```

除了你在生物课上学到的一切，我们的`Dog`现在*是一只*鸭子，因此可以赋给类型为`Duck`的变量。这个改动应该已经让上面的测试变绿了。

好了，现在我们的狗是一只鸭子，但是我们代码库中所有其他也有`makeNoise`方法的类呢？是否都应该扩展`Duck`类？这似乎不切实际，令人困惑(`class Train extends Duck`比狗是鸭子还要奇怪)。

也许我们可以通过使用*反射*来构建一个更加动态的鸭子。在运行时，JVM 拥有关于每个对象的大量类型信息。除了访问类型信息，反射还允许我们动态地检索和调用任意对象上的方法。

```
public class DynamicDuck extends Duck {

    private final Object notADuck;

    public DynamicDuck(Object notADuck) {
        this.notADuck = notADuck;
    }

    @Override
    public String makeNoise() {
        // try block is needed because the reflection stuff will 
        // throw all kind of horrible exceptions if you use it wrong
        try {
            // retrieve dynamic type information of the non-duck object
            final Class<?> notADuckType = this.notADuck.getClass();

            // find the method of the class that is not a duck by name
            final Method delegate = notADuckType.getMethod("makeNoise");

            // invoke the method on the object that is not a duck
            return (String) delegate.invoke(this.notADuck);
        } catch (Exception e) {
            throw new IllegalStateException("DynamicDuck error");
        }
    }
} 
```

使用我们的`DynamicDuck`,我们可以得到每个非鸭子对象的“鸭子视图”。让我们相应地调整我们的初始测试(并删除`Dog`类中的`extends`子句):

```
final Duck duck = new DynamicDuck(new Dog());
assertEquals("woof woof", duck.makeNoise()); 
```

这更具动态性，但仍不能很好地扩展。如果`Duck`类有更多的公共方法，我们就必须像上面那样将每个方法委托给非 duck 对象*。如果我们决定不把所有东西都看成鸭子，而是鹅，我们需要额外创建一个`DynamicGoose`类。那太麻烦了。*

幸运的是，Java 有另一个有用的概念，对我们的用例来说很方便。运行时可用的每个类都将使用`ClassLoader`从其字节码表示中加载。因为这是一个纯粹的运行时特性，所以你可以在运行时创建一些在编译时不存在的虚拟类。

我们可以动态地生成一个类的字节码，这个类自动地将每个被调用的方法委托给一个任意的非 duck 对象的等价方法。下面的代码使用了流行的库 *cglib* (用于字节码生成)和 *Objenesis* (用于实例化动态生成的类。

```
import net.sf.cglib.core.DefaultNamingPolicy;
import net.sf.cglib.proxy.CallbackFilter;
import net.sf.cglib.proxy.Enhancer;
import net.sf.cglib.proxy.Factory;
import net.sf.cglib.proxy.InvocationHandler;
import org.objenesis.Objenesis;
import org.objenesis.ObjenesisStd;

import java.lang.reflect.Method;

public class NotADuck {
    /**
     * The unique callback index to which each method in the proxy object is mapped.
     */
    private static final int CALLBACK_INDEX = 0;

    /**
     * Maps all methods to index {@link #CALLBACK_INDEX}.
     */
    private static final CallbackFilter ZERO_CALLBACK_FILTER = method -> CALLBACK_INDEX;

    // with caching
    private static final Objenesis OBJENESIS = new ObjenesisStd(true);

    public static <T> T asDuck(Object notADuck, Class<T> type) {
        final Enhancer enhancer = new Enhancer();
        final InvocationHandler invocationHandler = new DelegateDuckToNonDuckMethod(notADuck);

        enhancer.setSuperclass(type);
        enhancer.setUseFactory(true);
        enhancer.setNamingPolicy(new DefaultNamingPolicy());
        enhancer.setCallbackFilter(ZERO_CALLBACK_FILTER);
        enhancer.setCallbackType(invocationHandler.getClass());

        final Class<T> proxyClass = enhancer.createClass();
        final T duckInstance = OBJENESIS.getInstantiatorOf(proxyClass).newInstance();
        final Factory factory = (Factory) duckInstance;
        factory.setCallback(CALLBACK_INDEX, invocationHandler);
        return duckInstance;
    }

    private static class DelegateDuckToNonDuckMethod implements InvocationHandler {

        private final Object notADuck;

        private DelegateDuckToNonDuckMethod(Object notADuck) {
            this.notADuck = notADuck;
        }

        @Override
        public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
            final Method nonDuckMethod = notADuck.getClass().getMethod(method.getName(), method.getParameterTypes());
            return nonDuckMethod.invoke(notADuck, args);
        }
    }
} 
```

使用这个类，我们可以创建一个狗实例的动态鸭视图:

```
final Duck duck = NotADuck.asDuck(new Dog(), Duck.class);
assertEquals("woof woof", duck.makeNoise()); 
```

代码不再仅限于创建`Duck`对象:

```
List list = NotADuck.asDuck("I'm a String", List.class);
// both List and String have an isEmpty method
assertFalse(list.isEmpty()); 
```

这只是一个小实验。在这种实现中，可能存在缺陷、未被发现的边缘情况、性能问题以及普遍缺乏实际用例。尽管如此，看看 Java 的动态类型信息和任意字节代码的具体化能走多远还是很有趣的。

不要在家里这样做！
# 反思，为什么这么有用。

> 原文：<https://dev.to/rapidnerd/reflection-why-it-s-so-useful-52jf>

所以你可能在某个时候听说过编程中使用的术语“反射”,我很惊讶我的一些开发人员朋友听说过这个术语，但是从来不知道它是什么或者如何去做。简而言之，知道这些非常有用。

## 什么事？

反射是获取一个 API、库或标准库并检查它的过程，在运行时查看字段、接口、类、方法等。它还使得创建新对象和调用字段成为可能。

例如，假设我们在 Java 中有一个名为 Shopping list 的类，当我们从这个类中调用函数时，我们只能看到这个

```
import io.gpm.ShoppingList;

public void printList() {
     ShoppingList.getEverything();
} 
```

Enter fullscreen mode Exit fullscreen mode

从这个例子中，你可以很容易地看出方法`getEverything();`将会做什么，然而，如果我们看不到它背后的实际代码，而我们想包含一些我们自己的东西，那该怎么办呢？这就是反射发挥作用的地方。

## 如何

在所有的语言中，它会有所不同，对于这个例子，我将用 Java 来演示，谢天谢地，Java 提供了处理反射所需的一切。这可能与其他语言不同，根据研究，大多数广泛使用的语言都有某种形式的支持。

下面是一些关于我们如何获得方法和类的信息的非常基本的例子。

```
Method[] methods = ShoppingList.class.getMethods();

for(Method method : methods) { System.out.println(method.getName()); } 
```

Enter fullscreen mode Exit fullscreen mode

```
Class shoppingListClass = ShoppingList.class; 
```

Enter fullscreen mode Exit fullscreen mode

更多的例子可以在 Oracles 官方文档中找到

## 为什么我们需要这个？

我们可能需要它的一个很好的例子是《我的世界》，世界上最受欢迎的游戏之一。它的大部分在线服务器运行一个名为 [SpigotMC](https://spigotmc.org) 的 API，但是因为 API 和游戏只给了我们有限的方法和能力来做事情，我们需要反映到 API 和客户端中，让我们访问它发出的数据包和当前的方法，从那里可以修改它来做更多的事情，但仍然是有限的。这里的例子

```
 private static void send(Player player, PacketPlayOutTitle.EnumTitleAction action, String message, int fadeIn, int stay, int fadeOut) {
        PacketPlayOutTitle packetPlayOutTitle = new PacketPlayOutTitle();
        titleClass.getField("a").set(packetPlayOutTitle, action);
        if (action == PacketPlayOutTitle.EnumTitleAction.TITLE || action == PacketPlayOutTitle.EnumTitleAction.SUBTITLE) {
            titleClass.getField("b").set(packetPlayOutTitle, compone(message));
        }
        titleClass.getField("c").set(packetPlayOutTitle, fadeIn);
        titleClass.getField("d").set(packetPlayOutTitle, stay);
        titleClass.getField("e").set(packetPlayOutTitle, fadeOut);

        if (player != null) {
            ((CraftPlayer) player).getHandle().playerConnection.sendPacket(packetPlayOutTitle);
        } else {
            throw new IllegalArgumentException("Provided player was null!");
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，我们不得不稍微研究一下 API，找到一些名字奇怪的隐藏字段，然后自己复制这个函数，让它正常工作。最后，它只是在屏幕上显示一些文本。

这只是反映如何有用的一个小例子，下面是其他语言反映的一些例子和文档

[Java](https://docs.oracle.com/javase/8/docs/api/java/lang/reflect/package-summary.html)
T3】JavaScriptT5】T6】Ruby
[PHP](http://www.terminally-incoherent.com/blog/2008/05/27/metaprogramming-in-php/)(简称元编程)
[Python](https://www.geeksforgeeks.org/reflection-in-python/)
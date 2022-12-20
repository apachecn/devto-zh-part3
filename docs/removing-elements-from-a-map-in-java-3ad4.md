# 在 Java 中从地图中移除元素

> 原文：<https://dev.to/lankydandev/removing-elements-from-a-map-in-java-3ad4>

关于在 Java 中从一个`Map`中移除元素的非常简短的帖子。我们将关注移除多个元素，而忽略您可以使用`Map.remove`移除单个元素的事实。

下面的`Map`将用于这篇文章:

```
Map<Integer, String> map = new HashMap<>();
map.put(1, "value 1");
map.put(2, "value 2");
map.put(3, "value 3");
map.put(4, "value 4");
map.put(5, "value 5"); 
```

有几种方法可以删除元素。您可以手动遍历代码并删除它们:

```
for(Iterator<Integer> iterator = map.keySet().iterator(); iterator.hasNext(); ) {
  Integer key = iterator.next();
  if(key != 1) {
    iterator.remove();
  }
} 
```

这就是你在没有 Java 8+的情况下如何做的。从`Map`中移除元件时，需要`Iterator`来防止`ConcurrentModificationException` s。

如果你有更新版本的 Java (8+)的话，你可以从下面选择:

```
// remove by value
map.values().removeIf(value -> !value.contains("1"));
// remove by key
map.keySet().removeIf(key -> key != 1);
// remove by entry / combination of key + value
map.entrySet().removeIf(entry -> entry.getKey() != 1); 
```

`removeIf`是对`Collection` s 可用的方法。是的，`Map`本身不是`Collection`并且不能访问`removeIf`本身。但是，通过使用:`values`、`keySet`或`entrySet`，将返回`Map`内容的视图。这个视图实现了`Collection`，允许在上面调用`removeIf`。

`values`、`keySet`、`entrySet`返回的内容非常重要。下面是`values`的 JavaDoc 摘录:

```
* Returns a {@link Collection} view of the values contained in this map.
* The collection is backed by the map, so changes to the map are
* reflected in the collection, and vice-versa.
*
* The collection supports element removal, which removes the corresponding
* mapping from the map, via the {@code Iterator.remove},
* {@code Collection.remove}, {@code removeAll},
* {@code retainAll} and {@code clear} operations. 
```

这个 JavaDoc 解释说由`values`返回的`Collection`是由`Map`支持的，改变`Collection`或`Map`将会改变另一个。我不认为我能比已经写在那里的东西更好地解释 JavaDoc 正在说什么…所以我现在将停止尝试这一部分。我只展示了`values`的文档，但是当我说`keySet`和`entrySet`都由`Map`的内容支持时，你可以相信我。如果你不相信我，你可以自己看文件。

这也链接回第一个使用旧的 Java 版本*的例子。文档规定可以使用`Iterator.remove`。这是以前用过的。此外，`removeIf`的实现与`Iterator`的例子非常相似。说完了，我不妨展示一下:*

```
default boolean removeIf(Predicate<? super E> filter) {
  Objects.requireNonNull(filter);
  boolean removed = false;
  final Iterator<E> each = iterator();
  while (each.hasNext()) {
    if (filter.test(each.next())) {
      each.remove();
      removed = true;
    }
  }
  return removed;
} 
```

有一点额外的。但是，在其他方面都差不多。

所以，就这样了。除了我告诉你记住使用:`values`、`keySet`或`entrySet`将提供对`removeIf`的访问，允许轻松删除`Map`条目之外，没有太多要总结的了。

如果你觉得这篇文章很有帮助，你可以在 Twitter 上关注我，地址是 [@LankyDanDev](https://twitter.com/LankyDanDev) 来了解我的新文章。
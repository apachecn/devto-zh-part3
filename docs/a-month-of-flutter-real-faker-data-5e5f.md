# 动荡的一个月:真实的造假数据

> 原文：<https://dev.to/abraham/a-month-of-flutter-real-faker-data-5e5f>

*最初发表于[bendyworks.com](https://bendyworks.com/blog/a-month-of-flutter-real-faker-data)。*

[昨天](https://bendyworks.com/blog/a-month-of-flutter-stream-transforms-and-failing-tests)我带着失败的测试离开了代码库，因为存储在 JSON 文件[中的夹具数据无法在颤振测试中读取](https://github.com/flutter/flutter/issues/12999)。今天，我将通过在一个 Dart 文件中从静态 fixtures 切换到动态 factories 来完成向流式模拟数据的转移。去毛刺图像 id 仍然是硬编码的，但是其余的值将动态生成。

工作的核心将在`mockPostData`进行。这将创建一个带有来自 [`uuid`包](https://pub.dartlang.org/packages/uuid)的随机 [UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) 的`Map`，一个来自预选列表的随机图像 ID`DateTime`，以及带有 [`faker`包](https://pub.dartlang.org/packages/faker)的随机`text`和`username`。

我在每个`DateTime`值之间添加了一天，所以如果我生成三个帖子，它们将分别来自今天、昨天和两天前。

```
Map<String, dynamic> mockPostData({int index = 0}) {
  const Faker faker = Faker();
  final String imageId = faker.randomGenerator.element(imageIds);
  final String createdAt =
      DateTime.now().subtract(Duration(days: index)).toIso8601String();

   return <String, dynamic>{
    'id': Uuid().v4(),
    'imageUrl': 'https://source.unsplash.com/$imageId',
    'createdAt': createdAt,
    'text': faker.conference.name(),
    'username': faker.person.name()
  };
}
~~~{% endraw %}

There are also two helper methods, {% raw %}`mockPost`{% endraw %} and {% raw %}`mockPosts`{% endraw %}, to wrap the raw data in {% raw %}`Post`{% endraw %} instances. Now it's just a matter of updating the tests and the {% raw %}`_loadPosts` function to use the new mock methods.

Using faker to generate mock data results in different names showing up.

![Screenshot of random mock usernames](https://thepracticaldev.s3.amazonaws.com/i/aa14o8mhqjrkjh9y5xrg.png)

This is the base mock data rendering. Tomorrow starts the fun work of rendering the actual images.

## Code changes

- [#29 Turn mock posts into Stream](https://github.com/abraham/birb/pull/29) 
```

Enter fullscreen mode Exit fullscreen mode
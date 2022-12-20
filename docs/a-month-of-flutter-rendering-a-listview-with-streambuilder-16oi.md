# 动荡的一个月:用 StreamBuilder 渲染 ListView

> 原文：<https://dev.to/abraham/a-month-of-flutter-rendering-a-listview-with-streambuilder-16oi>

*最初发表于[bendyworks.com](https://bendyworks.com/blog/a-month-of-flutter-rendering-a-list-view-with-stream-builder)。*

Birb 的数据将存储在 [Firebase Cloud Firestore](https://firebase.google.com/docs/firestore/) 中。当查看处理获取和渲染数据的模式时，我决定使用 [cloud_firestore](https://pub.dartlang.org/packages/cloud_firestore) 包文档中的 [`StreamBuilder`](https://docs.flutter.io/flutter/widgets/StreamBuilder-class.html) 示例。我不知道分页在这种方式下会有多好，所以这将是另一天的实验。

我要做的第一个改变是将静态的`List<int>`变成一个`Stream`，并将其在窗口小部件树中向上移动。我希望`PostsList`小部件只关心来自`Stream`的渲染项目，而不是如何创建`Stream`本身。这使得在测试中模拟数据变得更加容易。

```
final Stream<List<int>> _posts = Stream<List<int>>.fromIterable(
  <List<int>>[
    List<int>.generate(10, (int i) => i),
  ],
);
~~~{% endraw %}

This looks a little weird but it is basically generating a list of 10 items. That {% raw %}`List`{% endraw %} of 10 items is used as the first value in a new {% raw %}`Stream`{% endraw %}. From the subscription side of the {% raw %}`Stream`{% endraw %}, there will be a single event with data that is a {% raw %}`List`{% endraw %} of 10 items. I'm going with this pattern because Firestore will have snapshots with multiple documents.

The {% raw %}`PostsList` `build` method needs to be updated to consume the `Stream` and use a `StreamBuilder`:

~~~dart
@override
Widget build(BuildContext context) {
  return StreamBuilder<List<int>>(
    stream: posts,
    builder: (BuildContext context, AsyncSnapshot<List<int>> snapshot) {
      if (snapshot.hasError) {
        return Text('Error: ${snapshot.error}');
      }
       switch (snapshot.connectionState) {
        case ConnectionState.waiting:
          return const Text('Loading...');
        default:
          if (snapshot.data.isEmpty) {
            return const NoContent();
          }
          return _itemList(snapshot.data);
      }
    },
  );
}
~~~

The `StreamBuilder` takes a `Stream` and will then call the builder with an [`AsyncSnapshot`](https://docs.flutter.io/flutter/widgets/AsyncSnapshot-class.html). There are a couple of different states on this snapshot that need to be handled:

 - The first is checking to see if there has been an error. If there has been, render some error text.
 - Second, if the connection is waiting, show a loader while waiting for data to arrive.
 - Third, if there is no data, render the `NoContent` widget.
 - Finally if none of the previous cases are met, render the actual data.

Looking at the tests for `PostList`, all four of those scenarios are tested by creating different kinds of mock streams with the `_postsStream` helper.

~~~dart
Stream<List<int>> _postsStream(int count) {
  return Stream<List<int>>.fromIterable(
    <List<int>>[
      List<int>.generate(count, (int i) => i),
    ],
  );
}
~~~

Then I can test that the loading text is shown, followed a test that all the mocked items are rendered.

~~~dart
testWidgets('renders list of PostItems', (WidgetTester tester) async {
  // Build our app and trigger a frame.
  await tester.pumpWidget(MaterialApp(
    home: PostsList(_postsStream(5)),
  ));

  expect(find.text('Loading...'), findsOneWidget);

  await tester.pump(Duration.zero);

  expect(find.byType(PostItem), findsNWidgets(5));
});
~~~

To simulate the error case, I can throw an error on a `Future` and convert that to a stream.

~~~dart
testWidgets('renders NoContent widget', (WidgetTester tester) async {
  // Build our app and trigger a frame.
  await tester.pumpWidget(MaterialApp(
    home: PostsList(Future<List<int>>.error('Bad Connection').asStream()),
  ));

  await tester.pump(Duration.zero);
   expect(find.text('Error: Bad Connection'), findsOneWidget);
});
~~~

One other minor change I made was to reduce the height of the cards so I could be sure the additional cards were rendering.

![List of short material cards](https://thepracticaldev.s3.amazonaws.com/i/7btcsxqqu4xif69xvp2r.png)

## Code changes

- [#25 Convert PostsList to use a StreamBuilder](https://github.com/abraham/birb/pull/25) 
```

Enter fullscreen mode Exit fullscreen mode
# 动荡的一个月:美味的欢迎小吃

> 原文：<https://dev.to/abraham/a-month-of-flutter-delicious-welcome-snackbar-3451>

*最初发表于[bendyworks.com](https://bendyworks.com/blog/a-month-of-flutter-delicious-welcome-snackbar)。*

当用户登录时，他们应该被告知登录是否成功。我将使用一个 [`SnackBar`小部件](https://docs.flutter.io/flutter/material/SnackBar-class.html)来完成这个任务。请注意，颤振`SnackBar`样式是[与当前](https://github.com/flutter/flutter/issues/19841)[材料设计规格](https://material.io/design/components/snackbars.html)的过时。

代码变化不是很大，但包括几个有趣的方面。一个是`SnackBar` API 需要引用父`Scaffold`小部件。这是因为`SnackBar`的定位非常具体。如果你只是不加考虑地显示它，它会覆盖像`BottomAppBar` s 或`FloatingActionButton` s 这样的东西。`Scaffold`控制这些小部件的位置，所以它可以确保`SnackBar` s 不会干扰它们。新的`_showSnackBar`方法将处理`SnackBar`的显示。

```
void _showSnackBar(BuildContext context, String msg) {
  final SnackBar snackBar = SnackBar(content: Text(msg));

  Scaffold.of(context).showSnackBar(snackBar);
}
~~~{% endraw %}

{% raw %}`_handleSignIn`{% endraw %} has been updated to trigger a {% raw %}`SnackBar`{% endraw %} with a message and it will pass through the [current {% raw %}`BuildContext`{% endraw %}](https://docs.flutter.io/flutter/widgets/BuildContext-class.html).{% raw %}

~~~dart
void _handleSignIn(BuildContext context) {
  auth.signInWithGoogle().then((FirebaseUser user) =>
      _showSnackBar(context, 'Welcome ${user.displayName}'));
}
~~~{% endraw %}

The {% raw %}`SignInFab`{% endraw %} tests will have to get wrapped in a {% raw %}`Scaffold`{% endraw %} widget to render properly. I've also taken the time to split the existing test in two, one for the rendering of the FAB and one for triggering the sign in flow.

A new test will assert there is not already a {% raw %}`SnackBar`, trigger a tap on the FAB, pause to let the widgets render, and then make sure the `SnackBar` is rendered as expected.

~~~dart
testWidgets('Displays welcome SnackBar', (WidgetTester tester) async {
  // Build our app and trigger a frame.
  await tester.pumpWidget(app);

  expect(find.byType(SnackBar), findsNothing);

  await tester.tap(find.byType(FloatingActionButton));
  await tester.pump(Duration.zero);

  expect(find.byType(SnackBar), findsOneWidget);
  expect(find.text('Welcome ${userMock.displayName}'), findsOneWidget);
});
~~~

There is now a nice notification when a user finishes signing in.

![Welcome notification](https://thepracticaldev.s3.amazonaws.com/i/9ygi7olbv0j1rotk1tip.png)

While working on this I've noticed there is a bug in bottom navigation icon colors. They switch to white on black while the authentication flow is happening but then the icons never switch back to black. I've [filed a bug](https://github.com/abraham/birb/issues/51) to look into a fix.

I also experimented with a light theme `SnackBar` but it doesn't provide enough contrast for my taste.

![Experimental light themed welcome notification](https://thepracticaldev.s3.amazonaws.com/i/wrf2agnzgkgvir1ygo8u.png)

## Code changes

- [#50 Show welcome message](https://github.com/abraham/birb/pull/50) 
```

Enter fullscreen mode Exit fullscreen mode
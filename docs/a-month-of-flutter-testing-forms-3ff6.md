# 动荡的一个月:测试表单

> 原文：<https://dev.to/abraham/a-month-of-flutter-testing-forms-3ff6>

*最初发表于[bendyworks.com](https://bendyworks.com/blog/a-month-of-flutter-testing-forms)。*

有了新的[用户注册表单](https://bendyworks.com/blog/a-month-of-flutter-user-registration-form)之后，是时候确保该表单已经过[测试](https://flutter.io/docs/cookbook/testing/widget/tap-drag)并将按预期工作了。

基本上有五种不同的状态需要测试。

## 默认状态

这是用户将首先到达的视图，在这里，我测试所有的组件是否如预期的那样存在。

```
testWidgets('Renders', (WidgetTester tester) async {
  await tester.pumpWidget(app);

  expect(find.text('Register'), findsOneWidget);
  expect(find.text('I agree to the Terms of Services and Privacy Policy'),
      findsOneWidget);
  expect(find.byType(TextFormField), findsNWidgets(2));
  expect(find.byType(OutlineButton), findsOneWidget);
  expect(find.byType(Checkbox), findsOneWidget);
});
~~~{% endraw %}

## Submitted

To succesfully submit the form, I require the user to provide a nickname and a full name. In the test those values will be provided with [{% raw %}`enterText`{% endraw %}](https://docs.flutter.io/flutter/flutter_test/WidgetTester/enterText.html). After filling out the two {% raw %}`TextFormFields` and submitting the form, I wait a tick for the success `SnackBar` to render.

~~~dart
testWidgets('Form can be submitted', (WidgetTester tester) async {
  await tester.pumpWidget(app);
  final Finder nickname = find.widgetWithText(TextFormField, 'Nickname');
  final Finder fullName = find.widgetWithText(TextFormField, 'Full name');
  final Finder submit = find.widgetWithText(OutlineButton, 'Register');

  expect(find.text('Form submitted'), findsNothing);

  await tester.enterText(nickname, 'Jess');
  await tester.enterText(fullName, 'Jess Sampson');

  await tester.tap(submit);
  await tester.pump();

  expect(find.text('Form submitted'), findsOneWidget);
});
~~~{% endraw %}

The success {% raw %}`SnackBar`{% endraw %} is a temporary placeholder so that I have an in-app confirmation the form was submitted. Once user registration logic is in place this messaging to the user will change.{% raw %}

~~~dart
void _submit() {
  if (_formKey.currentState.validate()) {
    const SnackBar snackBar = SnackBar(content: Text('Form submitted'));

    Scaffold.of(context).showSnackBar(snackBar);
  }
}
~~~{% endraw %}

## Required fields

Next there are two tests to make sure the nickname and full name fields are required. This checks that the required message was displayed and that the success message was not displayed.{% raw %}

~~~dart
testWidgets('Form requires nickname', (WidgetTester tester) async {
  await tester.pumpWidget(app);
  final Finder submit = find.widgetWithText(OutlineButton, 'Register');
  await tester.tap(submit);
  await tester.pump();

  expect(find.text('Nickname is required'), findsOneWidget);
  expect(find.text('Form submitted'), findsNothing);
});
~~~{% endraw %}

There are some improvements that could be made this error display. If a user focuses an errored {% raw %}`TextFormField`{% endraw %} and enters a letter, the error message should disappear. I've [created an issue](https://github.com/abraham/birb/issues/55) to implement this in the future.

## Disabled submit

If a user disables the Terms of Service/Privacy Policy checkbox, they are not longer permitted to register. Here I am testing that the submit button is disabled if the checkbox is unchecked.

The [{% raw %}`WidgetTester#widget` method](https://docs.flutter.io/flutter/flutter_test/WidgetController/widget.html) is a way to get a reference to a finder's actual widget. This is how I'm testing to see if the submit button is disabled. Usually you should test UI that the user can see but in this case the state of the button is conveyed to the user through its styling.

~~~dart
testWidgets('Submit disabled if TOS unchecked', (WidgetTester tester) async {
  await tester.pumpWidget(app);
  final Finder submit = find.widgetWithText(OutlineButton, 'Register');
  final Finder tos = find.byType(Checkbox);

  expect(tester.widget<OutlineButton>(submit).enabled, isTrue);

  await tester.tap(tos);
  await tester.tap(submit);
  await tester.pump();

  expect(tester.widget<OutlineButton>(submit).enabled, isFalse);
  expect(find.text('Form submitted'), findsNothing);
});
~~~

## Code changes

- [#54 Add registration form](https://github.com/abraham/birb/pull/54) 
```

Enter fullscreen mode Exit fullscreen mode
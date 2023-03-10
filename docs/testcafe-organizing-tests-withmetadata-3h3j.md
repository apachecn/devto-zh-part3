# TestCafe:用元数据组织测试

> 原文：<https://dev.to/pluralsight/testcafe-organizing-tests-withmetadata-3h3j>

*这是我在[媒体](https://medium.com/@mwq27/testcafe-organizing-tests-with-metadata-26900dbfdf55)T3 上的博客的交叉帖子*

TestCafe 框架为夹具和测试组织提供了一个不错的小特性。所有的夹具和测试都有一个可选的元属性，您可以使用它来附加任何附加信息。让我们看看如何将这个元属性用于更细粒度的测试运行。我们将首先看几个不同的方法来过滤你的测试。

### 按文件名过滤

我认为这是拆分测试最常见的方式。你可以将你的认证测试放在一个类似于 **authentication.spec.ts** 的文件中，或者将配置文件页面测试放在 **profile.spec.ts** 中，等等。如果您想将这些测试的子集作为冒烟测试运行呢？或者您有一组只想在 Internet Explorer 或 Safari 中运行的测试？如果你依赖于文件名，你可以创建一个名为 **smoke-tests.spec.ts** 的文件，将你想要的任何测试复制/粘贴到这个文件中。但是这需要重复测试代码，并且很难维护。

### 按夹具或测试名称过滤

您可以仅通过夹具名称:
来过滤运行哪些测试

```
$ testcafe chrome login.spec.ts --fixture "Bad Passwords"
# or by regular expression 
$ testcafe chrome login.spec.ts --fixture-grep "Bad.*" 
```

为了告诉 TestCafe 什么样的设备/测试将是冒烟测试，您可以将您的设备命名为:

```
fixture('Bad Passwords: smoke'); 
test('Incorrect passwords should show an error: smoke'); 
```

然后，您可以使用
进行过滤

```
--fixture-grep "smoke"
# or
--test-grep "smoke" 
```

然而，这可能会使设备名称变得脆弱。如果一个夹具名称中有“smoke”这个词，即使您不希望它是一个 smoke 测试，该怎么办？

### 使用元数据过滤

我们可以使用 **meta** 属性来创建一个测试子集，如下所示:

```
// login.spec.js 
fixture('Login Page') 
 .page('https://some-app.com') 
 .meta('smokeTest', true) 
 .meta('ieOnly', true); 
// Or you can pass an object instead 
...
 .meta({ smokeTest: true, ieOnly: true}) 
```

有了 meta 属性，您现在可以像这样运行测试:

```
$ testcafe chrome login.spec.js --fixture-meta smokeTest=true 
```

您甚至可以在测试中使用 meta 属性:

```
// login.spec.js 
fixture('Login Page') 
 .page('https://some-app.com'); 
test('should show an error with incorrect username', …) 
 .meta({ smokeTest: true }); 
```

你可以像使用`--fixture-meta`选项一样使用`--test-meta`选项:

```
$ testcafe chrome login.spec.js --test-meta smokeTest=true 
```

现在我们有了一个干净的方法来创建测试子集，它不依赖于 fixture/test 名称，或者文件名。元数据不仅可以用于过滤测试，还可以在您自己的定制测试报告中使用。

### 结论

TestCafe 的元数据特性使得在您的端到端套件中分离和组织测试运行变得简单，不仅是在设备级别，而且是在测试级别。我认为你应该在测试中充分利用 TestCafe。
# 动荡的一个月:持续的林挺

> 原文：<https://dev.to/abraham/a-month-of-flutter-continuous-linting-3di5>

*最初发表于[bendyworks.com](https://bendyworks.com/blog/a-month-of-flutter-continuous-linting)。*

昨天我[配置了 CI](https://bendyworks.com/blog/a-month-of-flutter-configuring-continuous-integration) 。今天，配合 [@tpolansk 的建议](https://twitter.com/tpolansk/status/1013320378939305984)，我们来设定[林挺](https://flutter.io/docs/testing/debugging#the-dart-analyzer)。对于这个项目，我们将从颤振小组使用的分析选项开始。为了下载该文件，我将在存储库的根目录下运行以下命令。

```
$ wget https://raw.githubusercontent.com/flutter/flutter/master/analysis_options.yaml
~~~{% endraw %}

The {% raw %}`analysis_options.yaml`{% endraw %} file tells the {% raw %}`dartanalyzer`{% endraw %} tool the preferences to use when analyzing the codebase. You can now run {% raw %}`flutter analyze`{% endraw %} to see everything in the app that can be improved. Your editor should use this file to show you inline feedback as well.{% raw %}

~~~bash
$ flutter analyze
Analyzing app...

   info • Prefer declare const constructors on `@immutable` classes • lib/main.dart:29:3 • prefer_const_constructors_in_immutables
   info • Prefer const with constant constructors • lib/main.dart:94:13 • prefer_const_constructors
   info • Prefer const with constant constructors • lib/main.dart:107:16 • prefer_const_constructors

3 issues found. (ran in 0.8s)
~~~{% endraw %}

Linting is one of the tooling options you want to get into a project as soon as possible so you don't run into issues like @hillelcoren did.

> A bit depressing seeing the number of warnings go from 2 to 2,000 but better now than later...
>
> [@hillelcoren](https://twitter.com/hillelcoren/status/1013337198295441409)

But having linting set up in your app doesn't mean developers will run or follow the recommended patterns. So let's enforce it with the [CI we set up yesterday](https://bendyworks.com/blog/a-month-of-flutter-configuring-continuous-integration).

Add the following to {% raw %}`.travis.yml`:

~~~yaml
matrix:
  include:
  - name: Test
    env: COMMAND=test
  - name: Analyze
    env: COMMAND=analyze
~~~

And modify the `script` line to use `$COMMAND` instead of `test` like this:

~~~yaml
script:
  - ./flutter/bin/flutter $COMMAND
~~~

The [`matrix` option](https://docs.travis-ci.com/user/customizing-the-build#build-matrix) on Travis CI is very powerful. It lets you set up multiple test runs that will perform in parallel.

Now we need to make the suggested improvements. In this case it's as simple as adding a couple of {% raw %}`const`{% endraw %}s where the analyzer says. Using {% raw %}`const`{% endraw %} lets the Dart compiler [better optimize your code](https://stackoverflow.com/a/47996512/26406) and improve performance.

Come back tomorrow when I'll be doing talking about Flutter's [big announcement](https://developers.googleblog.com/2018/12/flutter-10-googles-portable-ui-toolkit.html).

## Code changes

- [#7 Run flutter analyze on CI](https://github.com/abraham/birb/pull/7) 
```

Enter fullscreen mode Exit fullscreen mode
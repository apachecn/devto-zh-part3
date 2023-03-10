# 动荡的一个月:配置持续集成

> 原文：<https://dev.to/abraham/a-month-of-flutter-configuring-continuous-integration-2772>

*最初发表于[bendyworks.com](https://bendyworks.com/blog/a-month-of-flutter-configuring-continuous-integration)。*

当开始一个新项目时，我喜欢尽可能自动化。最重要的任务之一是让测试在 [CI](https://en.wikipedia.org/wiki/Continuous_integration) 上运行。有很多很棒的 CI 服务，但对于这个项目，我会选择 [Travis CI](https://travis-ci.org/) 。

让我们来看看我选定的配置及其全部含义:

```
language: dart
dist: xenial
addons:
  apt:
    packages:
      - lib32stdc++6
install:
  - git clone https://github.com/flutter/flutter.git -b beta
  - ./flutter/bin/flutter doctor
script:
  - cd app
  - ../flutter/bin/flutter test
cache:
  directories:
    - $HOME/.pub-cache
~~~

 - `language` tailors the CI environment for a specific language. If you leave this out, the default language is Ruby. This isn't strictly needed as Flutter will actually download its own version of Dart. I specified Dart so that if I write CI tooling in the future, I can do it in Dart.
 - `dist` specifies the Ubuntu base version that gets used. Travis currently defaults to Trusty Tahr which is end of life in 2019 so I'm specifying Xenial Xerus which is supported until 2021.
 - `addons` lets you have additional packages installed on the OS. In my experimentation, tests still pass without `lib32stdc++6` but `flutter doctor` complains so I'm including it anyway.
 - `install` would typically call Dart's `pub get` but Flutter complains so we'll just override that and install Flutter instead.
 - `script` is where we run the tests. Not that the Birb app is in the `app` directory so we have to `cd` into it first.
 - `cache` is the final bit. This tells Travis to cache the contents of the default Dart package cache. This should make it faster for Flutter to install the app's dependencies.

This configuration runs great and takes about 2 minutes 30 seconds on the base app.

I also tried caching the `flutter` directory to boost install speed. Here is an configuration iteration I tried:

~~~yaml
install:
  - |
    if [ ! -d flutter ] ; then
      git clone https://github.com/flutter/flutter.git -b beta
    fi
  - ./flutter/bin/flutter doctor
~~~
~~~yaml
cache:
  directories:
    - flutter
    - $HOME/.pub-cache
~~~

Caching the `flutter` directory dropped the test run time to to about 1 minute 10 seconds! The install step was then more complex as it has to check if the `flutter` directory exists and only run `git clone` if it doesn't. But there was a larger issue where the "if directory does not exist" was having false positives and causing Flutter to not get installed correctly. I would like to improve CI performance but don't want to spend any more time on it now so I've added [#6 Cache `flutter` directory on CI](https://github.com/abraham/birb/issues/6) to the backlog as a future improvement.

With CI setup, I like to [protect](https://help.github.com/articles/about-protected-branches/) the `master` branch from accidental pushes. This is less important when you are working on your own but is very important when working with teams. It's also a good way to require that tests are kept in a passing state.

This is how I configured GitHub's branch protection on the `master` branch:

![Configuring master as a protected branch on GitHub](https://thepracticaldev.s3.amazonaws.com/i/2mdkswyqynur4b8nsyee.png)

Come back tomorrow when I'll [setup linting](https://github.com/abraham/birb/issues/3).

## Code changes

- [#5 Add travis CI config](https://github.com/abraham/birb/pull/5) 
```

Enter fullscreen mode Exit fullscreen mode
# 当重构你的响应式 CSS 时，结果适得其反

> 原文：<https://dev.to/maxwell_dev/when-refactoring-your-responsive-css-backfires-3aln>

几周前，Dev.to 的 Jaqcue Schrag 发表了一篇关于重构她曾经写过的最糟糕的代码的文章。我认为这既是对用新思维解决老问题的洞察，也是对重构我自己的旧代码的推动。

[![jnschrag image](img/ea81c9c791bca7f8590542588559b7fc.png)](/jnschrag) [## 重构我写过的最糟糕的代码

### jacque sch rag Apr 18 ' 198min read

#javascript #beginners #es6 #refactoring](/jnschrag/refactoring-the-worst-code-i-ve-ever-written-42c7)

我用我的 Sass 模板挖掘了 repo 来构建原子样式表，反正早就应该更新了。这篇文章主要关注重构代码库中最重要的，也可以说是写得最差的部分。令我惊讶的是，我实际上很好地重构了代码*并且制造了一个更糟糕的问题，我不得不在事后解决它。*

这就是重构，大多数浪漫喜剧的情节，以及生活本身。

## 湿响应类设置

在我的原子 CSS 设置中，有几个样式是作为响应类构建的，它们的样式可以插入或覆盖不同的断点。举个例子，像这样的一个`div`。

```
<div class="atomic-p-base atomic-p-double-md">
  I'm a div!
</div> 
```

Enter fullscreen mode Exit fullscreen mode

简而言之，`atomic-p-base`类提供了一些填充，`atomic-p-double-md`类覆盖了大屏幕上的填充。

这是可行的，但是我想在重构中改变两件事。

首先，我希望以不同的方式将`md`添加到类名中。原子类应该一目了然地传达它们的功能，用破折号写名字和断点标签会使它们过于模糊。 [Tailwind CSS](https://tailwindcss.com/) 使用冒号语法，比如`md:px-4`。为了达到同样的效果，我想模仿它，把我的名字改成`atomic-p-double:md`。

第二，产生这种现象的 Sass 太潮湿。断点和媒体查询的代码足够简洁，正如您在这里看到的。

```
// Map for the breakpoints and max width
$breakpoint-map: (
  xs: 0px,
  sm: rem(480px),
  md: rem(800px),
  max: rem(1200px)
);

// Mixin for fast media queries based on the breakpoint map
@mixin larger-than($point-name) {
  @if ($point-name != 'xs') {
    $width: map-get($breakpoint-map, $point-name);

    @media (min-width: $width) { @content; }
  } @else {
    @content;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

然而，如果我想使一组新的类具有响应性，我需要重写下面的逻辑，以便每次创建和添加类名标签。

```
@each $bp-label, $bp in $breakpoint-map {

  $bp-label-final: '';

  @if ($bp-label != 'xs') { $bp-label-final: '-' + $bp-label; }

  @include larger-than($bp-label) {
    @each $label, $length in $spacing-map {
      // Margin
      #{$g-nmsp}m-#{$label}#{$bp-label-final} { margin: $length; }
      #{$g-nmsp}mt-#{$label}#{$bp-label-final} { margin-top: $length; }
      #{$g-nmsp}mr-#{$label}#{$bp-label-final} { margin-right: $length; }
      #{$g-nmsp}mb-#{$label}#{$bp-label-final} { margin-bottom: $length; }
      #{$g-nmsp}ml-#{$label}#{$bp-label-final} { margin-left: $length; }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

不得不重复写出`$bp-label-final`的逻辑并不理想。特别是因为，如果我想更改命名，我需要在几个地方进行更改。所以两个重构都归结于将这个逻辑移入 Sass mixin。

我一点也不知道萨斯代码与我的计划不一致...

## 首先重构

暂时抛开 CSS 情节的曲折，这是我第一次尝试“响应类名”mixin。

```
@mixin rsp-class($class-name) {
  @each $bp-label, $bp in $breakpoint-map {

    $bp-label-final: '';
    @if ($bp-label != 'xs') { $bp-label-final: \: + $bp-label; }

    @include larger-than($bp-label) {
      #{$g-nmsp}#{$class-name}#{$bp-label-final} {
        @content;
      }
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

将该逻辑引入可重用的 mixin 是一个非常简单的步骤，对吗？我所需要做的就是传入类名，然后就会创建几个响应类。

您还将看到在这一行中实现的另一个重构目标。

```
@if ($bp-label != 'xs') { $bp-label-final: \: + $bp-label; } 
```

Enter fullscreen mode Exit fullscreen mode

断点标签添加了一个命名空间冒号，使其以`:md`而不是`-md`结束。就像顺风一样！

剩下的就是替换代码库中重复的逻辑，比如用增加边距的响应类。

```
@each $label, $length in $spacing-map {
  @include rsp-class('m-#{$label}') { margin: $length; }
  @include rsp-class('mt-#{$label}') { margin-top: $length; }
  @include rsp-class('mr-#{$label}') { margin-right: $length; }
  @include rsp-class('mb-#{$label}') { margin-bottom: $length; }
  @include rsp-class('ml-#{$label}') { margin-left: $length; }
} 
```

Enter fullscreen mode Exit fullscreen mode

对于更简单的响应类，比如那些用于文本对齐的类，这样读起来更好。

```
@include rsp-class('text-center') { text-align: center; }
@include rsp-class('text-right') { text-align: right; }
@include rsp-class('text-left') { text-align: left; } 
```

Enter fullscreen mode Exit fullscreen mode

## 瀑布毁了我的兴致

后来，我发现 CSS 的生成方式有一个微妙的不同，这使得很多工作变得毫无用处。如果你还没想明白，就停下来看看吧！

完成了吗？我们继续。

区别在于这些响应类的创建顺序。第一个版本接受一组类，制作每个类的基本版本，然后按照升序制作基于断点的响应版本。结果是这样的:

```
.atomic-text-center {
    text-align: center
}

.atomic-text-right {
    text-align: right
}

@media (min-width: 30rem) {
    .atomic-text-center\:sm {
        text-align: center
    }
}

@media (min-width: 30rem) {
    .atomic-text-right\:sm {
        text-align: right
    }
}

@media (min-width: 50rem) {
    .atomic-text-center\:md {
        text-align: center
    }
}

@media (min-width: 50rem) {
    .atomic-text-right\:md {
        text-align: right
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

新版本的做法有所不同。mixin 一次只上一门课。它遍历该类的所有断点，然后从下一个断点开始。

```
.atomic-text-center {
    text-align: center
}

@media (min-width: 30rem) {
    .atomic-text-center\:sm {
        text-align: center
    }
}

@media (min-width: 50rem) {
    .atomic-text-center\:md {
        text-align: center
    }
}

.atomic-text-right {
    text-align: right
}

@media (min-width: 30rem) {
    .atomic-text-right\:sm {
        text-align: right
    }
}

@media (min-width: 50rem) {
    .atomic-text-right\:md {
        text-align: right
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这看起来没什么，但这就是 CSS。看似无关紧要的变化会毁掉整个事情。

关键是 **CSS 媒体查询不会增加一个类的特异性。**这些编译表中的每个类都具有相同的特异性，因此它们根据顺序相互覆盖。表单上较低的样式会覆盖之前的样式。

在这种情况下，我希望大屏幕上的类总是覆盖小屏幕上的类。只有当所有较大的断点类都放在更下面时，这种方法才有效。以前是这样的，但我的改变破坏了这一点。

回头看看 refactor 中已编译的 CSS 的更新示例。假设我有一个这样的元素。

```
<div class="atomic-text-right atomic-text-center:md">
</div> 
```

Enter fullscreen mode Exit fullscreen mode

在重构的 CSS 中，`atomic-text-right`在级联中比`atomic-text-center:md`更低。**尽管响应式类应该介入，但基类会在不应该介入时覆盖它。**这使得响应式风格类变得如此不一致，几乎毫无用处。这是一个过度适应的例子，或者说是让代码过于专注于解决一个问题，从而导致了其他问题。

每当有人说 CSS 很容易或者“不是真正的编程语言”，记住这样的案例！

### 第二次重构

正如我遇到的大多数过度合身的情况一样，我需要向后退一步。将命名逻辑和断点循环都移到 mixin 中太多了。所以我让 mixin 只负责命名。

```
@mixin rsp-class($bp-label, $class-name) {
  $bp-label-final: '';
  @if ($bp-label != 'xs') { $bp-label-final: \: + $bp-label; }

  @include larger-than($bp-label) {
    #{$g-nmsp}#{$class-name}#{$bp-label-final} {
      @content;
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这一变化意味着当我需要响应类时，我需要再次循环遍历断点图。我还需要传入断点标签来创建正确的类名。

```
// For Margin classes
@each $bp-label, $bp in $breakpoint-map {
  @each $label, $length in $spacing-map {
    @include rsp-class($bp-label, 'm-#{$label}') { margin: $length; }
    @include rsp-class($bp-label, 'mt-#{$label}') { margin-top: $length; }
    @include rsp-class($bp-label, 'mr-#{$label}') { margin-right: $length; }
    @include rsp-class($bp-label, 'mb-#{$label}') { margin-bottom: $length; }
    @include rsp-class($bp-label, 'ml-#{$label}') { margin-left: $length; }
  }
}

// Also for text alignment classes
@each $bp-label, $bp in $breakpoint-map {
  @include rsp-class($bp-label, 'text-center') { text-align: center; }
  @include rsp-class($bp-label, 'text-right') { text-align: right; }
  @include rsp-class($bp-label, 'text-left') { text-align: left; }
} 
```

Enter fullscreen mode Exit fullscreen mode

它没有第一次重构那么枯燥，但仍然比我开始的地方枯燥得多。命名逻辑是最麻烦的，也是最容易改变的，所以把它放在一个 mixin 中仍然是一个胜利。

## 一次最终成功的重构

对于我的原子 CSS 模板，还有很多重构，但这是最大的一个。这也是最有见地的，因为它提醒我们重构代码是多么艰难。如果不小心，看似正确的事情实际上会使代码变得更糟。找到副作用最小的最佳解决方案是真正困难的部分，需要妥协(比如接受我的新代码不能 100%是干的)。

尽管如此，像这样解决问题的方式让编程对我来说如此有趣。尽管重构是一场永无止境的战斗，但每一次重构都让我们变得更加强大。正如 Jacque 在激发了这篇文章的帖子中所写的，这种增长是值得庆祝的。所以我期待下一个像这样的重构难题！

*封面图片由 SafeBooru.org 提供*
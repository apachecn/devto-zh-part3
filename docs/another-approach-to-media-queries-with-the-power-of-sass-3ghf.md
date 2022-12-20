# 具有 Sass 功能的另一种媒体查询方法

> 原文：<https://dev.to/duranenmanuel/another-approach-to-media-queries-with-the-power-of-sass-3ghf>

当你一直在开发网站和布局[的时候，只要我一直在](https://enmascript.com/articles/2018/06/27/most-important-things-i-have-learned-after-10-years-as-a-software-developer)，你就会明白一件事，*拥有动态的未定义断点和媒体查询不是一个好主意*，为什么？，因为他们给你带来的问题比他们解决的更多...例如，看看下面的代码:

```
.wrapper {
    margin: 0 -15px;
}

@media screen and (min-width: 420px) and (max-width: 835px) {
    .wrapper {
        margin: 0 -5px;
    }
}

@media screen and (min-width: 836px) and (max-width: 1130px) {
    .wrapper {
        margin: 0;
    }
} 
```

哦，亲爱的...

## 问题

在上面的例子中有很多不好的事情发生:

1.  它有 hacky/ackward 断点和媒体查询。
2.  不清楚每个媒体查询覆盖的是什么屏幕或设备。
3.  它会产生难以解决的不一致和问题，通常需要一个粗略的解决方案。
4.  许多其他事情...*(很抱歉这个痛苦的例子...)*

## 向好的方向迈出的一步

如果你和我一样害怕在代码库中找到与上面类似的代码，这意味着你很优秀，作为一名开发人员，你已经受够了这种痛苦(这意味着是时候继续前进了)，你要做的第一件事就是考虑如何组织你的媒体查询？、*用什么标准？*，如果你正在开发一个个人项目，你可能已经谷歌过*“一个网站应该覆盖的最重要的断点是什么？*“，(不是我以前这么干的，*挤眉弄眼*)。这些问题的答案并不总是直截了当的，它真的取决于你在建设什么，以及确定哪些是你的网站/企业最重要的流量/收入来源。几个最流行的方法是按照**屏幕尺寸**划分媒体查询:

*   特大号、小号、中号、大号。

**或最流行设备的组合:**

*   *手机、平板电脑、台式机、大型台式机...*

可以选择任何适合你的方法*，只要它是一致的和可伸缩的*，在 Sass 中，我们可以通过使用*变量*为这些组合赋值，类似于:

```
$xs: 480px;
$sm: 768px;
$md: 992px;
$lg: 1200px; 
```

或者

```
$phone: 480px;
$tablet: 768px;
$desktop: 992px;
$large-desktop: 1200px; 
```

这并不坏，它允许我们创建更详细和一致的媒体查询，例如:

```
@media screen and (min-width: $phone) and (max-width: $tablet - 1px) { ... } 
```

这是可以的，注意我们是如何从`max-width: $tablet`中减去`1px`的，背后的原因很简单，如果我们使用`min-width: $tablet`而不从`max-width`中减去`1px`，其中一个媒体查询将会重叠，因为两者共享一个断点`(768px)`。

正如我所说的，这种方法很好，但是有一点，您仍然需要为每种情况编写所有的媒体查询语句，包括-1px。

## 借助 SASS 和 mixins 的强大功能，更好地进行媒体查询

既然我们知道并理解了良好的媒体查询的基础，**请允许我向您介绍一种非常强大和灵活的方法**，我们将从展示实现开始，它看起来有点复杂，但请相信我，**使用它非常简单**(如果您想先阅读解释，可以跳过这段代码，稍后再回来):

```
$devices: (
    phone: 480px,
    tablet: 768px,
    desktop: 992px,
    large-desktop: 1200px
);

@mixin min-device($device: map-get($devices, 'phone')) {
    @if map-has-key($devices, $device) {
        @media screen and (min-width: map-get($devices, $device)) {
            @content;
        }
    }
}

@mixin max-device($device: map-get($devices, 'phone')) {
    @if map-has-key($devices, $device) {
        @media screen and (max-width: map-get($devices, $device) - 1) {
            @content;
        }
    }
}

@mixin only-device($device: map-get($devices, 'phone')) {
    @if map-has-key($devices, $device) {
        $devices-length: length($devices);
        $map-list: map-keys($devices);

        @if index($map-list, $device) == $devices-length {
            @include min-device($device) {
                @content;
            }
        } @else {
            $next-device-index: index($map-list, $device) + 1;
            $next-device-key: nth($map-list, $next-device-index);

            @media screen and (min-width: map-get($devices, $device)) and (max-width: map-get($devices, $next-device-key) - 1) {
                @content;
            }
        }
    }
}

@mixin between-devices(
    $min-device: map-get($devices, 'phone'),
    $max-device: map-get($devices, 'tablet')
) {
    @if map-has-key($devices, $min-device) and
        map-has-key($devices, $max-device)
    {
        @media screen and (min-width: map-get($devices, $min-device)) and (max-width: map-get($devices, $max-device) - 1) {
            @content;
        }
    }
} 
```

这种方法将允许我们使用一种非常简单和详细的方法来表达媒体查询和断点，**下面是您使用它的方式**:

```
/* Apply to devices with a higher screen than phone */
@include min-device("phone") { ... }

/* Apply to devices with screen sizes smaller than tablet */
@include max-device("tablet") { ... }

/* Apply to only desktop devices */
@include only-device("desktop") { ... }

/* Apply to devices in a range, in this case, between tablet and large-desktop */
@include between-devices("tablet", "large-desktop") { ... } 
```

不需要每次都从断点中减去值，不需要写很长的语句，没有不必要的冗余，只是一个简单，可理解和流畅的语法，*嘣！*

### 解释代码

好了，有趣的部分来了，是时候一点一点解释代码了:

```
$devices: (
    phone: 480px,
    tablet: 768px,
    desktop: 992px,
    large-desktop: 1200px
); 
```

首先，我们使用 *Sass maps* 的能力来存储我们的值，而不是*变量*，这非常类似于*变量*的例子，我们在这里使用 *maps* 的原因是为了更容易地进行验证和执行流程，我们将在下一段代码中看到更多。

```
@mixin min-device($device: map-get($devices, 'phone')) {
    @if map-has-key($devices, $device) {
        @media screen and (min-width: map-get($devices, $device)) {
            @content;
        }
    }
} 
```

在这里，我们创建一个 mixin 作为`min-width`处理程序，我们接受一个参数并验证该参数是否存在于开始时创建的`$devices` *映射*中，我们使用非常方便的`map-has-key` *函数*来做这件事(我使用映射的原因之一)，然后我们基本上用`@content`将内容添加到媒体查询中，我们对`max-device`、`only-device`、`between-devices`mixin 也做同样的事情，它们之间唯一真正的区别是:

*   **max-device** :从传递的断点中减去 1px，避免属性重叠。
*   【仅 T2】-device:将媒体查询限制在一个范围内，其中传递的参数定义为`min-width`，并且`max-width`是*映射*结构中的下一个键，**示例**:

```
@include only-device("phone") { ... }. 
```

与以下内容相同:

```
@media screen and (min-width: $phone) and (max-width: $tablet - 1px) { ... } 
```

*   **设备间**:将媒体查询限制在一个范围内，第一个参数为`min-width`，第二个参数为`max-width`(同时从`max-width`中减去 1px)。

如你所见，这种方法有许多优点:

1.  **代码简单易读:**你瞬间明白代码里发生了什么。
2.  **目前的范围很明确:**很容易识别给定设备或屏幕尺寸的代码。
3.  非常灵活:如果你不喜欢*设备*的方式，你可以使用*屏幕尺寸*或者你自己的风格。
4.  **伸缩性好:**实现被封装在一个文件中，如果你需要对一个给定的断点进行修改或者添加一个新的断点，只需要修改它的值或者将新的断点添加到*映射*结构中。

好了，我亲爱的开发者们，我想现在就这样了*，我希望我能够帮助你们了解更多关于 CSS 和 SASS 的媒体查询和断点，与你们分享这种方法对我来说非常令人兴奋，如果你有任何关于它的评论，你可以发送到 duranenmanuel@gmail.com[或 twitter](mailto:duranenmanuel@gmail.com) [@duranenmanuel](https://twitter.com/duranenmanuel) 。*

 *下一集见。

最初发布于[Enmascript.com](https://enmascript.com/articles/2018/09/28/another-approach-to-media-queries-with-sass)*
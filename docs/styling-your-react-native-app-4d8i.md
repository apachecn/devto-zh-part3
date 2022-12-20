# 设计您的 React 本机应用程序

> 原文：<https://dev.to/hrastnik/styling-your-react-native-app-4d8i>

在我开始之前，请注意这篇文章不是关于样式的教程。这是我对代码和目录结构的理解，它们支持我创建的应用程序的样式。

每个 React 本地开发人员都有自己处理应用程序样式的方式。没有正确或错误的方法，但有些方法比其他方法有明显的好处。我已经开发了几个应用程序，每个项目都是学习新东西和改进过去错误的机会。有一件事我觉得我做得很好，并且自我开始以来有所提高——造型。所以我决定分享我从开始到现在的旅程——随意复制，或者在下面的评论中批评它。

当我刚开始使用 React Native 时，我有 web 背景，但我没有太多构建大型网站的经验。我建立的大部分东西真的很简单——没有太多复杂性的单页网站。这意味着我总是将所有的 CSS 保存在一个文件中。这种方法虽然还很初级，但对那些网站很有效。因此，当我进入 React Native 时，我很自然地开始将我所有的样式放在一个单独的`style.js`文件中，该文件为所有的屏幕导出样式。这在一段时间内有效，但随着应用程序的增长，文件也在增长，浏览文件开始变得越来越困难。当项目结束时，`style.js`是巨大的，处理项目中的变更感觉像是一件苦差事。这种方法显然必须改变。

对于下一个项目，我想到了一种不同的方法。我没有把所有的样式放在一个单独的`style.js`文件中，而是为每个组件创建了单独的`style.js`文件，并从一个全局的`constants.js`文件中导入了通用的东西，比如颜色。这样做效果好得多，因为现在样式就在组件旁边，我知道在哪里可以找到样式，而不必在巨大的样式表中导航。

这种方法解决了一些问题，但是过了一段时间，我开始考虑如何进一步改进它。我觉得我写了很多多余的代码，尤其是为了分隔组件。我会有很多的`marginTop`和`marginBottom`到处都是，这是没有规则的。有时候上面的组件有一个`marginBottom`，有时候下面的组件有一个`marginTop`。这意味着当我不得不改变一些间距时，我不得不打开`style.js`文件，搜索应用于两个组件的样式，检查哪一个设置了边距并改变它。有时我会删除一些组件，但忘记删除相邻组件的边距，这样间距就太大了。我知道我能以某种方式改善这一点。然后我就想到了！我创建了所有项目中最常用的组件。

[![Nothing](img/521dbe4d542d8d64a7b2ff1edf77e20d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bdd-Gxp0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/v7b0wtyo5ebt851bxfws.gif)

没错！没什么！良好的...不完全是没有。它实际上是一个`<Spacer />`组件，它所做的只是渲染一些空白空间，所以它什么也不是。它可以采用这些大小道具之一- `small`、`medium`、`large`、`extraLarge`，并且在内部呈现一个从全局`constants.js`文件中读取的具有固定间距的正方形`<View />`。下面是它在代码中的样子:

```
 <View style={S.container}>
    <Logo />

    <Spacer extraLarge />

    <TextInput {...usernameProps} />

    <Spacer medium />

    <TextInput {...passwordProps} />

    <Spacer large />

    <Button style={S.submitButton} />
  </View> 
```

Enter fullscreen mode Exit fullscreen mode

所以现在我的大多数屏幕和组件都使用了`<Spacer />`，这意味着我所有的`style.js`不再需要大多数的`marginTop`和`marginBottom`，我的生产力得到了极大的提高——在创建 UI 时，我不必总是在样式表和组件文件之间来回跳转，只是为了在一些元素之间增加一些间距。我可以在这里和那里添加一个`<Spacer />`，并根据需要分隔组件。我得到的另一个好处是，当我查看我的 render()方法时，我可以实际看到组件之间的间距，而不必检查样式表。

我喜欢间隔组件，所以很自然地我开始思考下一步我可以改进什么。我注意到我的`<Text />`组件有很多重复的代码，所以这是接下来要修复的。经过反复试验，我找到了一个我满意的组件。这是它的样子:

```
 <Text weightBold sizeSmall>This text is small and bold</Text>
  <Text sizeLarge colorLight>This text is large and has a light color</Text>
  <Text colorTheme>This text is medium size and has the theme color</Text> 
```

Enter fullscreen mode Exit fullscreen mode

其背后的想法是拥有一个灵活的文本组件来覆盖大多数用例。当需要额外的样式时，我可以添加一个样式道具并进一步定制文本。目前，我的文本组件可以接受 5 个大小属性中的一个，7 个颜色属性中的一个，3 个重量属性中的一个，但是很容易为其他样式属性如行高和间距添加这些布尔属性。

相同的方案也用于`<TextInput />`组件，因为它与本机`<Text />`组件共享许多道具。在文本输入高度、边框半径、选择颜色等方面有一些合理的默认值。，但这通常根据项目需要进行调整。

所以现在我有了我在所有项目中使用的 3 个组件。间隔、文本和文本输入。有了这三个组件，我需要编写的样式代码的数量就大大减少了，而且大部分可以归结为特定于屏幕的布局。

除了定制组件，我还在我的`constants.js`文件中添加了许多有用的常量。主要是颜色、间距和字体大小，但随着项目的发展，我有时会添加边框半径值和阴影。
为了帮助我定义配色方案，我使用了`color` npm 包。它允许操纵颜色，所以一旦我选择了一个主题颜色，不同的色调会自动使用`lighten`、`darken`和`color`包中的其他方法计算出来。

下面是它在代码中的样子:

```
// constants.js

import { Dimensions } from "react-native";
import Color from "color";

const window = Dimensions.get("window");
export const windowWidth = window.width;
export const windowHeight = window.height;

export const colorBackgroundTheme = "rgb(255, 17, 100)";
export const colorBackgroundLight = "rgba(244, 244, 244, 1)";
export const colorBackgroundDark = "rgba(10, 10, 10, 1)";

export const colorBackgroundThemeSoft = Color(colorBackgroundTheme)
  .lighten(0.25)
  .rgb()
  .string(2);
export const colorBackgroundThemeSofter = Color(colorBackgroundTheme)
  .lighten(0.5)
  .rgb()
  .string(2);
export const colorBackgroundThemeHard = Color(colorBackgroundTheme)
  .darken(0.25)
  .rgb()
  .string(2);
export const colorBackgroundThemeHarder = Color(colorBackgroundTheme)
  .darken(0.5)
  .rgb()
  .string(2);

export const colorBackgroundLightDark = Color(colorBackgroundLight)
  .darken(0.25)
  .rgb()
  .string(2);
export const colorBackgroundLightDarker = Color(colorBackgroundLight)
  .darken(0.5)
  .rgb()
  .string(2);

export const colorBackgroundDarkLight = Color(colorBackgroundDark)
  .lighten(0.25)
  .rgb()
  .string(2);
export const colorBackgroundDarkLighter = Color(colorBackgroundDark)
  .lighten(0.5)
  .rgb()
  .string(2);

export const colorTextTheme = "rgba(216, 0, 75, 1)";
export const colorTextLight = "rgba(255, 255, 255, 0.9)";
export const colorTextDark = "rgba(0, 0, 0, 0.9)";

export const colorTextLightSoft = Color(colorTextLight)
  .fade(0.3)
  .rgb()
  .string(2);
export const colorTextLightSofter = Color(colorTextLight)
  .fade(0.5)
  .rgb()
  .string(2);

export const colorTextDarkSoft = Color(colorTextDark)
  .fade(0.3)
  .rgb()
  .string(2);
export const colorTextDarkSofter = Color(colorTextDark)
  .fade(0.5)
  .rgb()
  .string(2);

export const spacingSmall = 4;
export const spacingMedium = 8;
export const spacingLarge = 16;
export const spacingExtraLarge = 32;

export const fontSizeExtraSmall = 8;
export const fontSizeSmall = 12;
export const fontSizeMedium = 16;
export const fontSizeLarge = 20;
export const fontSizeExtraLarge = 24;

export const fontWeightLight = "100";
export const fontWeightNormal = "500";
export const fontWeightBold = "900";

// Can also export borderRadius values, shadows, etc... 
```

Enter fullscreen mode Exit fullscreen mode

```
// Spacer.js
import React from "react";
import { View, StyleSheet } from "react-native";

import { constants as C } from "../../style";

const S = StyleSheet.create({
  spacingSmall: { width: C.spacingSmall, height: C.spacingSmall },
  spacingMedium: { width: C.spacingMedium, height: C.spacingMedium },
  spacingLarge: { width: C.spacingLarge, height: C.spacingLarge },
  spacingExtraLarge: { width: C.spacingExtraLarge, height: C.spacingExtraLarge }
});

const Spacer = ({ small, medium, large, extraLarge }) => {
  let style = S.spacingMedium;
  if (small) style = S.spacingSmall;
  else if (medium) style = S.spacingMedium;
  else if (large) style = S.spacingLarge;
  else if (extraLarge) style = S.spacingExtraLarge;

  return <View style={style} />;
};

export default Spacer; 
```

Enter fullscreen mode Exit fullscreen mode

```
// Text.js

import React from "react";
import { Text as RNText } from "react-native";

import { constants as C } from "../../style";

const Text = ({
  sizeExtraSmall,
  sizeSmall,
  sizeMedium,
  sizeLarge,
  sizeExtraLarge,

  colorTheme,
  colorDark,
  colorDarkSoft,
  colorDarkSofter,
  colorLight,
  colorLightSoft,
  colorLightSofter,

  weightLight,
  weightNormal,
  weightBold,

  style,
  ...props
}) => {
  let fontSize = C.fontSizeMedium;
  if (sizeExtraSmall) fontSize = C.fontSizeExtraSmall;
  else if (sizeSmall) fontSize = C.fontSizeSmall;
  else if (sizeMedium) fontSize = C.fontSizeMedium;
  else if (sizeLarge) fontSize = C.fontSizeLarge;
  else if (sizeExtraLarge) fontSize = C.fontSizeExtraLarge;

  let color = C.colorTextDark;
  if (colorTheme) color = C.colorTextTheme;
  else if (colorDark) color = C.colorTextDark;
  else if (colorDarkSoft) color = C.colorTextDarkSoft;
  else if (colorDarkSofter) color = C.colorTextDarkSofter;
  else if (colorLight) color = C.colorTextLight;
  else if (colorLightSoft) color = C.colorTextLightSoft;
  else if (colorLightSofter) color = C.colorTextLightSofter;

  let fontWeight = C.fontWeightNormal;
  if (weightLight) fontWeight = C.fontWeightLight;
  else if (weightNormal) fontWeight = C.fontWeightNormal;
  else if (weightBold) fontWeight = C.fontWeightBold;

  return <RNText style={[{ fontSize, color, fontWeight }, style]} {...props} />;
};

export default Text; 
```

Enter fullscreen mode Exit fullscreen mode

```
// TextInput.js

import React from "react";
import { TextInput as RNTextInput } from "react-native";

import { constants as C } from "../../style";

const TextInput = ({
  sizeExtraSmall,
  sizeSmall,
  sizeMedium,
  sizeLarge,
  sizeExtraLarge,

  colorTheme,
  colorDark,
  colorDarkSoft,
  colorDarkSofter,
  colorLight,
  colorLightSoft,
  colorLightSofter,

  weightLight,
  weightNormal,
  weightBold,

  style,
  ...props
}) => {
  let fontSize = C.fontSizeMedium;
  if (sizeExtraSmall) fontSize = C.fontSizeExtraSmall;
  else if (sizeSmall) fontSize = C.fontSizeSmall;
  else if (sizeMedium) fontSize = C.fontSizeMedium;
  else if (sizeLarge) fontSize = C.fontSizeLarge;
  else if (sizeExtraLarge) fontSize = C.fontSizeExtraLarge;

  let color = C.colorTextDark;
  if (colorTheme) color = C.colorTextTheme;
  else if (colorDark) color = C.colorTextDark;
  else if (colorDarkSoft) color = C.colorTextDarkSoft;
  else if (colorDarkSofter) color = C.colorTextDarkSofter;
  else if (colorLight) color = C.colorTextLight;
  else if (colorLightSoft) color = C.colorTextLightSoft;
  else if (colorLightSofter) color = C.colorTextLightSofter;

  let fontWeight = C.fontWeightNormal;
  if (weightLight) fontWeight = C.fontWeightLight;
  else if (weightNormal) fontWeight = C.fontWeightNormal;
  else if (weightBold) fontWeight = C.fontWeightBold;

  return (
    <RNTextInput
      selectionColor={C.colorBackgroundThemeSofter}
      style={[
        {
          fontSize,
          color,
          fontWeight,
          padding: C.spacingMedium,
          margin: 0,
          height: 52,
          backgroundColor: C.colorBackgroundLightDark,
          borderRadius: 26
        },
        style
      ]}
      {...props}
    />
  );
};

export default TextInput; 
```

Enter fullscreen mode Exit fullscreen mode
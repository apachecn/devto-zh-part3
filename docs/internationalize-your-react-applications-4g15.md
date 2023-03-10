# 语言无障碍。国际化您的 React 应用程序。

> 原文：<https://dev.to/pretaporter/internationalize-your-react-applications-4g15>

国际化是使应用程序适应不同语言和地区的过程。

那可以带来一些好处。你的目标群体可以比应用默认语言的群体更广泛。因此，通过国际化应用程序，你可能会接触到更多的受众。

> **文化可达性。**国际化不仅仅是翻译文本。用户期望本地化的日期、数字分隔符、货币。甚至颜色在不同的文化中也有不同的含义。在西方文化中，绿色象征着环保，[在伊朗，绿色象征着欢乐，而在中国，绿色象征着耻辱。](https://blog.amplexor.com/globalcontent/en/localization-strategy-translating-logos-across-cultures)

### eo-locale

我想和你们谈谈小，但非常有用的图书馆。它为您提供了格式化数字、日期和管理翻译简单方法。如果你不想读整篇文章，只需查看代码。

首先，您需要在项目中添加库。
`yarn add eo-locale`或`npm i eo-locale --save`

和...就这样，现在您可以格式化值了。

### 货币

许多使用相同货币的国家在显示价格时会有略微不同的格式。用美元、英镑或欧元设定一个价格，然后强迫客户适应的日子早已过去。`EOLocale.Number` -格式数值的组成部分。支持 150 多种语言。

```
import { EOLocale } from 'eo-locale';

<EOLocale.Number
  value={1000}
  currency="EUR"
  maximumFractionDigits={2}
  minimumFractionDigits={2}
  style="currency"
/>
// €1,000.00 
```

Enter fullscreen mode Exit fullscreen mode

### 日期

不同国家/地区对日期和时间表示的法律和文化要求各不相同，因此了解特定国家/地区使用的全数字日历日期的形式以了解日期的含义非常重要。`EOLocale.Date` -格式日期值的组成部分。

```
import { EOLocale } from 'eo-locale';

<EOLocale.Date
  value={new Date(2019, 2, 19)}
  day="numeric"
  month="long"
  year="numeric"
  weekday="long"
 />
 // Tuesday, March 19, 2019 
```

Enter fullscreen mode Exit fullscreen mode

### 文本

网上购物者更喜欢用他们的母语做生意。42%的欧洲人表示只用母语进行网上购物。[另一份报告](https://unbabel.com/blog/localization-cant-read-wont-buy/)发现 72%的非英语母语的网上购物者希望产品评论能以他们的母语发布。

如果你想管理你的翻译，需要更多一点的工作。在应用程序的根目录中添加`EOLocale.Provider`。`eo-locale`基于新的`React.Context` api。

```
import { EOLocale } from 'eo-locale';

const locales = [
  {
    language: 'en',
    messages: {
      hello: 'Hello {name}!'
    }
  },
];

<EOLocale.Provider language="en" locales={locales}>
  <span>
    <EOLocale.Text id="hello" name="World" /> // Hello World!
  </span>
</EOLocale.Provider> 
```

Enter fullscreen mode Exit fullscreen mode

可以在`EOLocale.Text`组件中传递任意名称的 prop。也支持多元化。

```
import { EOLocale } from 'eo-locale';

const locales = [
  {
    language: 'en',
    messages: {
      items: '{count, plural, one {One item} other {{count} items}}'
    }
  },
];

<EOLocale.Text id="items" count={3} />
// 3 items 
```

Enter fullscreen mode Exit fullscreen mode

如果你需要用字符串做一些逻辑，只需导入`use Translator`钩子。

```
import { useTranslator } from 'eo-locale';

function SomeComponent() {
  const translator = useTranslator();

  return <div>{translator.formatNumber(1000)}</div>;
} 
```

Enter fullscreen mode Exit fullscreen mode

您也可以格式化 html 值。GitHub 页面上的更多示例。所有这些都是基于国际化 API 的，现在它很好的支持了 T2。也支持服务器端渲染。我希望这个图书馆能对你的工作有所帮助。尽情享受吧！
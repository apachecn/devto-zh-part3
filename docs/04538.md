# 如何用 i18next 翻译你的 React.js app

> 原文：<https://dev.to/ksushiva/how-to-translate-your-react-js-app-with-i18next-12mn>

今天，我将向您展示如何翻译 React.js 应用程序中的文本和日期。

首先，你必须安装一些依赖项:

```
yarn add i18next i18next-xhr-backend i18next-browser-languagedetector react-i18next 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要在我们的 *src* 文件夹:
中创建一个名为 **i18n.js** 的文件

```
import i18n from 'i18next';
import Backend from 'i18next-xhr-backend';
import LanguageDetector from 'i18next-browser-languagedetector';
import { initReactI18next } from 'react-i18next';

const fallbackLng = ['en']; 
const availableLanguages = ['en', 'ru'];

i18n
  .use(Backend) // load translation using xhr -> see /public/locales. We will add locales in the next step

  .use(LanguageDetector) // detect user language

  .use(initReactI18next) // pass the i18n instance to react-i18next.

  .init({
    fallbackLng, // if user computer language is not on the list of available languages, than we will be using the fallback language specified earlier
    debug: true,
    whitelist: availableLanguages,

    interpolation: {
      escapeValue: false
    },
  });

export default i18n; 
```

Enter fullscreen mode Exit fullscreen mode

然后我们将 **index.js** 中的 i18n 导入 *src/index.js* :

```
...
import './i18n';

ReactDOM.render(<App />, document.getElementById('root'));
... 
```

Enter fullscreen mode Exit fullscreen mode

下一步是创建我们的语言环境:*public/locales/en/translation . JSON*和*public/locales/ru/translation . JSON*。由于 i18next-xhr-backend，这个翻译将被自动加载。

此外，在我们的 *App.js* 中，我们需要添加悬念以显示加载指示器。

```
import React, { Suspense } from 'react';
...
function App() {
  return (
    <div className="App">
      <Suspense fallback={(<div>Loading</div>)}>
      <WeatherForecast /> 
      </Suspense>
    </div>
  );
}
... 
```

Enter fullscreen mode Exit fullscreen mode

现在我们转到我们想要翻译的组件。如果你有一个类组件，我们将使用高阶组件**和翻译** :

```
import React, { Component } from 'react';
import { withTranslation } from 'react-i18next';

class News extends Component {

   render(){
     const { t } = this.props;

     return (
       <h1>{t('news.title')}</h1>
     )
   };
};

export default withTranslation()(News); 
```

Enter fullscreen mode Exit fullscreen mode

如果你有一个功能组件，你应该使用 react 钩子**使用翻译** :

```
import React from 'react';
import { useTranslation } from 'react-i18next';

const WeatherForecast = () => {
  const { t } = useTranslation();
  return (
    <h1>{t('weather.title')}</h1>
  )
};

export default WeatherForecast; 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们将修改我们的语言环境。在*public/locales/en/translation . JSON*:

```
{  "news":  {  "title":  "Our news"  },  "weather":  {  "title":  "Weather forecast"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

在*public/locales/ru/translation . JSON*:

```
{  "news":  {  "title":  "Наши новости"  },  "weather":  {  "title":  "Прогноз погоды"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

如果你想翻译一个包含变量的短语，你需要这样做:

```
 <p>{t('weather.temprature', {number: temprature})}</p> 
```

Enter fullscreen mode Exit fullscreen mode

在你的。需要编写的 json 区域设置文件:

```
 "temprature":  "Today in Lyon is cloudy: {{number}}°C." 
```

Enter fullscreen mode Exit fullscreen mode

我们将为我们的俄语或任何其他版本做同样的事情。

现在我将向你展示如何翻译日期。为此，我将使用 moment.js.

```
yarn add moment 
```

Enter fullscreen mode Exit fullscreen mode

在我的 **WeatherForecast.js** 里我加了:

```
<p>{t('weather.date', {date: Date.now()})}</p> 
```

Enter fullscreen mode Exit fullscreen mode

现在在*public/locales/en/translation . JSON*:

```
"weather":  {  "date":  "Today's date: {{date, currentDate}}"  }, 
```

Enter fullscreen mode Exit fullscreen mode

这里的`{{}}` *日期*是**值**，我们在组件中指定的变量*当前日期*是我们接下来要使用的**格式**。

在我们的 i18n.js 文件中，我们必须导入 moment.js 并为我们的日期配置翻译:

```
import moment from 'moment';

...

availableLanguages.forEach((element) => {
  if (element !== 'en') {
     import(`moment/locale/${element}`);
   }
}); // we are importing only the locales that we need.

.init({
    ...
    interpolation: {
      escapeValue: false, 
       format: (value, format, lng) => {
        if (format === 'currentDate') return 
           moment(value).locale(lng).format('LL');
          return value;
       },//if the format is 'currentDate', then take its __value__ transfom it to a moment object, translate it to the current language and show it in Month DD, YYYY format. 
    },
  }); 
```

Enter fullscreen mode Exit fullscreen mode

应该很好用。希望这篇文章是有帮助的。
# 使用 typeOf 从数据中获取类型

> 原文：<https://dev.to/andreasbergqvist/typescript-get-types-from-data-using-typeof-4b9c>

确保为变量设置的值与预期值相匹配的一个好方法是为它们设置特定的类型。

如果已经有了对象或数组中的数据。下面的方法是创建你的类型的好方法！

假设您有以下数据:

```
const data = {
  value: 123,
  text: 'text'
}; 
```

然后你可以使用
创建一个基于它的类型

```
type Data = typeof data;
// type Data = {
//   value: number;
//   text: string;
// } 
```

您可以对嵌套对象进行同样的操作:

```
const data = {
  value: 123,
  text: 'text',
  subData: {
    value: false
  }
};
type Data = typeof data;
// type Data = {
//   value: number;
//   text: string;
//   subData: {
//     value: boolean;
//   };
// } 
```

从 TypeScript 3.4 开始，如果你有一个字符串数组(注意`as const` ):

```
const data = ['text 1', 'text 2'] as const;
type Data = typeof data[number];
// type Data = "text 1" | "text 2" 
```

也可以从带有对象的数组中获取类型:

```
const locales = [
  {
    locale: 'se',
    language: 'Swedish',
  },
  {
    locale: 'en',
    language: 'English',
  }
] as const;
type Locale = typeof locales[number]['locale'];
// type Locale = "se" | "en" 
```

也可以从键中获取类型:

```
const currencySymbols = {
  GBP: '£',
  USD: '$',
  EUR: '€',
}
type CurrencySymbol = keyof typeof currencySymbols;
// type CurrencySymbol = "GBP" | "USD" | "EUR" 
```

关于`as const` vs 使用`<const>`的通知。它们的工作原理是一样的，但是`<const>`会在一个. tsx 文件中失败(React)。

```
const data = ['text 1', 'text 2'] as const;
// is the same as
const data = <const>['text 1', 'text 2']; 
```
# 停止将您的 process.env.NODE_ENV 复制到局部变量！🤯

> 原文：<https://dev.to/lgraziani2712/stop-copying-your-process-env-vars-to-local-variables-3jen>

> 封面图片来自
> [https://www . national geographic . com/photography/proof/2017/06/bird-gallery/](https://www.nationalgeographic.com/photography/proof/2017/06/bird-gallery/)

## TL；速度三角形定位法(dead reckoning)

而不是这个:

*   例子

```
const localAndLessCode = process.env.NODE_ENV;

if (localAndLessCode !== 'production') {
  // do dev-only things

  console.warn('[dev-only] Warnings to help devs!');
}

export function anotherFunc(num) {
  if (localAndLessCode !== 'production' && typeof num !== 'number') {
    console.warn('[dev-only] Warnings to help devs!');
  }

  // Do something
} 
```

请执行以下操作:

*   例子

```
if (process.env.NODE_ENV !== 'production') {
  // do dev-only things

  console.warn('[dev-only] Warnings to help devs!');
}

export function anotherFunc(num) {
  if (process.env.NODE_ENV !== 'production' && typeof num !== 'number') {
    console.warn('[dev-only] Warnings to help devs!');
  }

  // Do something
} 
```

## 为什么？

像 babel 这样的工具会用它的值替换每一个`process.env.NODE_ENV`(以及任何其他的`proces.env`变量)。然后，上面的例子将被转换成这样:

*   例子

```
const localAndLessCode = 'production';

if (localAndLessCode !== 'production') {
  // do dev-only things

  console.warn('[dev-only] Warnings to help devs!');
}

export function anotherFunc(num) {
  if (localAndLessCode !== 'production' && typeof num !== 'number') {
    console.warn('[dev-only] Warnings to help devs!');
  }

  // Do something
} 
```

*   例子

```
if ('production' !== 'production') {
  // do dev-only things

  console.warn('[dev-only] Warnings to help devs!');
}

export function anotherFunc(num) {
  if ('production' !== 'production' && typeof num !== 'number') {
    console.warn('[dev-only] Warnings to help devs!');
  }

  // Do something
} 
```

如您所见，在第二个示例中，两个比较都是静态的。像 webpack 的`TerserPlugin`或`babel-minify`这样的丑化者会利用这一点来删除那些 if(和它们的内容！).

### 免费！

这真的很酷，因为你不需要配置任何东西来减少你的包的大小，你可以在你的代码中*修正*！它将帮助你减少*大量*你不想在产品构建中使用的代码！
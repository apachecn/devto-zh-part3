# 紧凑还是冗长的代码样式？

> 原文：<https://dev.to/f1lt3r/compact-or-verbose-codestyle-30dd>

你更喜欢哪种代码风格，为什么？

## 紧凑

```
const get = (store, key) => 
    Reflect.has(window[store], key) && 
        JSON.parse(window[store][key]) ||
        new Error(`Key "${key}" not found in window.${store}.`); 
```

## 啰嗦

```
const get = (store, key) => {
    if (!Reflect.has(window[store], key)) {
        return new Error(`Key "${key}" not found in window.${store}.`);
    }

    const jsonStr = window[store][key]
    const dataObj = JSON.parse(jsonStr);
    return dataObj;
}; 
```
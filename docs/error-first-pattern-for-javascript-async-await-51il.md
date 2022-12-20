# JavaScript 异步/等待的第一个模式出错

> 原文：<https://dev.to/daleljefferson/error-first-pattern-for-javascript-async-await-51il>

处理错误并不有趣。在本文中，我将向您展示一种处理 Async/Await 错误的模式。这种模式避免了使用 try/catch。

```
// Callback
fetch(URL, (error, response) => ...)

// Promise
fetch(URL).then((response) => ...).catch((error) => {})

// Async / await
try { fetch(URL) } catch (error) {} 
```

注意，在回调时，你别无选择，只能处理错误，或者至少为没有处理它而感到内疚。

## 问题

```
async fetchData() {
    let json;

    try {
        const response = await fetch(URL)
        try {
             json = await response.json()
        } catch (error) {
             console.log("Parsing failed", error)
             return
        }
    } catch (error) {
        console.log("Request failed", error)
        return
    }

    const stars = json.stargazers_count
    this.setState({stars})
} 
```

**呸**，我希望你和我一样感到厌恶，例外应该是例外，移动设备上的连接问题没有例外，这是正常的控制流。

> 转到被认为有害的陈述 *(Edsger Dijkstra - 1968)*

我相信 try / catch 是一个美化了的 Go To 语句，执行停止，控制跳转到 catch 块。对于非正常控制流的异常，应该谨慎使用它。

所以我们已经从显式返回错误到抛出异常，这听起来不像是进步。

## 一种可能的解决方案

```
async fetchData() {
    const [responseError, response] = await fetch(URL);

    if (responseError || !response) {
        console.log("Request failed", responseError)
        return
    }

    const [parseError, json] = await response.json()

    if (parseError || !json) {
        console.log("Request failed", parseError)
        return
    }

    const stars = json.stargazers_count
    this.setState({stars})
} 
```

这使用了数组析构，因为 error 是第一个值，它让你考虑处理错误。我混合了 Go 错误处理和 NodeJS 错误优先回调。

## 实现细节

```
const errorFirstPromise = promise => {
  return new Promise(resolve => {
    return promise
      .then(result => {
        return resolve([null, result]);
      })
      .catch(error => {
        return resolve([error, null]);
      });
  });
}; 
```
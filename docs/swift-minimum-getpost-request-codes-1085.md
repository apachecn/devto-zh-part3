# Swift 最小获取/发布请求代码

> 原文：<https://dev.to/mishimay/swift-minimum-getpost-request-codes-1085>

复制并粘贴这些代码，然后开始制作应用程序！

## 获取代码

```
let url = URL(string: "https://httpbin.org/get")!
let task = URLSession.shared.dataTask(with: url) { (data, response, error) in
    if let error = error {
        print("error: \(error)")
    } else {
        if let response = response as? HTTPURLResponse {
            print("statusCode: \(response.statusCode)")
        }
        if let data = data, let dataString = String(data: data, encoding: .utf8) {
            print("data: \(dataString)")
        }
    }
}
task.resume() 
```

## 邮政编码

```
let url = URL(string: "https://httpbin.org/post")!
var request = URLRequest(url: url)
request.httpMethod = "POST"
let task = URLSession.shared.dataTask(with: request) { (data, response, error) in
    if let error = error {
        print("error: \(error)")
    } else {
        if let response = response as? HTTPURLResponse {
            print("statusCode: \(response.statusCode)")
        }
        if let data = data, let dataString = String(data: data, encoding: .utf8) {
            print("data: \(dataString)")
        }
    }
}
task.resume() 
```

## 雨燕版

```
Apple Swift version 4.2 (swiftlang-1000.11.37.1 clang-1000.11.45.1)
Target: x86_64-apple-darwin18.2.0 
```
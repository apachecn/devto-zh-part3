# 从 JavaScript 与 Python 对话(然后再回来！)

> 原文：<https://dev.to/healeycodes/talking-to-python-from-javascript-and-back-again-31jf>

许多初学者都很难理解在不同编程语言之间传递数据的概念。理解值存在于变量中要简单得多，变量可以在函数之间传递。然而，为了超越程序的边缘，我们必须以某种方式序列化我们的数据。我们将看看这两种语言交流的两种方式。AJAX 请求通过新的 Fetch API，以及本地进程之间的管道。

序列化数据意味着获取一个值、对象或数据结构，并将其转换为可以存储或传输的格式。最重要的是，它需要在另一端重新组合起来。让我们看看 JavaScript 对象符号(JSON)。JSON 是一种人类可读的格式，对于机器的读写来说很简单。[规格](https://www.json.org/)小到可以在一杯咖啡中读取。JavaScript 和 Python 都有标准的库方法来解析和编写 JSON。

## JSON 速成班

JSON 建立在两种数据结构之上。**对象** —像 JavaScript `Object`和 Python `Object`或`Dictionary`这样的键/值对。**数组**——一系列数据，如 JavaScript `Array`和 Python `List`。

```
/* JavaScript
   Try this out in your developer console! */

var person = {"name":"Andrew", "loves":"Open Source"};
var asJSON = JSON.stringify(person);

// `person` is of type 'object'
console.log(`person is of type ${typeof person}`);

// `asJSON` is of type 'string'
console.log(`asJSON is of type ${typeof asJSON}`);

// We can convert it back to an object by parsing it
// `asObject` is of type 'object'
var asObject = JSON.parse(asJSON);
console.log(`asObject is of type ${typeof asObject}`); 
```

让我们通过使用标准库模块`json`在 Python 中做同样的事情。

```
# python 
animal = {'type':'cat', 'age':12}
as_json = json.dumps(animal)

print(type(animal))  # prints '<class 'dict'>' print(type(as_json))  # prints '<class 'str'>' 
# now back again as_object = json.loads(as_json)
print(type(as_object))  # prints '<class 'dict'>' 
```

**重述:**在 JavaScript 中，你用`JSON.stringify()`序列化到 JSON，用`JSON.parse()`解析。在 Python 中，首先导入`json`模块，然后用`json.dumps()`序列化，用`json.loads()`解析。

## 通过 AJAX 交谈

在过去，这将通过 [XMLHttpRequest](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) 来完成，但是相对较新的 [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) 使用起来要舒服得多。首先，我们将编写一个小型 Python web 服务器，然后我们将看看如何在浏览器中来回传递 JSON。

[烧瓶](http://flask.pocoo.org/)是一个‘微框架’。它不仅使用起来非常有趣，而且作为原型也非常棒。我们将使用它的 [jsonify](http://flask.pocoo.org/docs/1.0/api/#flask.json.jsonify) 模块，该模块编写/解析 JSON 并设置正确的响应头(一个应用程序/json mime 类型)。它需要两个特定于操作系统的命令来安装和运行一个调试服务器。对 OS X 的我来说，它们是`pip install flask`和`FLASK_APP=app.py flask run`。

沿着这个库编写代码或者派生[这个库](https://github.com/healeycodes/talking-between-python-and-js)来获取所有例子的代码。

```
# app.py from flask import Flask, jsonify, request, render_template
app = Flask(__name__)

@app.route('/hello', methods=['GET', 'POST'])
def hello():

    # POST request
    if request.method == 'POST':
        print('Incoming..')
        print(request.get_json())  # parse as JSON
        return 'OK', 200

    # GET request
    else:
        message = {'greeting':'Hello from Flask!'}
        return jsonify(message)  # serialize and use JSON headers 
@app.route('/test')
def test_page():
    # look inside `templates` and serve `index.html`
    return render_template('index.html') 
```

随着我们的服务器运行并为我们提供一个可以运行 JavaScript 的页面，让我们用 JSON 来说吧！我们将使用 Fetch API 发送一个 GET 请求，并接收来自 Flask 的问候。在编写一行客户端代码之前，我总是使用 [Postman](https://www.getpostman.com/) 来测试我的 web 服务器——它是免费的，是 API 测试和开发的行业标准工具之一。

我在`templates/index.html`内的`<script>`标签中运行以下代码片段。`index.html`里面没有其他东西，所以呈现的页面是空白的。一切都发生在控制台中。

```
// GET is the default method, so we don't need to set it
fetch('/hello')
    .then(function (response) {
        return response.text();
    }).then(function (text) {
        console.log('GET response text:');
        console.log(text); // Print the greeting as text
    });

// Send the same request
fetch('/hello')
    .then(function (response) {
        return response.json(); // But parse it as JSON this time
    })
    .then(function (json) {
        console.log('GET response as JSON:');
        console.log(json); // Here’s our JSON object
    }) 
```

厉害！我们让 Python 使用 JSON 与客户端 JavaScript 进行数据序列化。让我们翻转一下，从浏览器发送 JSON 到 Python。我们将再次使用 Fetch API，但这将是一个 POST 请求，而不是 GET。

**初学者提示:**记住 POST 和 GET 的区别。当你邮寄邮件时，你带着装满信息的信去邮局。当你收到邮件时，你再次前往邮局，但这一次你拿的是留给你的东西。

```
// POST
fetch('/hello', {

    // Specify the method
    method: 'POST',

    // JSON
    headers: {
        'Content-Type': 'application/json'
    },

    // A JSON payload
    body: JSON.stringify({
        "greeting": "Hello from the browser!"
    })
}).then(function (response) { // At this point, Flask has printed our JSON
    return response.text();
}).then(function (text) {

    console.log('POST response: ');

    // Should be 'OK' if everything was successful
    console.log(text);
}); 
```

有了这两个核心构件，我们就征服了通过 HTTP 的 JSON 通信。但是，请注意，你应该在这些承诺的末尾加上`catch`。我只是为了清晰起见才修剪的。最好优雅地处理错误，这样我们就可以告诉用户他们断开连接了，或者我们这边有错误。在这里看[的文件。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch)

如果您使用 Python 与 Node.js web 服务器对话，您可能会接触到[请求](http://docs.python-requests.org/en/master/)模块，它的语法与 Fetch API 几乎相同。

## 通过流程交谈

我们将生成进程(两种方式)，这样我们就可以看到 Node.js 和 Python 之间的通信是什么样子。我们将在两个实例中监听子进程的 [stdout](https://en.wikipedia.org/wiki/Standard_streams#Standard_output_(stdout)) 流。假设我们有一个不定期报告数据的程序。温度传感器。我们想要监听那个程序并存储它报告的值。

这是我们用 Python 编写的假传感器程序。它将数据输出到标准输出。我们将在 Node.js.
中捕捉它

```
# sensor.py 
import random, time
while True:
    time.sleep(random.random() * 5)  # wait 0 to 5 seconds
    temperature = (random.random() * 20) - 5  # -5 to 15
    print(temperature, flush=True, end='') 
```

当以这种方式传输信息时，重要的是刷新流，以便它在您期望的时候到达 stdout([)为什么我们在 C 中要刷新()。](https://www.quora.com/Why-do-we-use-the-functions-fflush-stdin-and-fflush-stdout-in-c))。更多关于 Python flushing [的信息点击这里](https://stackoverflow.com/a/35467658)。我们还确保打印出来的语句末尾不包含额外的信息(即使`parseFloat()`会清除它！)默认情况下，它是换行符`\n`。

因此，我们是 Node.js，我们需要报告的当前温度。让我们将`sensor.py`生成为一个进程，并监听 stdout 事件。*管道*两种运行语言之间的数据。

```
// temperature-listener.js

const { spawn } = require('child_process');
const temperatures = []; // Store readings

const sensor = spawn('python', ['sensor.py']);
sensor.stdout.on('data', function(data) {

    // Coerce Buffer object to Float
    temperatures.push(parseFloat(data));

    // Log to debug
    console.log(temperatures);
}); 
```

## 翻转过来，反过来

现在，让我们交换一下角色。Node.js 传感器和 Python 监听器！这一次我们将尝试不同的方法，使用换行符(`\n`)来分隔不同的读数，而不是等待一个事件。我们将把数据添加到一个缓冲区中，直到遇到一个换行符。一旦我们这样做了，我们就收集了一个完整的读数，我们可以存储它。

首先，Node.js.
中的等效传感器

```
// sensor.js

function reportReading() {
    const temperature = (Math.random() * 20) - 5; // Range of -5 to 15
    process.stdout.write(temperature + '\n'); // Write with newline char
    setTimeout(reportReading, Math.random() * 5000); // Wait 0 to 5 seconds
}
reportReading(); 
```

现在在 Python 中，一个温度监听器程序将产生上述代码作为一个进程。

```
# temperature-listener.py 
import sys
from subprocess import Popen, PIPE

temperatures = []  # store temperatures sensor = Popen(['node', 'sensor.js'], stdout=PIPE)
buffer = b''
while True:

    # read sensor data one char at a time
    out = sensor.stdout.read(1)

    # after a full reading..
    if out == b'\n':
        temperatures.append(float(buffer))
        print(temperatures)
        buffer = b''
    else:
        buffer += out  # append to buffer 
```

你可以运行`node temperature-listener.js`或者`python temperature-listener.py`，结果是一样的。温度数组将随着新数据的到来而增长，我们的调试日志行将产生以下输出。

```
[ 3.8075910850643098 ]
[ 3.8075910850643098, -1.5015912681923482 ]
[ 3.8075910850643098, -1.5015912681923482, 11.97817663641078 ] 
```

我们已经看到了 Python 和 JavaScript 之间两种不同的通信方式，但是如果这两种方式都不适合您，请不要担心！有许多方法可以在这两种语言之间传递数据。不限于:[命名管道](https://en.wikipedia.org/wiki/Named_pipe)，TCP 套接字，WebSockets，文件轮询。

* * *

加入我的关于编程和个人成长的[时事通讯](https://buttondown.email/healeycodes)的 150 多人注册！

我发关于科技的微博。
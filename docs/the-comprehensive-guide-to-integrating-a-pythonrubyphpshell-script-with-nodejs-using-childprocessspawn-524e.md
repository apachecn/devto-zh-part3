# 使用 child_process.spawn 将 Python/Ruby/PHP/shell 脚本与 Node.js 集成的综合指南

> 原文：<https://dev.to/hugo__df/the-comprehensive-guide-to-integrating-a-pythonrubyphpshell-script-with-nodejs-using-childprocessspawn-524e>

有时从 Node.js 运行 Python/Ruby/PHP shell 脚本是必要的。这篇文章着眼于利用 child_process.spawn 封装 Node.js/JavaScript.调用的最佳实践

这里的目标是在 Node.js 和外壳之间有一个互操作层。如果系统的其他部分不是用 JavaScript 开发的，这是一个快速的解决方法。

我们将使用`spawn`而不是`exec`,因为我们讨论的是传递数据，而且可能是大量的数据。要了解`child_process.spawn`和`child_process.exec`的区别(参见“[node . js child _ process 的 spawn 和 exec 的区别](https://www.hacksparrow.com/difference-between-spawn-and-exec-of-node-js-child_process.html)”)。

它的长与短用于使用缓冲接口的少量数据(200k 以下)的`exec`和使用流接口的大量数据的`spawn`。

对于我们将要看到的一些用例，有一个更详细的语法，但是它更适合与 Ruby/Python/PHP 集成，因为我们可能会得到比几行文本更多的数据。

完整的例子[github.com/HugoDF/node-run-python](https://github.com/HugoDF/node-run-python)。

目录:

*   [调用一个 shell 命令并记录下来](#call-a-shell-command-and-log-it)
*   [调用 Python 获取其版本](#call-python-for-its-version)
*   [从节点](#call-a-python-script-from-node)调用 Python 脚本
*   [使用 child_process.spawn 从 Node.js 向 Python 脚本传递参数](#pass-arguments-to-a-python-script-from-node-js-using-child-process-spawn)
*   [从 Node.js 读取 child_process.spawn 输出](#read-child-process-spawn-output-from-node-js)
*   [处理来自 child_process.spawn 的错误](#handle-errors-from-child-process-spawn)
*   [将结构化数据从 Python/Ruby 传递到 Node.js/JavaScript](#pass-structured-data-from-python-ruby-to-node-js-javascript)

以下示例包含两个部分。

实际运行 shell 命令的部分，通常是一个名为`run`的函数。它们还包含一个实际调用 ie 的 IIFE(“立即调用的函数表达式”)。`(async () => { await run() })()`。这种生活是由 async/await 支持的一种很好的模式(参见 [Async JS:历史、模式和陷阱](https://codewithhugo.com/async-js/#async-await)),但它只是出于说明的目的，因为它代表了从应用程序的另一部分对包装的`spawn`调用的调用。

## 调用 shell 命令并记录

在这种情况下使用`spawn`是多余的，因为 echo 只会返回传递给它的内容。

这个例子非常简单明了，展示了如何使用`child_process.spawn`来“解释”和读回数据。

`spawn`将要调用的可执行文件作为第一个参数，并可选地将该可执行文件的选项/参数数组作为第二个参数。

```
const { spawn } = require('child_process');

function run() {
  const process = spawn('echo', ['foo']);
  process.stdout.on(
    'data',
    (data) => console.log(data.toString())
  );
}

(() => {
  try {
    run()
    // process.exit(0)
  } catch (e) {
    console.error(e.stack);
    process.exit(1);
  }
})(); 
```

```
 $ node run.js

foo 
```

## 称 Python 为其版本

我们将快速展示如何用 python 做类似于上面的事情。再次注意`--version`是如何在数组内部传递的。

我们还创建了一个很好的记录器来区分 stdout 和 stderr，并绑定到它们。由于 spawn 返回一个具有`stdout`和`stderr`事件发射器的实例，我们可以使用`.on('data', () => { /* our callback function */ })`将`logOutput`函数绑定到`'data'`事件。

另一个有趣的花絮是`python` `--` `version`输出版本到`stderr`。关于*NIX 可执行文件是否在成功/错误时使用退出代码、stderr 和 stdout 的不一致是一个怪癖，我们在将 Python/Ruby/other 与 Node.js 集成时必须牢记在心。

```
const { spawn } = require('child_process')

const logOutput = (name) => (data) => console.log(`[${name}] ${data.toString()}`)

function run() {
  const process = spawn('python', ['--version']);

  process.stdout.on(
    'data',
    logOutput('stdout')
  );

  process.stderr.on(
    'data',
    logOutput('stderr')
  );
}

(() => {
  try {
    run()
    // process.exit(0)
  } catch (e) {
    console.error(e.stack);
    process.exit(1);
  }
})();

Output: 
```

sh
$ node run.js

[stderr] Python 2.7.13

```
 ## Call a Python script from Node

We’ll now run a fully-fledged Python script (although it could just as well be Ruby, PHP, shell etc.) from Node.js.

This is `script.py`, it just logs out `argv` (the “argument vector”, ie. `['path/to/executable', /* command line arguments]`) 
```

py
导入系统

print(sys.argv)

```
 Like in the previous example, we’ll just call spawn with `python` with the path to the Python script (`./script.py`) in the second parameter.

Here comes another gotcha of integrating scripts in this fashion. In this example, the path to the script is based on the working directory from which `node` is called.

There are workaround of course using the `path` module and `__dirname`, which for example could resolve a `other-script.py` co-located with the JavaScript file/Node module calling `spawn` using: `require('path').resolve(__dirname, './other-script.py')`. 
```

js
const { spawn } = require(' child _ process ')

const 登录输出=(名称)= >(数据)=> console.log( `[${name}] ${data.toString()}`)

函数 run(){
const process = spawn(' python '，[')。/script . py '])；

process.stdout.on(
'data '，
logout output(' stdout ')
)；

process.stderr.on(
'data '，
logout output(' stderr ')
)；
}

(()= > {
try {
run()
//process . exit(0)
} catch(e){
console . error(e . stack)；
process . exit(1)；
}
})()；

```
 Output: 
```

嘘

$ node run.js

[stdout] ['./script.py']

```
 ## Pass arguments to a Python script from Node.js using child\_process.spawn

The next step of integration is to be able to pass data from the Node/JavaScript code to the Pytonh script.

In order to do this, we’ll just passed more shell arguments using the arguments array (second parameter to `spawn`). 
```

js
const { spawn } = require(' child _ process ')

const 登录输出=(名称)= >(数据)=> console.log( `[${name}] ${data.toString()}`)

函数 run(){
const process = spawn(' python '，[')。/script.py '，' my '，' args '])；

process.stdout.on(
'data '，
logout output(' stdout ')
)；

process.stderr.on(
'data '，
logout output(' stderr ')
)；
}

(()= > {
try {
run()
//process . exit(0)
} catch(e){
console . error(e . stack)；
process . exit(1)；
}
})()；

```
 Our `script.py` will also just log out the `argv` except the first element (which is the path to the script). 
```

py
导入系统

print(sys.argv)[1:]

```
 Here’s the output: 
```

sh
$ node run.js

[stdout] ['my', 'args']

```
 ## Read child\_process.spawn output from Node.js

It’s nice to be able to pass data down to the Python script, but we’re still not able to get the data from the Python script back in a format that we’re able to leverage in our Node.js/JavaScript application.

The solution to this is to wrap the whole `spawn` -calling function into a Promise. This allows us to decide when we want to `resolve` or `reject`.

To keep track of the Python script’s output stream(s), we manually buffer the output using arrays (one for `stdout` and another for `stderr`).

We also add a listener for `'exit'` using `spawn().on('exit', (code, signal) => { /* probably call resolve() */ })`. This is where we will tend to `resolve`/`reject` the Promise’s value(s) from the Python/Ruby/other script. 
```

js
const { spawn } = require(' child _ process ')

const 登录输出=(名称)= >(数据)=> console.log( `[${name}] ${data}`)

函数 run() {
返回新的承诺((resolve，reject)=>{
const process = spawn(' python '，[')。/script.py '，' my '，' args '])；

```
const out = []
process.stdout.on(
  'data',
  (data) => {
    out.push(data.toString());
    logOutput('stdout')(data);
  }
);

const err = []
process.stderr.on(
  'data',
  (data) => {
    err.push(data.toString());
    logOutput('stderr')(data);
  }
);

process.on('exit', (code, signal) => {
  logOutput('exit')(`${code} (${signal})`)
  resolve(out);
}); 
```

});
}

(async()= > {
try {
const output = await run()
logout put(' main ')(output)
process . exit(0)
} catch(e){
console . error(e . stack)；
process . exit(1)；
}
})()；

```
 Output: 
```

$ node run.js

[stdout] ['my '，' args']
[main] ['my '，' args']

```
 ## Handle errors from child\_process.spawn

Next up we need to handle errors from the Python/Ruby/shell script at the Node.js/JavaScript level.

The main way that a \*NIX executable signals that it errored is by using a `1` exit code. That’s why the `.on('exit'` handler now does a check against `code === 0` before deciding whether to resolve or reject with value(s). 
```

js
const { spawn } = require(' child _ process ')

const 登录输出=(名称)= >(数据)=> console.log( `[${name}] ${data}`)

函数 run() {
返回新的承诺((resolve，reject)=>{
const process = spawn(' python '，[')。/script.py '，' my '，' args '])；

```
const out = []
process.stdout.on(
  'data',
  (data) => {
    out.push(data.toString());
    logOutput('stdout')(data);
  }
);

const err = []
process.stderr.on(
  'data',
  (data) => {
    err.push(data.toString());
    logOutput('stderr')(data);
  }
);

process.on('exit', (code, signal) => {
  logOutput('exit')(`${code} (${signal})`)
  if (code === 0) {
    resolve(out);
  } else {
    reject(new Error(err.join('\n')))
  }
}); 
```

});
}

(async()= > {
try {
const output = await run()
logout output(' main ')(output)
process . exit(0)
} catch(e){
console . Error('脚本执行时出错'，e . stack)；
process . exit(1)；
}
})()；

```
 Output: 
```

sh
$ node run.js

[stderr] Traceback(最近一次调用 last):
File "。/script.py "，第 3 行，在
print(sy . argv)[1:]
name 错误:名称' sy '未定义

脚本执行期间出错错误:Traceback(最近一次调用 last):
File "。/script.py "，第 3 行，在
print(sy . argv)[1:]
name 错误:名称' sy '未定义

```
at ChildProcess.process.on (/app/run.js:33:16)
at ChildProcess.emit (events.js:182:13)
at Process.ChildProcess._handle.onexit (internal/child_process.js:240:12) 
```

```
 ## Pass structured data from Python/Ruby to Node.js/JavaScript

The final step to full integration between Ruby/Python/PHP/shell scripts and our Node.js/JavaScript application layer is to be able to pass structured data back from the script up to Node.js/JavaScript.

The simplest structured data format that tends to be available in both Python/Ruby/PHP and Node.js/JavaScript is JSON.

In the Python script, we print the `json.dumps()` output of a dictionary, see `script.py`: 
```

py
导入系统
导入 json

send _ message _ back = {
' arguments ':sys . argv[1:]，
'message ':" " "您好，
这是我的消息。

“走向世界”

print(JSON . dumps(send _ message _ back))

```
 In Node, we add some JSON-parsing logic (using `JSON.parse`) in the `'exit'` handler.

A gotcha at this point is if, for example `JSON.parse()` fails due to badly-formed JSON, we need to propagate that error up, hence the try/catch where the `catch` clause `reject`-s the potential error: `try { resolve(JSON.parse(out[0])) } catch(e) { reject(e) }`. 
```

js
const { spawn } = require(' child _ process ')

const 登录输出=(名称)= >(消息)=> console.log( `[${name}] ${message}`)

函数 run() {
返回新的承诺((resolve，reject)=>{
const process = spawn(' python '，[')。/script.py '，' my '，' args '])；

```
const out = []
process.stdout.on(
  'data',
  (data) => {
    out.push(data.toString());
    logOutput('stdout')(data);
  }
);

const err = []
process.stderr.on(
  'data',
  (data) => {
    err.push(data.toString());
    logOutput('stderr')(data);
  }
);

process.on('exit', (code, signal) => {
  logOutput('exit')(`${code} (${signal})`)
  if (code !== 0) {
    reject(new Error(err.join('\n')))
    return
  }
  try {
    resolve(JSON.parse(out[0]));
  } catch(e) {
    reject(e);
  }
}); 
```

});
}

(async()= > {
try {
const output = await run()
logout put(' main ')(output . message)
process . exit(0)
} catch(e){
console . Error('脚本执行过程中出错'，e . stack)；
process . exit(1)；
}
})()；

```
 Output 
```

$ node run.js

[stdout] {"message ":"你好，\ n 这是我的消息。\ n \ nTo the world "，" arguments": ["my "，" args"]}

【主】您好，
这是我的留言。

完全地

```
 I’ve got mentoring spots open at [https://mentorcruise.com/mentor/HugoDiFrancesco/](https://mentorcruise.com/mentor/HugoDiFrancesco/), so do that if you want Node.js/JavaScript/career mentoring or feel free to tweet at me [@hugo\_\_df](https://twitter.com/hugo__df)

[unsplash-logo
<path d="M10 9V0h12v9H10zm12 5h10v18H0V14h10v9h12v-9z"></path>Elaine Casap](https://unsplash.com/@ecasap?utm_medium=referral&utm_campaign=photographer-credit&utm_content=creditBadge "Download free do whatever you want high-resolution photos from Elaine Casap") 
```
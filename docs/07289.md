# 我的第一个网络大会，有意思。

> 原文：<https://dev.to/gaserd/my-first-web-assembly-interesting-138l>

[![oh my god](img/2263e8a108876596d24491a2767659a2.png)](https://i.giphy.com/media/10HHiQbUEcOMr6/giphy.gif)

请耐心点😂

嗨，如果你和我一样想了解这个话题，那么你需要有点耐心。

我第一次没有成功。

所以，先来安装`emcc`
安装:

```
git clone https://github.com/emscripten-core/emsdk.git 
```

进一步

```
cd emsdk
./emsdk install latest 
```

并且进一步

```
./emsdk activate latest 
```

此外...

```
source ./emsdk_env.sh --build=Release 
```

很好，你安装了 emcc，现在你可以使用编译器 C/C++来编译 wasm，但是你需要安装 Python 2.7

是啊，完成！

```
echo "source $(pwd)/emsdk_env.sh --build=Release > /dev/null" >> ~/.bashrc 
```

好吧，我们去看看。

创建 index.html

```
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    WASM Demo
    <meta name="viewport" content="width=device-width, initial-scale=1">
</head>
<body>

    <h1>WASM Demo</h1>

    <script src="script.js"></script>
    <div id="container"></div>    
</body>
</html> 
```

创建 squarer.c

```
#include <emscripten.h>

EMSCRIPTEN_KEEPALIVE
int fib(int n) {
  int i, t, a = 0, b = 1;
  for (i = 0; i < n; i++) {
    t = a + b;
    a = b;
    b = t;
  }
  return b;
}

EMSCRIPTEN_KEEPALIVE
int squarer(int num) {
    return num * num;
} 
```

并创建 script.js

```
fetch('squarer.wasm').then(response =>
  response.arrayBuffer()
).then(bytes => WebAssembly.instantiate(bytes)).then(results => {
  instance = results.instance;
  document.getElementById("container").textContent = instance.exports._squarer(2) + instance.exports._fib(2);
}).catch(console.error); 
```

现在去编译文件 squarer.c

```
emcc -O3 -s WASM=1 squarer.c -o squarer.wasm 
```

呜呜呜！我们编译文件`squarer.wasm`
，现在，我们用`Web Server Chrome`检查我们的站点，他需要一个`fetch`
# 在 PHP 7.4 上试过 FFI

> 原文：<https://dev.to/nishimura/tried-ffi-on-php-7-4-4nl9>

# 获取 PHP 7.4 并检查启动

我试过 PHP 7.4 上的 FFI。

首先，获取 php 源代码并编译。需要配置- with-ffi 选项。

```
git clone https://github.com/php/php-src.git
cd php-src
git checkout PHP-7.4
./configure --with-ffi
make 
```

Enter fullscreen mode Exit fullscreen mode

用 FFI 启动服务器需要`ffi.enable=1`定义。

```
php-src/sapi/cli/php -d ffi.enable=1 -S localhost:8000 
```

Enter fullscreen mode Exit fullscreen mode

当服务器启动时，首先复制这里的例子[https://php.net/ffi.examples-basic](https://php.net/ffi.examples-basic)并确保没有错误。

# 用 C 语言为 FFI 制作共享库

推出 PHP 7.4 后，用 c 语言制作共享库。

```
#include <string.h> 
#define BUF_SIZE 1024 
const char * sample(const char *data, int mod)
{
  char buf[BUF_SIZE];
  const char * ret = buf;
  int i;

  for (i = 0; i <= strlen(data) && i < BUF_SIZE; i++){
    if (data[i] >= 97 && data[i] <= 122 && i % mod == 0)
      buf[i] = data[i] - 32;
    else
      buf[i] = data[i];
  }

  return ret;
} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个简单的程序，获取字符串和整型参数，并转换成大写。

将程序编译到共享库中，并从 php 程序中加载。

```
gcc -shared -o libsample.so sample.c 
```

Enter fullscreen mode Exit fullscreen mode

```
<?php
header('Content-Type: text/plain');

$ffi = FFI::cdef("

const char * sample(const char *data, int mod);

", __DIR__ . '/libsample.so');

var_dump($ffi);
var_dump($ffi->sample("sample test test", 3));
var_dump($ffi->sample("sample test test", 4));
var_dump($ffi->sample("sample test test", 1));

/* output:

object(FFI)#1 (0) {
}
string(16) "SamPle teSt TesT"
string(16) "SampLe tEst Test"
string(16) "SAMPLE TEST TEST"
*/ 
```

Enter fullscreen mode Exit fullscreen mode

效果很好！

PHP 的`string`类型变量在 C 中可以作为`const char *`类型变量接收。
似乎需要`const`。
PHP int 类型变量是 int 类型变量在 c 中也是

# 使用回调函数

目前 PHP 手册中的回调都是用`zend_write`来描述的，但我觉得不是那么清楚。https://php.net/ffi.examples-callback
T2

所以，我写了一个原创的回调程序，让它简单易懂。

```
#include <string.h> 
#define BUF_SIZE 1024 
typedef int (*callback_t)(int);

const char * sample(const char *data, callback_t callback)
{
  char buf[BUF_SIZE];
  const char * ret = buf;
  int i;

  for (i = 0; i <= strlen(data) && i < BUF_SIZE; i++){
    if (data[i] >= 97 && data[i] <= 122 && callback(i))
      buf[i] = data[i] - 32;
    else
      buf[i] = data[i];
  }

  return ret;
} 
```

Enter fullscreen mode Exit fullscreen mode

尝试了 PHP 闭包映射到 C 函数指针的行为。这个程序使用了一个简单的回调函数，接收 int 参数并返回 int。

下面是一个使用结构、回调和引用的例子:

```
<?php
header('Content-Type: text/plain');

$ffi = FFI::cdef("

typedef int (*callback_t)(int);
const char * sample(const char *data, callback_t callback);

", __DIR__ . '/libsample.so');

var_dump($ffi);
var_dump($ffi->sample("sample test test", fn($i) => $i % 3));
var_dump($ffi->sample("sample test test", fn($i) => $i % 4));
var_dump($ffi->sample("sample test test", fn($_) => true));

/* output:

object(FFI)#1 (0) {
}
string(16) "sAMpLE TEsT tESt"
string(16) "sAMPlE TeST tEST"
string(16) "SAMPLE TEST TEST"
*/ 
```

Enter fullscreen mode Exit fullscreen mode

既然 Arrow Functions 函数已经合并，就可以使用它了。

回调函数不能返回`string`类型。当尝试它时，出现错误`Uncaught FFI\Exception: FFI internal error. Unsupported return type`。

# 使用 C 语言的 struct

可以通过用结构声明调用`FFI :: cdef`来使用结构。

```
#include <string.h> 
#define BUF_SIZE 1024 
typedef int (*callback_t)(int);
struct cbdata {
  callback_t f;
};

const char * sample(const char *data, struct cbdata *cbdata)
{
  char buf[BUF_SIZE];
  const char * ret = buf;
  int i;

  for (i = 0; i <= strlen(data) && i < BUF_SIZE; i++){
    if (data[i] >= 97 && data[i] <= 122 && cbdata->f(i))
      buf[i] = data[i] - 32;
    else
      buf[i] = data[i];
  }

  return ret;
} 
```

Enter fullscreen mode Exit fullscreen mode

```
<?php
header('Content-Type: text/plain');

$ffi = FFI::cdef("

typedef int (*callback_t)(int);
struct cbdata {
  callback_t f;
};

const char * sample(const char *data, struct cbdata *cbdata);

", __DIR__ . '/libsample.so');

$cbdata = $ffi->new('struct cbdata');
$n = 3;
$cbdata->f = function($i) use(&$n){
    return $i % $n === 0;
};

$pcbdata = FFI::addr($cbdata);

var_dump($ffi->sample("sample test test", $pcbdata));

$n = 4;
var_dump($ffi->sample("sample test test", $pcbdata));

$n = 1;
var_dump($ffi->sample("sample test test", $pcbdata));

/* output:

string(16) "SamPle teSt TesT"
string(16) "SampLe tEst Test"
string(16) "SAMPLE TEST TEST"
 */ 
```

Enter fullscreen mode Exit fullscreen mode

从 PHP 引用可以从 C 函数的回调中使用，似乎可以在任何时候改变值的同时执行 C 函数。

# 使用另一种语言

如果我想高速执行数值计算，用 C 语言可能更好，但我不想用 C 做字符串处理
如果其他语言有 FFI，可以通过 FFI 进行通信。

```
{-# LANGUAGE ForeignFunctionInterface #-}

#include "template_operations.h"

module HaskellPhp where

import Foreign
import Foreign.C.String
import Foreign.C.Types

data Tops = Tops { assign::FunPtr (CString -> CString -> IO CInt) }

foreign export ccall hsParse :: CString -> Ptr Tops -> IO CString
foreign import ccall "dynamic" mkFun :: FunPtr (CString -> CString -> IO CInt)
                                     -> (CString -> CString -> IO CInt)
foreign import ccall "assign_value_get" value_get :: IO CString

instance Storable Tops where
    sizeOf _ = #size CTOPS
    alignment _ = #alignment CTOPS
    peek ptr = do
      assign' <- (#peek CTOPS, assign) ptr
      return Tops { assign=assign' }
    poke ptr (Tops assign') = do
                           (#poke CTOPS, assign) ptr assign'

--
-- Function to parse some string
--
hsParse :: CString -> Ptr Tops -> IO CString
hsParse cs cops = do
  ops <- peek cops
  let f = mkFun $ assign ops
  a <- newCString "foo"
  b <- newCString "bar"
  r <-  f a b
  cstr <- value_get
  s <- peekCString cstr
  newCString $ "hsParse finish: [" ++ show r ++ "]" ++ "[" ++ s ++ "]" 
```

Enter fullscreen mode Exit fullscreen mode

```
typedef struct template_operations
{
  size_t (*assign)(const char * key, const char * value);
} CTOPS; 
```

Enter fullscreen mode Exit fullscreen mode

```
#include <stdio.h>
#include <HsFFI.h> 
#ifdef __GLASGOW_HASKELL__
#include "Callback_stub.h"
#endif 
#include "template_operations.h" 
int prepare() {
  hs_init(0,0);
}
int finish() {
  hs_exit();
}

static const char * assign_string = "";
void assign_value_set(const char *value)
{
  assign_string = value;
}
const char *assign_value_get()
{
  return assign_string;
}

const char* parse(char *data, struct template_operations *tops)
{
  return hsParse(data, tops);
} 
```

Enter fullscreen mode Exit fullscreen mode

```
<?php
header('Content-Type: text/plain');
$ffi = FFI::cdef('
int prepare();
int finish();

typedef struct template_operations
{
  size_t (*assign)(const char * key, const char * value);
} CTOPS;

const char* parse(char *data, struct template_operations *tops);
void assign_value_set(const char *value);
const char * assign_value_get();

', __DIR__ . '/libcallback.so');

$ffi->prepare();

$tops = $ffi->new('struct template_operations');
$tops->assign = function($key, $value) use ($ffi){
    $value = "$key = $value";
    $ffi->assign_value_set($value);

    // Notice:
    // return string value is not supported
    return strlen($value);
};

$data = '<div>$foo</div>';
$ret = $ffi->parse($data, FFI::addr($tops));

var_dump($ffi->assign_value_get());
var_dump($ret);

$ffi->finish();

/* Output

string(9) "foo = bar"
string(30) "hsParse finish: [9][foo = bar]"

 */ 
```

Enter fullscreen mode Exit fullscreen mode

下面是一个使用 Haskell 的例子。
它假设 Haskell 将创建一个模板引擎，从这里与 PHP 交互。
使用`hsc2hs`在 haskell 中使用 ffi。

由于返回值不能是字符串类型，所以我把它做成 C 语言的全局变量。
PHP 本来就不是线程安全的，没有问题吧？可能...

如果写到这里，它可以直接调用 Haskell，而不需要使用 C 语言。

```
{-# NOINLINE return_value #-}
return_value :: IORef CString
return_value = unsafePerformIO $ newCString "" >>= newIORef

return_value_set :: CString -> IO ()
return_value_set a = writeIORef return_value a

return_value_get :: IO CString
return_value_get = readIORef return_value 
```

Enter fullscreen mode Exit fullscreen mode

```
<?php
header('Content-Type: text/plain');
$ffi = FFI::cdef('
int hs_init(int *, char **[]);
int hs_exit();

typedef struct template_operations
{
  size_t (*assign)(const char * key, const char * value);
} CTOPS;

const char* parse(char *data, struct template_operations *tops);
void return_value_set(const char *value);
const char * return_value_get();

', __DIR__ . '/libcallback.so');

$argc = FFI::new('int');
$argv = FFI::new('char[0]');
$pargv = FFI::addr($argv);
$ffi->hs_init(FFI::addr($argc), FFI::addr($pargv));

$tops = $ffi->new('struct template_operations');
$tops->assign = function($key, $value) use ($ffi){
    $value = "$key = $value";
    $ffi->return_value_set($value);

    // Notice:
    // return string value is not supported
    return strlen($value);
};

$data = '<div>$foo</div>';
$ret = $ffi->parse($data, FFI::addr($tops));

var_dump($ffi->return_value_get());
var_dump($ret);

$ffi->hs_exit();

/* Output

string(9) "foo = bar"
string(30) "hsParse finish: [9][foo = bar]"

 */ 
```

Enter fullscreen mode Exit fullscreen mode

# 结论

到目前为止，我编写的示例程序位于这里:

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [西村](https://github.com/nishimura) / [ php_ffi_samples](https://github.com/nishimura/php_ffi_samples)

<article class="markdown-body entry-content container-lg" itemprop="text">

# PHP FFI 示例

2019/05/18

## 编译 PHP 7.4

```
mkdir work
cd work

git clone https://github.com/php/php-src.git
cd php-src
git checkout PHP-7.4
./configure --with-ffi
make

cd ..
```

Enter fullscreen mode Exit fullscreen mode

## 检验样品

```
git clone https://github.com/nishimura/php_ffi_samples.git
```

Enter fullscreen mode Exit fullscreen mode

## 用 FFI 启动服务器

```
cd php_ffi_samples
../php-src/sapi/cli/php -d ffi.enable=1 -S localhost:8000

# and access to
#   http://localhost:8080/
#   http://localhost:8080/1/
#   ...
```

Enter fullscreen mode Exit fullscreen mode

## 创建 DSO 模块

要求:c 构建工具

```
cd 2
make

# and access to
#   http://localhost:8080/2/
```

Enter fullscreen mode Exit fullscreen mode

## 其他样品

*   3:使用闭包参数调用
*   4: c 结构、php 引用和回调
*   5: FFI 桥
*   6，7:直接调用 haskell 函数

## 到哈斯克尔的 FFI 桥

要求:haskell 构建工具(ghc)

### 样本 5

PHP = > FFI C = > FFI Haskell = > Callback C = > Callback PHP = >设置 C 变量而不是返回值

### 样品 6，样品 7

PHP => FFI Haskell 回调 PHP = >设置 haskell IORef 而不是返回值

</article>

[View on GitHub](https://github.com/nishimura/php_ffi_samples)

通过使用 FFI，PHP 专用于表单操作和 DB 调用，逻辑可以用你喜欢的任何语言编写，比如 Haskell，Go，Rust！
# 使用自定义终端功能

> 原文：<https://dev.to/jaga/using-custom-terminal-functions-1p3o>

## 你知道你可以在你的终端上声明一个自定义函数吗？

您每天在终端上执行的许多操作都可以不费吹灰之力自动完成，其中一种方法就是使用自定义功能。

bash/zsh 中的函数是如何工作的？这里有一个小例子:

```
function sayCiaone() {
    echo "ciaone from a shell function"
} 
```

将此内容复制到您的终端中，然后调用`sayCiaone`

* * *

## 以下是我日常使用的一些功能

### 创建文件夹并将光盘放入其中

```
function mkcd() {
    mkdir -p "$1" && cd "$1"
} 
```

`$1`是`mkcd`之后调用`mkcd my-new-folder`的第一个参数，本质上类似于键入

```
mkdir my-new-folder
cd my-new-folder 
```

* * *

### 在文件中粘贴剪贴板内容

```
function pastefile() {
    pbpaste > "$1"
} 
```

*   是 macOS 上的一个内置工具，可以打印剪贴板内容
*   `>`表示从左边写输入(命令 pbpaste 的输出)
*   `$1`是参数，在本例中是我要粘贴到的文件名。

`pastefile myFile`将用我当前剪贴板上的内容创建一个名为 *myFile* 的新文件

* * *

### 打印 package.json 脚本

```
function scripts() {
     cat package.json | jq -r '.scripts'
} 
```

[![scripts](img/75984e7e902e05e3fa1bd95dbcd564c4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hFWeB5N8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://jagascript.com/scripts-0cad385767a37dbef1d6729f44f50539.gif)

*   `|`是 shell 脚本中的管道操作符，这意味着他左边命令的输出将是他右边命令的输入，在本例中是`package.json`文件的内容
*   `jq`是一个用于格式化和漂亮打印 JSON 的实用程序，在这种情况下，我们只读取接收到的对象的字段*脚本*

* * *

### 使用 wip 提交消息提交所有内容

```
function wip() {
    git add --all
    git commit -m "wip"
} 
```

这是不言自明的:D

## 提示

*   在 *zsh* 中，您可以将功能设置为单行
*   在*中，zsh* 功能关键字是可选的

以下片段在 *zsh*
有效

```
mkcd() { mkdir -p "$1" && cd "$1" } 
```

## 保存在点文件中以便重复使用

声明一个函数只会使它在当前的终端会话中可用，如果您希望它被持久化并可以从任何终端访问，您可以在您的。zshrc 或者。bashrc

## 更新

看来`function`关键字是可选的

这段代码实际上在 zsh 和 bash 中都有效

sayCiaone() {
回显“来自 shell 函数的 CIA one”
}

感谢 [@moopet](https://dev.to/moopet)
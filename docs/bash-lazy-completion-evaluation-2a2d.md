# bash -懒惰完成评估。

> 原文：<https://dev.to/zanehannanau/bash-lazy-completion-evaluation-2a2d>

# bash 中的懒惰完成评价

TL；dr:延迟加载完成可以帮助提高终端初始化速度，减少内存使用(如果只是轻微的)，并且只使用一次昂贵的函数(例如生成器)。

惰性评估有很多好处——更少的内存、更少的解析、更少的评估，并且您只需为您使用的东西付费。那么，在 bash 中我们可以用它做什么呢？

嗯，这里有一个愚蠢的(嗯，需要一些跳转)的方法来设置它:

```
function __lazyfunc {
  if [[ -z "$(type -t $1)" || $LAZYFUNC_FORCE_REDEFINE ]]; then local fn="$1"
    shift
    eval "$fn () { unset -f $fn ; eval \"\$( $@ )\" ; $fn  \$@ ; }"
  fi
} 
```

那么它是做什么的呢？

*   如果未声明，则仅定义。如果您对此重新运行`. .bashrc`，它不会覆盖这些值。
*   (局部)取第一个参数，把它从前面移走
*   评估定义函数的字符串

好了

* * *

当然，也有一些缺点-

*   大多数声明并不打算这样做
*   重写可用的源代码需要一些编辑工作

例如，用这个，而不是仅仅用

```
eval "$(pandoc --bash-completion)" 
```

现在需要

```
__lazyfunc _pandoc pandoc --bash-completion 
```

对其他人来说更糟。例如，nvm:

```
export NVM_DIR="$HOME/.nvm"
__lazyfunc __nvm 'cat $NVM_DIR/bash_completion'
__lazyfunc nvm 'cat $NVM_DIR/nvm.sh'
complete -o default -F __nvm nvm 
```

货物和垃圾？:

```
__lazyfunc _rustup rustup completions bash rustup
__lazyfunc _cargo 'cat $(rustc --print sysroot)/etc/bash_completion.d/cargo'
complete -F _rustup -o bashdefault -o default rustup
complete -F _cargo cargo 
```

## [使用中的](#in-use)

使用上述设置，加载一段时间后(并在 eval 调用前添加`time`)，

```
$ nvm a
real    0m0.007s
user    0m0.006s
sys 0m0.001s
lias ^C
$ cargo a
real    0m0.142s
user    0m0.089s
sys 0m0.042s
dd ^C
$ rustup 
real    0m0.023s
user    0m0.012s
sys 0m0.011s

completions     dump-testament  man             show            -v
component       -h              override        target          -V
default         --help          run             toolchain       --verbose
doc             help self            uninstall       --version
docs            install         set update          which
$ rustup ^C
$ pandoc 
real    0m1.221s
user    0m0.302s
sys 0m0.202s

$ pandoc
$ npm 
real    0m0.954s
user    0m0.535s
sys 0m0.351s
v12.2.0

real    0m2.186s
user    0m1.153s
sys 0m1.058s
$ node

real    0m0.993s
user    0m0.524s
sys 0m0.404s
Welcome to Node.js v12.2.0.
Type ".help" for more information.
> 
```

(node 和 npm 是通过 nvm 加载的，它们有自己的宏，所以这比通常花费的时间要长一些)

```
function node {
        unset -f node npm
        local which=`nvm which | tail -n 1`
        NODE_ICU_DATA="$(dirname "$(dirname "$which")")/lib/node_modules/full-icu"
        PATH="$PATH:$(dirname "$which")"
        $which $@
}
function npm {
        >&2 node -v
        npm $@
}
__lazyfunc _npm_completion npm completion
complete -F _npm_completion npm 
```

现在，有什么好处呢？

*   在磁盘访问速度较慢的计算机上，这大大减少了启动和访问终端之间的时间
*   在电话(termux)上，这也减少了启动之间的时间

当然，也有一些缺点

*   功能的启动会增加初始完成的时间
*   这种初始设置可能很烦人

现在，我非常怀疑我是第一个发布这个消息的人；但是值得花时间。

# 调查

正如您所猜测的，这意味着它热交换功能。那么，有什么区别呢？

```
$ type _cargo
_cargo is a function 
```

```
_cargo () 
{ 
    unset -f _cargo;
    eval "$( cat $(rustc --print sysroot)/etc/bash_completion.d/cargo )";
    _cargo $@
} 
```

```
$ type __nvm
__nvm is a function 
```

```
__nvm () 
{ 
    unset -f __nvm;
    eval "$( cat $NVM_DIR/bash_completion )";
    __nvm $@
} 
```

```
$ nvm alias ^C
$ type __nvm
__nvm is a function 
```

```
__nvm () 
{ 
    declare previous_word;
    previous_word="${COMP_WORDS[COMP_CWORD - 1]}";
    case "${previous_word}" in use | run | exec | ls | list | uninstall)
            __nvm_installed_nodes
        ;;
        alias | unalias)
            __nvm_alias
        ;;
        *)
            __nvm_commands
        ;;
    esac;
    return 0
} 
```
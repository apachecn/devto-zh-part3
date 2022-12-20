# 用 ndenv 忽略“. node-version”上的“v”

> 原文：<https://dev.to/risacan/ignore-v-on-node-version-with-ndenv-445a>

# premise

作为多种语言的版本管理工具使用了[anyenv](https://github.com/riywo/anyenv) 。 因此，我们将 anyenv 支持的[ndenv](https://github.com/riywo/ndenv) 作为 node 的版本管理工具使用。

# 发生的事情

明明安装了指定的版本，却被告知没有安装。

```
 🌸 npm run storybook (*任意のnpmコマンド)
    ndenv: version `10.14.0' is not installed

    🌸 ndenv install -l 
    Available versions:
      v0.1.14
      v0.1.15
      v0.1.16
    ~省略~
      v10.14.0
    ~省略~

    🌸 ndenv install v10.14.0
    🌸 ndenv global v10.14.0
    🌸 npm run storybook
    ndenv: version `10.14.0' is not installed 
```

# Conclusion

在存储库中添加`.node-version`的人使用了[nodenv](https://github.com/nodenv/nodenv) ，其记述与 ndenv 不同。

## 具体地说

在指定项目中使用的 Node 版本的`.node-version`中，明明写有`10.14.0`，却安装了`v10.14.0`。

```
 🌸 cat .node-version
    10.14.0 
```

*   ndenv 和 nodenv 中，`.node-version`的写法不同。
*   ndenvは `v10.14.0`
*   nodenvは `10.14.0`
*   如果不与. node-version 中指定的完全相同，则会出现`not installed`

# Solution

*   实际上，在 ndenv 上，也可以安装没有`v`标记的版本...所以只用数字指定版本&并安装了对应。 (明明内容一样，但是更改了名字后重新安装会有违和感...)

```
 🌸 ndenv install 10.14.0
    🌸 ndenv global 10.14.0
    🌸 ndenv version 
    10.14.0 
```
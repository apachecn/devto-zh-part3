# 轻量级 fzf 用法

> 原文：<https://dev.to/callas1900/lightweight-fzf-usages-149p>

[fzf](https://github.com/junegunn/fzf) 的有效核心用法是写入函数。巴沙尔或类似的东西。

本文关注轻量级的用法。

## 用 fzf 打开 vim

```
vim $(fzf) 
```

## 用 fzf 打开 meld(差异工具)

```
 meld $(fzf -m) 
```

## 用 fzf 读取 json REST Api

```
curl ... | json_pp | fzf 
```
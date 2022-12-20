# Dockerfile 提示 1:定义一个基础层

> 原文：<https://dev.to/rawkode/dockerfile-tip-1-define-a-base-layer-1aae>

随着多阶段 Dockerfile 的出现，人们很常见到非常特定的层来完成单一任务；它们通常使用相同的“初级”基层。

**举例:**

```
FROM elixir:1.8 AS deps

RUN mix deps.get

FROM elixir:1.8 AS compile

COPY --from=deps ...

RUN mix compile 
```

Enter fullscreen mode Exit fullscreen mode

这允许冗长的任务利用构建缓存并在层之间传递工件，而无需重新运行冗长的任务。然而，我经常看到这个“主要”层在一个 docker 文件中重复了 4/6/10 次；使得维护很麻烦。

## 提示

用你的主图像创建一个空层:

```
FROM elixir:1.8 AS base

FROM base AS deps

# ...

FROM base AS compile

# ... 
```

Enter fullscreen mode Exit fullscreen mode

这减少了重复，使维护和拉式请求更容易审查/管理/更改。

直到下一次
🤘
# 条件控制:我只想要那个信息一次

> 原文：<https://dev.to/sckott/condition-control-i-just-want-that-message-once-482e>

我相信已经有一种方法可以做到这一点，但这里去。或者这是一种反模式。不管怎样，这是我在问一个愚蠢的问题。

几小时前我碰到了这个:

```
Sys.unsetenv("ENTREZ_KEY")
library(brranching)
mynames <- c("Poa annua", "Salix goodingii", "Helianthus annuus")
phylomatic_names(taxa = mynames, format='rsubmit')

No ENTREZ API key provided
 Get one via taxize::use_entrez()
 See https://ncbiinsights.ncbi.nlm.nih.gov/2017/11/02/new-api-keys-for-the-e-utilities/

No ENTREZ API key provided
 Get one via taxize::use_entrez()
 See https://ncbiinsights.ncbi.nlm.nih.gov/2017/11/02/new-api-keys-for-the-e-utilities/

No ENTREZ API key provided
 Get one via taxize::use_entrez()
 See https://ncbiinsights.ncbi.nlm.nih.gov/2017/11/02/new-api-keys-for-the-e-utilities/

[1] "poaceae%2Fpoa%2Fpoa_annua" "salicaceae%2Fsalix%2Fsalix_goodingii" "asteraceae%2Fhelianthus%2Fhelianthus_annuus" 
```

[brranching](https://github.com/ropensci/brranching/) 包在内部使用 [taxize](https://github.com/ropensci/taxize/) 包，调用它的函数`taxize::tax_name()`。如果找不到用户的 NCBI Entrez API 密钥，函数会向用户抛出有用的消息，并告诉他们如何找到它。

然而，用户不必获得 API 密钥。如果他们不这样做，那么他们就会受到相同信息的多次重复。

我想知道对此能做些什么。也就是说，如果将要抛出的消息与函数调用中已经抛出的消息相同，只需跳过其他相同的消息。

当然有消息的`suppressMessages()`,但是在包开发中，如果你想让用户看到一条消息，你就不要隐藏消息。`suppressMessages`对于这个用例来说是一个太钝的工具。

## 代码

`with_mssgs()`捕获值和消息，隐藏消息

```
with_mssgs <- function(expr) {
  my_mssgs <- NULL
  w_handler <- function(w) {
    my_mssgs <<- c(my_mssgs, list(w))
    invokeRestart("muffleMessage")
  }
  val <- withCallingHandlers(expr, message = w_handler)
  list(value = val, messages = my_mssgs)
} 
```

`MessageKeeper`是一个小的 [R6](https://cran.rstudio.com/web/packages/R6/) 类，用于处理消息、匹配和简单检查消息是否被使用过。

```
library(R6)
MessageKeeper <- R6::R6Class("MessageKeeper",
  public = list(
    bucket = NULL,
    print = function(x, ...) {
      cat('MessageKeeper', sep = "\n")
      cat(paste0(' messages: ', length(self$bucket)))
      if (length(self$bucket) > 0) {
        cat("\n")
        for (i in self$bucket) {
          cat(paste0(" ", substring(i, 1, 50)))
        }
      }
    },
    add = function(x) {
      self$bucket <- c(self$bucket, list(x))
      invisible(self)
    },
    remove = function() {
      if (self$length() == 0) return(NULL)
      head <- self$bucket[[1]]
      self$bucket <- self$bucket[-1]
      head
    },
    purge = function() {
      self$bucket <- NULL
    },
    thrown_already = function(x) {
      x %in% self$bucket
    },
    not_thrown_yet = function(x) {
      !self$thrown_already(x)
    }
  )
) 
```

MessageKeeper 示例

```
mssger <- MessageKeeper$new()
mssger
#> MessageKeeper
#> messages: 0
mssger$add("one")
mssger$add("two")
mssger
#> MessageKeeper
#> messages: 2
#> one two
mssger$thrown_already("one")
#> [1] TRUE
mssger$thrown_already("bears")
#> [1] FALSE
mssger$not_thrown_yet("bears")
#> [1] TRUE
mssger$purge() 
```

`handle_mssgs()`是一个函数，您将目标函数封装在其中以处理消息

```
handle_mssgs <- function(expr) {
  res <- with_mssgs(expr)
  if (!is.null(res$messages)) {
    # if not thrown yet, add to bucket and throw it
    if (my_mssger$not_thrown_yet(res$messages[[1]]$message)) {
      my_mssger$add(res$messages[[1]]$message)
      message(res$messages[[1]]$message)
    }
  }
  return(res$value)
} 
```

设置消息管理器

```
my_mssger <- MessageKeeper$new() 
```

对数值求平方并返回，如果大于 20 则抛出一条消息

```
squared <- function(x) {
  stopifnot(is.numeric(x))
  y <- x^2
  if (y > 20) message("woops, > than 20! check your numbers")
  return(y)
} 
```

`foo()`使用`vapply()`通过`squared()`运行任何数字向量

```
foo <- function(x) {
  vapply(x, function(z) squared(z), numeric(1))
} 
```

做同样的事情，但是使用我们的 MessageKeeper

```
bar <- function(x) {
  # tear down on exit
  on.exit(my_mssger$purge())
  vapply(x, function(z) handle_mssgs(squared(z)), numeric(1))
} 
```

讨厌地为每一个可能的实例抛出一条消息

```
foo(1:20)
#> woops, > than 20! check your numbers
#> woops, > than 20! check your numbers
#> woops, > than 20! check your numbers
#> woops, > than 20! check your numbers
#> woops, > than 20! check your numbers
#> woops, > than 20! check your numbers
#> woops, > than 20! check your numbers
#> woops, > than 20! check your numbers
#> woops, > than 20! check your numbers
#> woops, > than 20! check your numbers
#> woops, > than 20! check your numbers
#> woops, > than 20! check your numbers
#> woops, > than 20! check your numbers
#> woops, > than 20! check your numbers
#> woops, > than 20! check your numbers
#> woops, > than 20! check your numbers
#> [1] 1 4 9 16 25 36 49 64 81 100 121 144 169 196 225 256 289
#> [18] 324 361 400 
```

而`bar()`只抛出一次消息

```
bar(1:20)
#> woops, > than 20! check your numbers
#> [1] 1 4 9 16 25 36 49 64 81 100 121 144 169 196 225 256 289
#> [18] 324 361 400 
```
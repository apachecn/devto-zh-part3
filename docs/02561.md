# 纯脚本中的刽子手

> 原文：<https://dev.to/riccardoodone/hangman-in-purescript-1pe0>

你可以继续在这里阅读或者[跳转到我的博客](https://odone.io/posts/2019-05-20-hangman-in-purescript.html)来获得完整的体验，包括美妙的粉色、蓝色和白色调色板。

* * *

# 纯脚本中的刽子手

运行示例

```
Insert word to guess
> hello
-----
Try to guess
> hol
h-l--
> hello
You won! 
```

Enter fullscreen mode Exit fullscreen mode

代码

```
module Main where

import Prelude
import Effect (Effect)
import Effect.Console (log)
import Node.ReadLine as RL
import Data.String.Yarn (fromChars, toChars) as S
import Data.List.Lazy as L
import Data.String (length) as S

mask :: String -> String -> String
mask word guess =
  let as = S.toChars word :: L.List Char
      bs = S.toChars guess :: L.List Char
      bs' = bs <> L.replicate (L.length as - L.length bs) '-'
      zipper a b = if a == b then a else '-'
  in
      S.fromChars $ L.zipWith zipper as bs'

main :: Effect Unit
main = do
    interface <- RL.createConsoleInterface RL.noCompletion
    let
        lineHandler word guess =
            case word of
                 "" -> do
                    log $ S.fromChars $ L.take (S.length guess) (L.repeat '-')
                    log "Try to guess"
                    RL.setLineHandler interface $ lineHandler guess
                    RL.setPrompt "> " 2 interface
                    RL.prompt interface
                 _ ->
                    if word == guess then do
                        RL.close interface
                        log "You won!"
                    else do
                        log $ mask word guess
                        RL.setPrompt "> " 2 interface
                        RL.prompt interface
    RL.setLineHandler interface $ lineHandler ""
    log "Insert word to guess"
    RL.setPrompt "> " 2 interface
    RL.prompt interface 
```

Enter fullscreen mode Exit fullscreen mode

* * *

从我个人的电子邮件中获取最新内容。用你的想法回复。让我们互相学习。订阅我的 [PinkLetter](https://odone.io#newsletter) ！
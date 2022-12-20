# 纯脚本中的尼姆

> 原文：<https://dev.to/riccardoodone/nim-in-purescript-1b7k>

你可以继续在这里阅读或者[跳转到我的博客](https://odone.io/posts/2019-05-27-nim-in-purescript.html)来获得完整的体验，包括美妙的粉色、蓝色和白色调色板。

* * *

[来自维基百科](https://en.wikipedia.org/wiki/Nim)

> Nim 是一种数学策略游戏，两个玩家轮流从不同的堆中移走(即 nimming)物体。每回合，一名玩家必须移走至少一个物体，并且可以移走任意数量的物体，只要它们都来自同一个堆。游戏的目标是避免拿走最后一个物体。

运行示例

```
1: * * * * *
2: * * * *
3: * * *
4: * *
5: *
Player1
Insert ROW NUM. Example '1 3'
> 1 3
1: * *
2: * * * *
3: * * *
4: * *
5: *
Player2
Insert ROW NUM. Example '1 3'
> 1 3
Invalid move
Player2
Insert ROW NUM. Example '1 3'
... 
```

Enter fullscreen mode Exit fullscreen mode

代码

```
module Main where

import Prelude
import Effect (Effect)
import Effect.Console (log, logShow)
import Data.Foldable (foldMap, foldr)
import Data.Array as Array
import Data.String.Common as String
import Node.ReadLine as RL
import Data.String.Pattern (Pattern(..))
import Data.Int as Int
import Data.Maybe (Maybe(..))

newtype Board = Board
    { row1 :: Int
    , row2 :: Int
    , row3 :: Int
    , row4 :: Int
    , row5 :: Int
    }
instance showBoard :: Show Board where
    show (Board board) = foldMap identity
        [ "1: ", stars board.row1, "\n"
        , "2: ", stars board.row2, "\n"
        , "3: ", stars board.row3, "\n"
        , "4: ", stars board.row4, "\n"
        , "5: ", stars board.row5
        ]
        where stars i = String.joinWith " " $ Array.replicate i "*"

data Player
    = Player1
    | Player2
instance showPlayer :: Show Player where
    show Player1 = "Player1"
    show Player2 = "Player2"

initialBoard :: Board
initialBoard = Board
    { row1: 5
    , row2: 4
    , row3: 3
    , row4: 2
    , row5: 1
    }

nextPlayer :: Player -> Player
nextPlayer Player1 = Player2
nextPlayer Player2 = Player1

validMove :: Int -> Int -> Board -> Boolean
validMove 1 num (Board board) = board.row1 >= num
validMove 2 num (Board board) = board.row2 >= num
validMove 3 num (Board board) = board.row3 >= num
validMove 4 num (Board board) = board.row4 >= num
validMove 5 num (Board board) = board.row5 >= num
validMove _ _   _    = false

play :: Int -> Int -> Board -> Board
play 1 num (Board board) = Board $ board { row1 = board.row1 - num }
play 2 num (Board board) = Board $ board { row2 = board.row2 - num }
play 3 num (Board board) = Board $ board { row3 = board.row3 - num }
play 4 num (Board board) = Board $ board { row4 = board.row4 - num }
play 5 num (Board board) = Board $ board { row5 = board.row5 - num }
play _ num (Board board) = Board board

finished :: Board -> Boolean
finished (Board board) = foldr (\a b -> a && b) true
    [ board.row1 == 0
    , board.row2 == 0
    , board.row3 == 0
    , board.row4 == 0
    , board.row5 == 0
    ]

main :: Effect Unit
main = do
    interface <- RL.createConsoleInterface RL.noCompletion
    let
        lineHandler board player move =
            case map Int.fromString $ String.split (Pattern " ") move of
                 [ Just row, Just num ] ->
                      if validMove row num board
                          then do
                              let nextBoard = play row num board
                              let nextPlayer_ = nextPlayer player
                              if finished nextBoard
                                  then do
                                      RL.close interface
                                      log $ show nextPlayer_ <> " won!"
                                  else do
                                      logShow nextBoard
                                      logShow nextPlayer_
                                      RL.setLineHandler interface $ lineHandler nextBoard nextPlayer_
                                      log "Insert ROW NUM. Example '1 3'"
                                      RL.setPrompt "> " 2 interface
                                      RL.prompt interface
                          else do
                              log "Invalid move"
                              logShow player
                              log "Insert ROW NUM. Example '1 3'"
                              RL.setPrompt "> " 2 interface
                              RL.prompt interface
                 _ -> do
                      log "Insert ROW NUM. Example '1 3'"
                      RL.setPrompt "> " 2 interface
                      RL.prompt interface
    logShow initialBoard
    logShow Player1
    RL.setLineHandler interface $ lineHandler initialBoard Player1
    log "Insert ROW NUM. Example '1 3'"
    RL.setPrompt "> " 2 interface
    RL.prompt interface 
```

Enter fullscreen mode Exit fullscreen mode

* * *

从我个人的电子邮件中获取最新内容。用你的想法回复。让我们互相学习。订阅我的 [PinkLetter](https://odone.io#newsletter) ！
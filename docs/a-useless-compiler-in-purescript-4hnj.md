# PureScript 中无用的编译器

> 原文：<https://dev.to/riccardoodone/a-useless-compiler-in-purescript-4hnj>

你可以继续在这里阅读或者[跳转到我的博客](https://odone.io/posts/2019-04-01-a-useless-compiler-in-purescript.html)来获得完整的体验，包括美妙的粉色、蓝色和白色调色板。

* * *

[![](img/628824cf0212dc9c072a65047c85cbed.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FM3wiphg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2400/1%2ArTVIrJPogxTyV4Guona3Pw.jpeg)

## [T1】简介](#intro)

这周我决定用 PureScript 写一些类似编译器的代码。至于最后几个帖子，我已经将范围限制到了最小的单元。

这意味着，我自己没有太多时间学习编译器。希望这将成为我决定深入研究的有用基础。

## 我们出发吧

据我所知，编译器由一系列步骤组成，这些步骤获取一些输入代码并将其转换为目标语言。

作为输入，我们将使用一种支持对整数进行两种操作(即`add`和`sub`)的语言。特别是，我们将使用以下代码作为输入:`add 1 sub 6 add 3 2`。

## 解析

我们要做的第一件事是将代码解析成抽象语法树(AST)。换句话说，一个使我们能够轻松处理代码的结构。上面提供的示例代码的 AST 类似于

```
 add
  / \
  1  sub
     / \
    6  add
       / \
      3   2 
```

Enter fullscreen mode Exit fullscreen mode

完成这项工作的代码如下

```
data Ast
  = Node Op Ast Ast
  | Value Int
instance showAst :: Show Ast where
  show (Node op ast1 ast2) = "(" <> show op <> " " <> show ast1 <> " " <> show ast2 <> ")"
  show (Value i) = show i

data Op
  = Add
  | Sub
instance showOp :: Show Op where
  show Add = "Add"
  show Sub = "Sub"

astParser :: Parser Ast
astParser = do
  skipSpaces
  op <- opParser
  skipSpaces
  ast1 <- valueParser <|> astParser
  skipSpaces
  ast2 <- valueParser <|> astParser
  pure $ Node op ast1 ast2

opParser :: Parser Op
opParser =
  addParser <|> subParser

addParser :: Parser Op
addParser = do
  _ <- string "add"
  pure Add

subParser :: Parser Op
subParser = do
  _ <- string "sub"
  pure Sub

intParser :: Parser Int
intParser = do
  ds <- many1 anyDigit
  case fromString $ fromChars ds of
    Just i -> pure i
    Nothing -> fail "I was expecting an int!"

valueParser :: Parser Ast
valueParser = do
  i <- intParser
  pure $ Value i 
```

Enter fullscreen mode Exit fullscreen mode

## 生成/评估

有了 AST，我们可以编译成其他语言或者评估代码:

```
generate :: Ast -> String
generate (Value i) = show i
generate (Node Add ast1 ast2) =
  "(" <> generate ast1 <> " + " <> generate ast2 <> ")"
generate (Node Sub ast1 ast2) =
  "(" <> generate ast1 <> " - " <> generate ast2 <> ")"

evaluate :: Ast -> Int
evaluate (Value i) = i
evaluate (Node Add ast1 ast2) =
  evaluate ast1 + evaluate ast2
evaluate (Node Sub ast1 ast2) =
  evaluate ast1 - evaluate ast2 
```

Enter fullscreen mode Exit fullscreen mode

## 试驾

```
input :: String
input =
  "add 1 sub 6 add 3 2"

main :: Effect Unit
main = do
  logShow $ runParser astParser input
  -- (Right (Add 1 (Sub 6 (Add 3 2))))
  logShow $ map generate $ runParser astParser input
  -- (Right "(1 + (6 - (3 + 2)))")
  logShow $ map evaluate $ runParser astParser input
  -- (Right 2) 
```

Enter fullscreen mode Exit fullscreen mode

## 其他

如果您想了解更多，这是激励我走进编译器世界的原因:

*   [超级微型编译器](https://github.com/jamiebuilds/the-super-tiny-compiler/blob/master/the-super-tiny-compiler.js)
*   [微型 Interepter 和编译器](https://github.com/mgechev/tiny-compiler/blob/master/tiny.js)
*   [如何成为一名编译器——用 JavaScript 制作编译器](https://medium.com/@kosamari/how-to-be-a-compiler-make-a-compiler-with-javascript-4a8a13d473b4)

* * *

从我个人的电子邮件中获取最新内容。用你的想法回复。让我们互相学习。订阅我的 [PinkLetter](https://odone.io#newsletter) ！
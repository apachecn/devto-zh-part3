# 用 Aeson 编码和解码 JSON

> 原文：<https://dev.to/gillchristian/encoding-and-decoding-json-with-aeson-28af>

在过去的几天里，我一直在开发一个小应用程序，用来解析我从银行获得的交易报表，我的想法是生成一些更容易可视化的东西。

我得到一个`.txt`文件，其中每一行对应一个事务，我想从中生成一个 JSON 文件，然后我可以在图表应用程序中使用它。

在这个过程中，我通过使用 [Aeson](http://hackage.haskell.org/package/aeson-1.4.3.0/docs/Data-Aeson.html) 了解了一些关于在 Haskell 中使用 JSON 的事情。

为了对 JSON 的值进行解码和编码，我们使用了`decode`和`encode`方法(很好的命名😉).

```
decode :: FromJSON a => ByteString -> Maybe a
encode :: ToJSON a => a -> ByteString 
```

*注意*:通过设置[过载字符串](https://downloads.haskell.org/~ghc/latest/docs/html/users_guide/glasgow_exts.html#overloaded-strings)编译器扩展，我们不仅可以将字符串文字视为`String`，还可以将其视为`ByteString`、`Text`等等。

我们可以从这些类型签名中学到很多关于 Aeson 如何工作的知识。

`decode`取一个`ByteString`(我们可以把它想象成一个字符串)并返回一个`Maybe a`，条件是这个`a`必须有`FromJSON`类型的类。有了这个名字，我们可以假设它是针对可以从 JSON 解析的类型的。

另一方面，`encode`接受一个类型为`a`的值，这次使用的是`ToJSON`类型的类，并返回一个字符串。在这种情况下，我们可以假设`ToJSON`意味着可以转换成 JSON 的类型。

让我们试着使用它们。注意，我们需要指定解码/编码的类型，这样编译器就知道应该使用 FromJSON/TOJSON 的哪个实例。

```
λ> :set -XOverloadedStrings
λ> import Data.Aeson
λ> decode "true" :: Maybe Bool
Just True
λ> decode "tru" :: Maybe Bool
Nothing
λ> decode "123" :: Maybe Bool
Nothing
λ> decode "123" :: Maybe Int
Just 123
λ> encode (123 :: Int)
"123"
λ> encode ("hola" :: String)
"\"hola\""
λ> encode (Just 123 :: Maybe Int)
"123"
λ> encode (Nothing :: Maybe Int)
"null" 
```

解码会返回一个`Maybe a`，因为我们不知道字符串中的 JSON 格式是否正确，所以操作可能会失败。而编码，因为我们有了`ToJSON`约束，应该总是成功的。

所有这些类型都“开箱即用”，因为 Aeson 已经实现了这些实例🎉

```
λ> :i Int
data Int = ghc-prim-0.5.3:GHC.Types.I# ghc-prim-0.5.3:GHC.Prim.Int#
        -- Defined in ‘ghc-prim-0.5.3:GHC.Types’
...
instance ToJSON Int
  -- Defined in ‘aeson-1.4.2.0:Data.Aeson.Types.ToJSON’
instance FromJSON Int
  -- Defined in ‘aeson-1.4.2.0:Data.Aeson.Types.FromJSON’ 
```

但是我们不会只处理原始类型，对吗？

但是，如果我们试图编码一个自定义类型的值，我们会得到一个错误。

```
λ> data Obj = Obj { id :: Int }
λ> encode Obj { id = 1 }

<interactive>:5:1: error:
    • No instance for (ToJSON Obj) arising from a use of ‘encode’
    • In the expression: encode Obj {id = 1}
      In an equation for ‘it’: it = encode Obj {id = 1} 
```

我们需要为这种类型实现`ToJSON`。

```
λ> :i ToJSON
class ToJSON a where
  toJSON :: a -> Value
  default toJSON :: (GHC.Generics.Generic a,
                     aeson-1.4.2.0:Data.Aeson.Types.ToJSON.GToJSON
                       Value Zero (GHC.Generics.Rep a)) =>
                    a -> Value
  toEncoding :: a -> Encoding
  toJSONList :: [a] -> Value
  toEncodingList :: [a] -> Encoding
        -- Defined in ‘aeson-1.4.2.0:Data.Aeson.Types.ToJSON’ 
```

我们可以手动完成:

```
{-# LANGUAGE OverloadedStrings #-}

import Data.Aeson

newtype Obj = Obj
  { a :: Int
  }

instance ToJSON Obj where
  toJSON Obj {a = a} = object [("a", toJSON a)]

-- object :: [Pair] -> Value
-- type Pair = (Text, Value) -- Value here is the Aeson.Value 
```

或者我们可以使用一些编译器的魔法，使用编译器扩展来帮我们做到这一点:[deriveany class](https://downloads.haskell.org/~ghc/latest/docs/html/users_guide/glasgow_exts.html?highlight=deriveanyclass#extension-DeriveAnyClass)&[DeriveGeneric](https://downloads.haskell.org/~ghc/latest/docs/html/users_guide/glasgow_exts.html?highlight=deriveanyclass#extension-DeriveGeneric)。

```
{-# LANGUAGE DeriveAnyClass #-}
{-# LANGUAGE DeriveGeneric  #-}
{-# LANGUAGE OverloadedStrings #-}

import Data.Aeson

newtype Obj = Obj
  { a :: Int
  } deriving (Generic, ToJSON) 
```

而这里🎉

这就是我们大部分时间要做的事情，除非我们以某种特定的方式对数据进行编码或解码。

实现和派生`FromJSON`的工作非常相似，所以不值得展示。

## 结论

派生类型类实例的编译器魔力使得在 Haskell 中使用 JSON 变得非常容易。我们仍然拥有我们类型的安全性，而不必像其他语言(例如 Elm)那样为所有东西实现定制的编码器/解码器。
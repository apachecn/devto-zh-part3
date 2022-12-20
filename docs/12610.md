# 用 GHC 仿制药进行鸭分型

> 原文：<https://dev.to/involans/duck-typing-with-ghc-generics-nj1>

如果您是从一般的编程背景开始接触 Haskell 的，有些术语可能会非常晦涩难懂。Haskell(或者更确切地说是 GHC)版图中比神秘更令人困惑的一块是 [GHC 泛型](http://hackage.haskell.org/package/base-4.12.0.0/docs/GHC-Generics.html)系统。在这里，我将介绍这是什么，为什么您可能想要使用它，以及它如何让您完成在 Haskell 类型系统的约束下看起来不可能完成的事情。

# 泛型/=参数多态性

不幸的是，术语“泛型”在软件开发社区中被过度使用了。从 Java 开始，这个术语已经扩展到许多语言社区(可能包括今天使用的最有影响力和最广泛的类型系统，TypeScript)，其含义是“参数多态性”，即构造具有一个或多个类型参数的类型的能力，因此在一个结构可以由许多不同的类型居住的意义上是通用的。Haskell 中这种无处不在的例子，包括所有常见的疑点如`[a]`、`Maybe a`、`Tree a`、`Map k v`、`(a,b)`甚至`IO a`。

所以给定这两种类型:

```
data A = A Int Bool Char
data B a b c = B a b c 
```

`A`是单态的，即只有一个居民，`B`是多态的，可以居住各种不同的东西，如`B Word Word Word`、`B String (Set Int) (IO ())`等，甚至`B Int Bool Char`，与`A`同构。我们也可以说`A`属于`*`类，而`B`属于`* -> * -> * ->
*`类。

但是即使`A`和`B Int Bool Char`具有相同的形状(1 个构造函数，3 个位置字段),并且它们的每个字段都具有相同的形状，但是它们是不同的类型，如果不编写某种类型的类，我们无法直接编写一个函数，比如说，从`A`或`B Int Bool Char`中提取第二个位置字段作为`Bool`,更不用说`B a Int b`了。

相比之下，像 Python 或 Clojure 这样的动态类型语言，编写这样的函数很简单，它们根据对象的结构而不是名称来操作对象:

```
def foo(x):
  print x.foo 
```

```
(defn  foo  [x]  (print  (:foo  x))) 
```

这种不同的数据类型方法，即所谓的结构类型，与名义类型相反，通常被称为*鸭子类型*，如果它叫起来像鸭子，看起来像鸭子，那么实际上它就是一只鸭子。在结构类型术语中，如果它有一个同名的字段，并且该字段有该类型，那么就我们而言，我们可以将其视为同类事物(就好像有一个`HasFoo`类)。

GHC 泛型允许我们考虑数据，而不是它的名字，而是它的形状。因此，我们将看到如何将类似 python 的对象系统引入 Haskell。

# 究竟什么是对象？

这都需要对一个物体有所了解。python 对象实际上只是从名称到字段的映射，它们都是动态类型的。所以这也是我们的目标。

```
import qualified  Data.Map.Strict as Map

type Map = Map.Map

data Object = Object
  { objectMeta             :: Map String String
  , objectNamedFields      :: Map String Dynamic
  , objectPositionalFields :: [Dynamic]
  }
  deriving (Show, Typeable) 
```

除了命名字段，我们还将存储关于对象的特殊元数据，比如它的类型和构造函数。字段或者有名字(如在记录中)，或者有位置。

当然，我们想要提取字段和元数据，所以我们需要说明如何获取字段:

```
data Key a = Named String | Positional Int
  deriving (Show, Eq)

get :: Typeable a => Key a -> Object -> Maybe a
get (Named s) = getField s
get (Positional i) = getFieldAt i

getField :: Typeable a => String -> Object -> Maybe a
getField k (Object _ fs _) = Map.lookup k fs >>= fromDynamic

getFieldAt :: Typeable a => Int -> Object -> Maybe a
getFieldAt k (Object _ _ fs) = listToMaybe (drop k fs) >>= fromDynamic

getType :: Object -> Maybe String
getType (Object m _ _) = Map.lookup "__type" m

mkObject :: Typeable a => String -> a -> Object
mkObject k v = Object mempty (Map.singleton k (toDyn v)) [] 
```

这使我们可以获取单个字段，其中用户指定名称，并隐式要求它是特定的类型。如果可以的话我们会归还的。

让我们把这些东西拿出来兜一圈:

```
let os = [mkObject "foo" 'a'
         ,mkObject "foo" (10 :: Int)
         ,mkObject "foo" 'b'
         ,mkObject "bar" 'c'
         ]
mapM_ print . catMaybes $ fmap (get (Named "foo" :: Key Char)) os
> 'a'
> 'b' 
```

请注意，我们必须告诉编译器我们期望返回什么，我们可以通过注释键来实现。我们甚至可以有一个像 clojure 中有用的`get-in`这样的函数，它遍历一系列字段，在最后找到我们想要的东西:

```
getIn :: Typeable a => Key a -> [Key Object] -> Object -> Maybe a
getIn k ks o = foldr (\k mo -> mo >>= get k) (pure o) (reverse ks) >>= get k 
```

其工作原理如下:

```
let o = mkObject "a" (mkObject "b" (mkObject "c" (mkObject "d" True)))
print $ getIn (Named "d" :: Key Bool) [Named "a", Named "b", Named "c"] o
> Just True 
```

# 嗯，那还不是很有用

到目前为止，我们只有一个靠不住的基于地图的 obejct 系统。使用`Object`类型进行任何真正的编程既不符合人体工程学也不实际。相反，我们希望使用普通的 Haskell ADTs，并且能够将它们转换成结构化查询的对象。

这就是泛型派上用场的地方。它们基于这样的观察，即 Alegbraic 数据类型的代数意味着我们可以确定一个可以用来表示所有数据类型的形状的最小子集:

*   `V1` -这是没有构造函数的数据类型。这些并不多，但是一些例子包括`Void`，以及在一些系统中用作标签的幻象数据类型。
*   `U1` -这是没有任何字段的值。这包括像`True`和`False`这样的常见事物以及像`data XS = A | B | C`这样的枚举。在这种情况下，被表示的值是`True`或`A`。
*   有两个构造函数可供选择。这包括像`Bool = True | False`，或者`data Tree a = Tip | Branch (Tree a) (Tree a)`或者`data Either a b = Left a | Right b`这样的事情。在这种情况下，表示的值是 *a Bool* ，或者*a 或 b* ，而不是边本身。当有两个以上选项时，表达式嵌套，如`(a :+: b :+: c :+: ...)`等。
*   `a :*: b`代表两个事物的结合。当一个值有多个字段时，无论是命名字段还是位置字段，都会发生这种情况。在`data Foo = F Int Char Word`中有三个字段，并且都存在，所以这可能是作为`(int :*: (char :*: word))`出现的。最简单的例子就是`(a,b)`，也就是这两样东西的组合。
*   `K1`代表一个叶值本身。
*   保存元数据，包括数据类型名、构造函数名和字段名。而不是在几个地方重复这一点，这是统一到一个表示。

然后，我们的工作是使用 GHC 泛型机制将普通的 Haskell ADTs 转换成这些泛型数据类型，然后将它们转换成对象。因为所有这六个东西都是不同的类型，所以我们需要一个类型类来调度它们:

# 从代表到对象

正常的模式是有两个类型类，一个用于正常的 Haskell ADTs，一个用于结构表示。我们将这里的两个类称为`ToObject`和`ToObject'`

```
class ToObject a where
  object :: a -> Object
  default object :: (Generic a, ToObject' (Rep a)) => a -> Object
  object x = toObject (from x) 
```

在外部包装类中，我们允许类型指定一个自定义实例，但是如果你能把自己转换成上面提到的那些类型中的一个，那么我可以用一种统一的、通用的方式来处理你。

这个实现存在于泛型领域，其中每个构造函数都有一个幻影参数，所以我们期望这个类中的所有类型都是`* -> *`类型。这意味着结构类型类是:

```
class ToObject' f where
  toObject :: f p -> Object 
```

然后我们需要处理六种可能的表示。前几个非常简单:

```
instance ToObject' V1 where
  toObject x = error "impossible" 
```

对于无人居住的类型，我们将永远不会被传递任何实例，因为它们永远不会被构造。我们可以在这里出错。

```
instance ToObject' U1 where
  toObject U1 = Object Map.empty Map.empty [] 
```

对于空值，返回一个空对象。

```
instance ( ToObject' lhs , ToObject' rhs) => ToObject' (lhs :+: rhs) where
           toObject (L1 lhs) = toObject lhs
           toObject (R1 rhs) = toObject rhs 
```

如果我们有一个 LHS，那么处理它，否则处理 RHS。

```
instance ( ToObject' lhs
         , ToObject' rhs) => ToObject' (lhs :*: rhs) where
           toObject (lhs :*: rhs) = toObject lhs <> toObject rhs 
```

如果我们有一个 RHS 和一个 LHS，那么以某种方式将它们结合起来。为此，为我们的对象定义一个半群实例是有帮助的:

```
instance Semigroup Object where
  (Object m1 a fs) <> (Object m2 b fs') = Object (m1 <> m2) (a <> b) (fs <> fs') 
```

有趣的是当我们深入到值和名称时，那么`K1`和`M1`。对于`K1`,我们知道它的值，但是不知道它的名字，如果它有名字的话，那么我们将把它打包成一个对象，放在一个特殊的键下，这个键不是合法的 Haskell 标识符，所以我们可以在以后检测它。我们还想小心区分像`10 :: Int`、`'a' :: Char`这样的值，我们想把它们作为叶子直接粘贴在对象中，以及像`Tree a`这样的 ADT，如果可能的话，我们想进一步分解它们。因此，我们将在这里引入一个新的类型类来在原始值和复合值之间分派:

```
class ToValue a where
  toVal :: a -> Dynamic

  default toVal :: (ToObject a) => a -> Dynamic
  toVal x = toDyn $ object x

instance (ToValue x) => ToObject' (K1 i x) where
  toObject (K1 x) = Object Map.empty (Map.singleton "$value" (toVal x)) [] 
```

现在，当我们到达`M1`节点时，我们需要根据它们是否包含构造函数、数据类型或选择器(*字段*)名称来对它们进行不同的处理。构造函数和数据类型很简单——提取名称并标记对象:

```
metaCon, metaType :: String
metaCon  = "tag"
metaType = "type"

instance (Constructor t, ToObject' f) => ToObject' (M1 C t f) where
  toObject m =
    let o = toObject (unM1 m)
     in o { objectMeta = Map.insert metaCon (conName m) (objectMeta o) }

instance (Datatype t, ToObject' f) => ToObject' (M1 D t f) where
  toObject m =
    let o = toObject (unM1 m)
     in o { objectMeta = Map.insert metaType (datatypeName m) (objectMeta o) } 
```

当我们得到一个选择器时，我们需要根据我们得到的是一个叶节点(一个值)还是另一种类型的对象，以及根据选择器是有名称的(是一个记录字段)还是空的(是位置的)来稍微改变一下行为:

```
instance (Selector t, ToObject' f) => ToObject' (M1 S t f) where
  toObject m =
    let val = toObject (unM1 m)
     in case Map.toList (objectNamedFields val) of
         [("$value", v)] -> case selName m of
                               [] -> val { objectNamedFields = mempty
                                         , objectPositionalFields = [v]
                                         }
                               k -> val { objectNamedFields = Map.singleton k v }
         _ -> case selName m of
                [] -> Object mempty mempty [toDyn val]
                k  -> Object mempty (Map.singleton k (toDyn val)) [] 
```

# 付诸行动

所以现在我们需要一些 ADT 来使用它，例如:

```
data Foo = Foo { fooA :: Int, fooB :: Bool, fooC :: String }
  deriving (Show, Eq, Generic)

data Bar = Bar Double Int deriving (Show, Eq, Generic)

data WhatIDid = Veni | Vidi | Vici deriving (Show, Eq, Generic)

data Nested = N { nInt :: Int, nFoo :: Foo, nBar :: Bar }
  deriving (Show, Eq, Generic)

data Tree a = Tip | Branch { treeNode :: a, treeLHS :: (Tree a), treeRHS :: (Tree a) }
  deriving (Show, Eq, Generic) 
```

对于我们想要结构化处理的所有东西，我们需要选择加入`ToObject`和`ToValue`类。令人欣慰的是，实现并不繁琐，但它们有点样板化:

```
instance ToValue Int where toVal = toDyn
instance ToValue Double where toVal = toDyn
instance (Typeable a) => ToValue [a] where toVal = toDyn
instance ToValue Bool where toVal = toDyn
instance ToValue Char where toVal = toDyn

instance ToValue Foo
instance ToObject Foo

instance ToValue Bar
instance ToObject Bar

instance ToValue WhatIDid
instance ToObject WhatIDid

instance ToValue Nested
instance ToObject Nested

instance (Typeable a, ToValue a) => ToValue (Tree a)
instance (Typeable a, ToValue a) => ToObject (Tree a) 
```

我们可以试一试

```
let t_char = Branch 'a' Tip
                        (Branch 'b' (Branch 'e' Tip Tip)
                                    (Branch 'd' Tip Tip))
getIn (Named "treeNode" :: Key Char) [Named "treeRHS", Named "treeLHS"]
      (object t_char)
> Just 'e'

let t_bar = Branch (Bar 1.0 1)
                   Tip
                   (Branch (Bar 2.0 2)
                           (Branch (Bar pi 0) Tip Tip)
                           (Branch (Bar 42 3) Tip Tip))
getIn (Positional 0 :: Key Double) [Named "treeRHS", Named "treeLHS", Named "treeNode"]
      (object t_bar)
> Just 3.141592653589793 
```

# 把一切都包起来

这不一定是一个严肃的例子，尽管它确实指出了 GHC 泛型所擅长的事情。当您希望根据数据的形状而不是含义来处理数据时，这是减少重复工作的好方法，并且允许第三方工具选择加入。这样的例子通常与外部世界的交互有关，比如 JSON 编码/解码，或者二进制序列化。您可以编写自己的`FromJSON`和`ToJSON`实例，但是如果您没有非常挑剔的需求，为什么不让编译器为您编写呢？然后确保永远不会忘记向编码器添加一个新字段。

甚至这个玩具示例也有有趣的应用，比如非常轻量级的反射(有更好的反射工具，但是这里的 API 非常简单)，或者它可以在配置文件中描述通过值图到配置节点的路径。显然，这不是 Haskell 方法，因为它涉及到放弃名义类型系统的类型安全，以换取结构类型的灵活性，但它表明，只需很少的努力，Haskell 就具有与最单一类型语言相同的灵活性。
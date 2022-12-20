# 我第一次使用 SPARK-Ada

> 原文：<https://dev.to/pinotattari/my-first-experience-with-spark-ada-2onp>

我用 Ada 语言编程，我喜欢它。

> 是的，我知道，听起来像是“出柜”...😉

在 Ada 的世界里，有一件事我一直想尝试:它是 Ada 的一个精简子集，叫做 *SPARK* (与 Apache 无关)，它允许*形式检查*，也就是说，*证明*你的代码的一些属性，比如:没有缓冲区溢出和其他运行时错误，没有未初始化的变量读取，等等。我喜欢用数学证明来武装我的代码(或者至少是它的一部分)😊。

> 好奇 SPARK？看看这篇[介绍 SPARK](https://learn.adacore.com/courses/intro-to-spark/index.html) 的文章

几天前，我决定用我的一个旧函数来试试:一个类似 Ruby 的`split`在分隔符处分割字符串。这是我不久前写的一小段代码，在我的许多程序中安全地使用过:实验用的正确的“*豚鼠*”。

> 如果你好奇，最后的火花码在 [github](https://github.com/fintatarta/ada-tokenizer) 上，请便！😊

首先要做的是用普通数组替换使用`Vectors`(一种动态数组，类似于其对应的`C++`)。你看，你可以想象，`Vectors`使用指针(*访问类型*在 Ada 行话中)并且它们在 SPARK 中是*禁止的*。事实上，指针将允许对象的*别名*，这将使形式检查变得不可能。

> 这并不像听起来那么糟糕:在 Ada 中，相对于`C`或`C++`，使用指针的必要性大大降低了。唯一真正需要的是当你想动态分配结构的时候。

因此，我用所需功能的最小集合编写了自己的伪`Vector`类型。想法是将提取的片段保存在一个足够大的固定数组中(分配给堆栈，因此没有显式指针)，以保存片段(如果你`split`一个长度为`N`的字符串，你最多得到`N+1`个片段)。也许不是非常有效的内存，但很好地满足了我的需求。

产生的代码(出于“教导”的目的，相对于原始代码稍作删减...)如下。这看起来很长，但我只是添加了一些注释来解释更 Ada 风格的语法。

首先是*规范*部分(非常粗略地相当于一个`*.h`文件)

```
with Ada.Strings.Unbounded;     use Ada.Strings.Unbounded;

package Token_Lists 
   with SPARK_Mode => On -- This specs is in SPARK
is

   subtype List_Length is Integer range 1 .. Integer'Last;

   -- "Tagged types" correspond roughly to what other 
   -- languages call "class".  Token_List is the desired
   -- pseudo-vector for holding the pieces. 
   --
   -- The funny (<>) means that the type is parameterized, but the
   -- parameter is not public.  This forces you to initialize 
   -- any variable of type Token_List with some kind of constructor.
   --
   -- Finally, "private" means that the internal details are not
   -- visible and are described later, in the private part. 
   --
   type Token_List (<>) is tagged private;

   function Create (N : List_Length) return Token_List
     with
       Post =>
         Create'Result.Capacity = N and Create'Result.Length = 0;
    --
    -- Post is an "attribute" and it specifies a Boolean expression
    -- that will be true when the function returns (contract). 
    --
    -- The post-condition says that the created list will have 
    -- room for N entries and it will be empty
    --

   function Capacity (Item : Token_List) return Positive;

   function Length (Item : Token_List) return Natural
     with Post => Length'Result <= Item.Capacity;
   --
   -- Number of the pieces currently in the list.  Of course 
   -- it cannot be larger than the capacity, if it happens 
   -- there is something wrong somewhere...
   ---

   procedure Append (List : in out Token_List;
                     What : String)
     with
       Pre'Class =>
         List.Length  < List.Capacity,

       Post =>
         List.Length = List.Length'Old + 1
     and List.Capacity = List.Capacity'Old;
    -- 
    -- The precondition (attribute "Pre") says that before calling
    -- the procedure there must be some room in the list; the
    -- post condition says that after the call there is a new entry,
    -- but the capacity is unchanged (it is obvious to you, not to
    -- SPARK).  SPARK will analyze the body of Append and it 
    -- will try to prove that the contract is respected, that is, 
    -- that the postcondition follows from the precondition.
    --
    -- If you arm correctly the code that calls Append, SPARK will
    -- try to prove that the precondition is always verified. If it 
    -- succeeds you know you'll never have an overflow!
    --

private
   --
   -- Some privacy needed... :-)
   --

   type Token_Array is array (Positive range <>) of Unbounded_String;
   --
   -- The funny "(Positive range <>)" says that the index of 
   -- indexes of a variable of type Token_Array is a range of
   -- positive integers. 
   -- 
   -- Yes, in Ada the array indexes do not *necessarily* start
   -- from zero... They can start from whatever you want... ;-D
   --

   type Token_List (Length : List_Length) is tagged
      record
         Tokens     : Token_Array (1 .. Length) := (others => Null_Unbounded_String);
         First_Free : Positive := 1;
      end record
     with 
       Predicate => 
         Token_List.First_Free <= Integer (Token_List.Length) + 1
     and Token_List.Tokens'Length = Token_List.Length;
    -- 
    -- This is the full definition of Token_List anticipated 
    -- in the public part above.  In Ada it is not possible 
    -- to have some field public and some private (like in C++
    -- or Ruby).  If you put the full definition in the public
    -- part, everything is public (typically considered bad practice),
    -- otherwise everything is private.  Honestly, I never felt the
    -- need of the hybrid solution.
    -- 
    -- "Predicate" is another attribute.  It specifies a condition
    -- that a variable of type Token_List must always satisfy.
    --
    -- If you ask to the compiler to check assertions, the 
    -- compiler will produce code that checks the predicate
    -- at the end of methods ("primitive procedures" in Ada 
    -- jargon) that modify the variable and if the condition 
    -- is not satisfied, an exception is raised, pointing an
    -- accusing finger against the culprit. 
    --
    -- A powerful bug trap!
    --

   function Create (N : List_Length) return Token_List
   is (Token_List'(Length     => N,
                   Tokens     => (others => Null_Unbounded_String),
                   First_Free => 1));

   function Capacity (Item : Token_List) return Positive
   is (Item.Tokens'Last);

   function Length (Item : Token_List) return Natural
   is (Item.First_Free - 1);
end Token_Lists; 
```

现在的*体*(或多或少相当于`*.c` )

```
pragma Ada_2012;
package body Token_Lists 
   with SPARK_Mode => On -- This body is in SPARK
is

   ------------
   -- Append --
   ------------

   procedure Append
     (List : in out Token_List;
      What : String)
   is
   begin
      --
      -- If you look at the definition of Token_List 
      -- you'll see that List.Tokens is an array. 
      -- List.Tokens'Last is the last index of Lists.Tokens
      -- 
      if List.First_Free > List.Tokens'Last then
         raise Constraint_Error;
      end if;

      List.Tokens (List.First_Free) := To_Unbounded_String (What);
      List.First_Free := List.First_Free + 1;
   end Append;

end Token_Lists; 
```

即使你不知道阿达，我想你应该能理解代码。

如你所见，`Append`非常简单，显然是正确的，对吗？我的意思是，开头的`if`停止了所有超出数组`List.Tokens`的试探性写入，对吗？

SPARK 抱怨说，它无法证明溢出不会发生。我的第一反应是类似于“你疯了吗？你没看到支票上面的第四行吗？你怎么了？”

无论如何，错误信息旁边有一个“放大镜”图标。我点击它，我得到了，火花的礼遇，一个*反例*

```
 [Counterexample] List = (Length => ?, First_Free => 2147483647) 
 and List.Tokens'First = 1 and List.Tokens'Last = 2147483647 
```

什么东西...？啊！...是的，好吧，你是对的...

以防你一下子没认出来，`2147483647`是 2^31 一号。**如果**我创建了一个有 2g 条目的`Token_List`(怀疑它是否适合堆栈...无论如何...)**和**我加满了，**然后**我*会*实际上有溢出。

现在我有两个选择:第一个是说我永远不会使用 2g 的条目，并忽略大小写；第二个是使它成为官方的，你最多可以有 2^31 -2 条目。这真的很容易做到:只需在*规格*文件中修改行

```
subtype List_Length is Integer range 1 .. Integer'Last; 
```

用于定义列表长度的类型为

```
subtype List_Length is Integer range 1 .. Integer'Last-1; 
```

注意末端的 *-1* 。因为这个类型在构造函数
中使用

```
function Create (N : List_Length) return Token_List; 
```

这保证了你永远不会用大于 2^31 -2 的`N`来调用`Create`(如果你试图这样做，会引发一个异常)。这安抚的火花。

在捕捉到这个*边界线条件*错误后，我开始触发其余的代码，捕捉另外两个边界线情况。

总的来说，我可以说原始代码很好，因为错误是由一些非常极端的条件触发的；然而，很高兴知道现在一切都干净了...
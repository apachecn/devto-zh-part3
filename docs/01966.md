# 7 个应用程序的建模练习。

> 原文：<https://dev.to/antonrich/modeling-exercise-for-7-apps-2324>

我想出了一个主意，我可以先通过建模应用程序来训练我的建模技能。某种形式的强化练习。

让我们开始吧:

### TH app

*   目标:
    *   训练识别单词是否有浊音的能力。

#### MVP:

```
- Show one word out of a list of words iteratively.
- Ability to answer with key events. Left means voiceless, right means voiced. 
```

#### 成熟的应用程序:

```
- Show a list or results at the end.
- Randomize the list of words.
- The ability to get the words from a text from a website 
```

#### 型号为 MVP:

```
-- Model

type TH
    = Soft
    | Hard

type Correctness
    = Correct
    | Incorrect

type alias Model =
    { words : List String
    , answer : Maybe TH
    , correctness : Maybe Correctness
    } 
```

#### 全吹模型:

为了让它结束，我需要添加两个页面播放和结果的状态。我还想保留结果，并用它们显示一个表。

```
-- Model

type TH
    = Soft
    | Hard

type Correctness
    = Correct
    | Incorrect

type State
    = Playing
    | Results

type alias Result =
    ( TH, Correctness )

type alias Model =
    { words : List String
    , answer : Maybe TH
    , correctness : Maybe Correctness
    , state : State
    , results : List Result
    } 
```

### 呼吸 app。

*   目标:
    *   我有一长串不同的呼吸练习。
    *   如果你一个接一个地做，效果会最好。
    *   问题是说明书是俄文的，所以我需要先翻译一下。稍后我会加入要点。我想我甚至可能已经有了译本。

#### MVP:箱式呼吸。[这将是 mvp 版本。这个应用程序很棒。看一看吧。](https://play.google.com/store/apps/details?id=pl.orbitemobile.breathair&hl=en&showAllReviews=true)

#### 型号:

```
-- Model

type State
    = TimingState
    | BreathingState
    | TheEndState

type alias Model =
    { inhale : Inhale
    , inhalePause : InhalePause
    , exhale : Exhale
    , exhalePause : ExhalePause
    , duration : Duration
    , state : Timings
    }

-- There is going to be three states:
-- 1) Choosing the timings
-- 2) The actuall breathing
--    - There is gotta be some pause button during that process
-- 3) The end state.

-- Four views:
-- 1) inhale    has a min 3 sec
-- 2) inhale pause
-- 3) exhale    has a min 3 sec
-- 4) exhale pause
-- 5) the overall duration. Min 2 min - max 60 min.

-- All of the fields can have different time length.

-- each of the above has a length in seconds
-- a checkbox for making the thing move synchroniously. 
```

### 笔记 app。更多关于那个应用的信息[点击这里](https://dev.to/antonrich/note-app-practice-with-elm-some-struggles-as-well-1g8p)

*   目标:
    *   尝试做笔记。

#### MVP:

*   两列。第一个有一个输入字段，另一个显示输出。

### 打地鼠游戏。

*   目标:
    *   从韦斯博斯的 js 30 课程中克隆一个[打地鼠游戏](https://www.youtube.com/watch?v=toNFfAaWghU)。

#### MVP:

*   游戏本身。

#### 型号

```
-- Model

type alias Model =
    { holes : List Hole
    , random : Int -- for randomization.
    , points : Int -- for keeping score
    , numberOfAttempts : Int -- If the player is fast will he have more attemps.
    }

-- What will the duration be based on?
-- I think it should be based on time.
-- a fast player will have more attempts.

-- What about levels? For now I will ignore that aspect.

type Hole
    = Empty
    | Full -- the mole is in the hole 
```

起初，这个模型看起来很简单，但我想了很多之后，我想出了这个模型。

### 时钟

*   目标:
    *   [从韦斯博斯的 js 30 课程中克隆一个时钟](https://www.youtube.com/watch?v=xu87YWbr4X0)

#### MVP:

```
- A cloock with a seconds hand. 
```

#### 成品版本:

```
- The clock itself with hours, minutes and seconds hands. 
```

#### 型号:

```
-- Model

type alias Model =
    { seconds : Seconds } -- the rest will be similar with just different timings

type alias Seconds =
    { seconds : Time.Posix
    , svgPosition : { x, y } -- x the center of the circle, and y will be the end
                             -- of the hand and will tell the angle.
    } 
```

### 阿拉伯语英语聊天室

*   目标:
    *   一个把信息转换成阿拉伯英语的聊天室。阿拉伯语有元音，但实际上没有元音字母。你能在没有元音字母的情况下阅读英语吗？

#### MVP:

```
- A chat window where you can communicate back and forth. The output is processed with an Arabic English algorithm. 
```

#### 型号:

我还不知道如何建模这个应用程序，因为必须有这个应用程序的后端。当我想到后端会是什么，我会建模应用程序。

### 企鹅迷宫游戏。

*   目标:
    *   克隆 Lumosity 的企鹅追逐游戏。非常有趣的游戏。在我手机上经常播放这个。

#### MVP:

*   一个迷宫和一只可以走过迷宫的企鹅。

#### 全 app:

*   旋转迷宫
*   与你竞争的人工智能企鹅
*   级别

#### 型号:

老实说，这超出了我的能力范围，我甚至不知道如何处理这件事。尽管如此，我还是要试试:

```
-- Model

type alias Model =
    { maze : Maze
    , entryPoint : EntryPoint -- penguin's start position
    , fishPoint : FishPonit -- The end point point. Where the penguin finally get's the fish.
    , penguin : Penguin
    }

type alias Penguin =
    { x : Int
    , y : Int
    } 
```

这个模型只是我在黑暗中拍摄。我有一些关于如何建造迷宫的资源。因此，在了解这些之后，我将相应地改变模型。

就这样了，伙计们。你怎么看待这种运动？你认为这是一个好的 delibarate 做法吗？
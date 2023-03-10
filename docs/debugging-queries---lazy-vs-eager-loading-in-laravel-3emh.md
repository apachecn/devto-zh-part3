# 调试查询 Laravel 中的延迟加载与急切加载

> 原文：<https://dev.to/costicaaa/debugging-queries---lazy-vs-eager-loading-in-laravel-3emh>

如今，框架带来了很多好处。他们带来了太多，我们常常忽略了引擎盖下实际发生的事情，只是想当然。不要误解我，我并不提倡反对框架。我喜欢它们，很长一段时间以来，我认为使用框架而不关心它们的内部是好的。

我依靠魔法。但是，正如一个又老又秃的人不断重复的那样:

> 没有魔法这种东西

所以我开始更加关注我的代码做什么(或者不做什么，hihi)。

介绍够了，让我们切入正题。我们想列出一些职位和每个职位的评论。一种方法是:

```
@foreach($posts as $post)
    <h3>{{$post->name}}</h3>
    @foreach($post->comments as $comment)
        <p>
            {{$comment->text}}
        </p>
    @endforeach
@endforeach 
```

我的模型中定义的关系有:

```
class Post extends Model
{
    protected $table = "posts";
    public function comments()
    {
        return $this->hasMany(Comment::Class);
    }
}

class Comment extends Model
{
    protected $table = "comments";
    public function post()
    {
        return $this->belongsTo(Post::class);
    }
} 
```

简单雄辩的关系。引擎盖下有很多魔法，所以我们可以写这个

```
 // Eager loading
    $posts = Post::with("comments")->get(); 
```

或者更简单，这个

```
 // Lazy loading ( the actual query which brings the comments for a post is called later ) 
     $posts = Post::get(); 
```

记住，显示它们的代码是相同的:

```
 @foreach($posts as $post)
         @foreach($post->comments as $comment)
         ...
         @endforeach
     @endforeach 
```

那么，到底发生了什么？

嗯，在第一种情况下(急切加载)，执行以下两个查询:

```
 DB::enableQueryLog();
    $posts = Post::with("comments")->get();
    $query_dump = DB::getQueryLog();

    // dump value : 
    $query_dump === array:2 [▼
     0 => array:3 [▼
       "query" => "select * from `posts`"
       "bindings" => []
       "time" => 1.47
     ]
     1 => array:3 [▼
       "query" => "select * from `comments` where `comments`.`post_id` in (1, 2, 3)"
       "bindings" => []
       "time" => 0.2
     ]
   ] 
```

这些是 Laravel 在幕后做的查询。

现在，对于惰性加载:

```
 DB::enableQueryLog();
    $posts = Post::get();
    $query_dump = DB::getQueryLog();

    // dump value : 
    $query_dump === array:1 [▼
      0 => array:3 [▼
        "query" => "select * from `posts`"
        "bindings" => []
        "time" => 1.71
      ]
    ] 
```

只有一个查询。但是在视图中，对于我们记录的每个帖子，这个查询:

```
 @foreach($posts as $post)
        @php
            \DB::enableQueryLog();
        @endphp

        <h3>{{$post->name}}</h3>

        @foreach($post->comments as $comment)
            <p>
                {{$comment->text}}
            </p>
        @endforeach
        @php
            $query_dump = \DB::getQueryLog();
            dump($query_dump);
        @endphp
    @endforeach

    // dump value : 
    $query_dump === array:1 [▼
      0 => array:3 [▼
        "query" => "select * from `comments` where `comments`.`post_id` = ? and `comments`.`post_id` is not null"
        "bindings" => array:1 [▼
          0 => 1
        ]
        "time" => 0.2
      ]
    ] 
```

跳过关于设计模式和一些代码应该在哪里的讨论。TL；dr 是查询*不应该在视图中。*

所以我们有 1 个初始查询和 3 个视图。对于这个简单的例子，我们已经将查询数量增加了一倍。

现在想象一下，有 100 个帖子，把这些东西叫做`$post->image->path`和`$post->users_who_like_it`aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa 和可怕的嵌套`$post->users_who_like_it->image->path`。

> $post->image->path *不是*嵌套的，它是 post 和它的 image 之间的简单连接- > path 是 image 的属性
> 
> > $ post-> users _ who _ like _ it-> image-> path*被*嵌套:post 与用户“联合”,然后每个用户与其图像“联合”

是啊，在这上面筑巢会更糟。

现在，你可以做数学，如果你认为这不是一个问题，继续懒惰加载一切。归根结底，这是你的决定。但现在，希望是一个知情的人。

一分钱给你的想法||建议||反馈！

干杯: )
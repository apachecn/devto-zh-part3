# 在脚手架的引擎盖下生成控制器

> 原文：<https://dev.to/saral/under-the-hood-of-scaffold-generated-controller-o3a>

这一周，我一直在做一个待办事项应用，第一次使用了 rails [脚手架](https://dev.to/saral/my-first-rails-scaffold-3gfn)。这产生了大量所需的文件、路径和代码。我真的很想了解脚手架生成的控制器中发生了什么，所以我开始解读字里行间的含义，并试图解开 Rails 的魔法。

首先是生成的 todo_list 控制器中的 index 方法。

```
def index
 @todo_list = TodoList.all
end 
```

我认为这很容易理解，因为我已经完成了一个 rails 项目。已经创建的整个 TodoList 模型将存储在`@todo_list`实例中。这是一个由符号`@`表示的全局实例，这意味着我也能够从视图中访问内容。

然后我通过`rails c`打开了我的 rails 控制台。在这里，我想自己测试一些东西。Rails 控制台是一个很好的实验场所，可以看到某些语法实际上在做什么。在控制台中运行`TodoList.all`后，我对`.all`方法的理解得到了验证。

我想看看如果我试图找出@todo_list 的类会发生什么。为此，在我的 rails 控制台中，我运行了

```
@todo_list = TodoList.all
@todo_list.class 
```

我收到的类类型的输出是`TodoList::ActiveRecord_Relation.`。在使用模型时，我已经多次看到这种类类型。关于 ActiveRecord_Relation 类，我需要理解很多东西，但我的基本理解是:

看起来像我在 Rails 控制台中看到的 activeRecord_Relation，将数据存储在一个数组中。在我的情况下，类似这样的

```
#<ActiveRecord::Relation [#<TodoList id: 1, title: "New todo", description: "Hello this is the first todo", created_at: "2019-02-21 04:51:53", updated_at: "2019-02-21 04:51:53">, 
#<TodoList id: 2, title: "Todo #2", description: "Hello", created_at: "2019-02-21 05:28:20", updated_at: "2019-02-21 05:28:20">]> 
```

因为 acitveRecord_Relation 是一个数组，所以我可以对它进行迭代，并显示内容，这正是在视图中所做的。这也意味着，我可以通过索引来访问数组中的元素。这正是我接下来试图在控制台中验证我的声明的方法。

```
TodoList.all[1]
TodoList.all[1].created_at
TodoList.find(1).created_at 
```

我可以用第一个语法访问 TodoList 模型的第二个元素。对于第二个，我可以查看第二个数据的 created_at time 列。最后，使用第三种语法，我能够查看 id 为 1 的数据列。

提示:看看索引和使用 id 的 find 方法之间的区别。

我敢肯定，activeRecord_Relation 还有更多的内容。然而，我对它微不足道的了解让我得以一瞥。希望将来会有一个关于 acitveRecord_Relation 本身的帖子，因为我对它有更好的理解。

接下来，我查看了支架生成的 create 方法。这里有些东西非常有趣

```
def create
    @todo_list = TodoList.new(todo_list_params)

    respond_to do |format|
      if @todo_list.save
        format.html { redirect_to @todo_list, notice: 'Todo list was successfully created.' }
        format.json { render :show, status: :created, location: @todo_list }
      else
        format.html { render :new }
        format.json { render json: @todo_list.errors, status: :unprocessable_entity }

      end
    end
  end 
```

让我感兴趣的是有时会调用`new`方法，其他时候我曾见过使用`create`方法向模型添加条目。我的问题是“如果两个方法行为相同，那么两者之间有什么区别？”

我在这里找到了我的答案[。](https://stackoverflow.com/questions/9791386/differences-in-rails-between-new-save-and-create%20sorta%20sums%20it%20up)

然后我去 rails 控制台用`create`和`new`方法做了一点实验。

```
TodoList.new id:4 , title:"Todo 3", description:"What is up?" 
```

这种语法有效，我没有得到任何错误，但是，数据不会写在模型中。所发生的是使用`new`方法只是实例化，而不是保存到数据库。为了保存到数据库，我们需要将`save`方法与`new`方法和
方法结合使用

```
test = TodoList.new id:4 , title:"Todo 3", description:"What is up?”
test.save 
```

这将测试的内容保存到模型 TodoList 中。当我们想到这一点时，`new`和`save`方法一起工作类似于`create`方法。然而，正如 [stackoverflow 回答](https://stackoverflow.com/questions/9791386/differences-in-rails-between-new-save-and-create%20sorta%20sums%20it%20up)所描述的。

对我来说比较有意义的答案是

虽然 create 调用 new 然后 save 是正确的，但是这两种方法的返回值有很大的不同。
根据对象是否成功保存到数据库，Save 返回 true 或 false。无论对象是否被保存，Create 都将返回模型。这对上面的代码有影响，因为即使对象验证失败并且没有保存，if 语句的顶部分支也总是会被执行。如果您使用带有分支逻辑的 create，您将面临静默失败的风险，而如果您使用 new + save，则不会出现这种情况。

最后，介绍给我的一个新特性是 respond_to 块。这个块允许我以标准 html 格式或 json 格式呈现我的数据元素。为了快速验证，我在浏览器中输入了。我在 URL(localhost:3000/todo _ lists/27 . json)的末尾添加了 json，并获得了 JSON 中数据的呈现版本。

目前，我不知道如何处理这些信息。我所知道的是 json 是 web 开发中的一个重要元素。为什么，以及如何在我的编码之旅中找到答案。
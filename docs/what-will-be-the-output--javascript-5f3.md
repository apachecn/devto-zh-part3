# 输出会是什么？JavaScript

> 原文：<https://dev.to/nitishk72/what-will-be-the-output--javascript-5f3>

### 我有一个程序，有点疯狂。

这是程序，我想知道你们中有多少人已经知道这个概念。

```
 var boy = {
            name:"Natasha",
            birthday : {
                date : 1,
                month : 'April',                    
            }   
        }

    var girl = { ...boy } ;  // copying object

    boy.name = 'Nitish' ;  
    boy.birthday.date = 31 ;
    boy.birthday.month = "December" ;

   console.log(boy) ;

   console.log(girl); 
```

这些类型的问题通常会在面试中被问到。

## `Comment you answer`

更多类似问题请跟我来
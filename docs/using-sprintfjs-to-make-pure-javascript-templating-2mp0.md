# 使用 sprintf.js 制作纯 javascript 模板

> 原文：<https://dev.to/luciunknown/using-sprintfjs-to-make-pure-javascript-templating-2mp0>

如果您只处理没有前端框架的 js 项目，您可能已经注意到，随着长项目的增长，一些代码块可能会非常重复。一个常见的例子是 html，它保存某个列表中某个项目的信息:

```
const persons = [
    {id:1, name:'Ted'},
    {id:2, name:'Bob'},
    {id:3, name:'Bar'},
    {id:4, name:'Foo'},
]

persons.forEach(function(person, index){
    let item = "<h3><a>"+person.id+"</a> "+person.name+"</h3>";
    document.body.innerHTML+= item;
}); 
```

Enter fullscreen mode Exit fullscreen mode

因此，我们可以改变前面的例子，添加一个简单的模板来获取最常见的 HTML 列表项，如下所示:

```
const Template = {
    getPersonListItem:function(person){
         return "<h3><a>"+person.id+"</a> "+person.name+"</h3>";
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们最终得到了比初始代码稍微好一点的东西:

```
const persons = [
    {id:1,name:'Ted'},
    {id:1,name:'Bob'},
    {id:1,name:'Bar'},
    {id:1,name:'Foo'},
]
const Template = {
    getPersonListItem:function(person){
         return "<h3><a>"+person.id+"</a> "+person.name+"</h3>";
    }
}

persons.forEach(function(person, index){
    let item = Template.getPersonListItem(person);
    document.body.innerHTML+= item;
}); 
```

Enter fullscreen mode Exit fullscreen mode

但是我们在“getPersonListItem”函数上仍然有这个字符串插值。这时 sprintf js 库可以帮助使这些代码更加清晰易读。请记住，这是一个非常简单的例子，但是我们知道表示列表项的 HTML 可能要复杂得多，有时维护起来有点棘手。所以让我们使用 sprintf.js :

```
const persons = [
    {id:1,name:'Ted', alias:'T'},
    {id:1,name:'Bob', alias:'B'},
    {id:1,name:'Bar', alias:'BA'},
    {id:1,name:'Foo', alias:'F'},
]
const Template = {
    personListItem:"<h3><a>%(id)i</a> %(name)s</h3>",
    personEspecialListItem:"<h5>%(alias)s</h5>"
}

persons.forEach(function(person, index){
    let item = sprintf(Template.personListItem, person);
    let title = sprintf(Template.personEspecialListItem, person);
    document.body.innerHTML+= item;
    document.body.innerHTML+= title;
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们没有字符串插值，我们可以将 person 对象直接传递给 sprintf 函数，这允许我们编写更复杂的模板，并在一个地方维护所有与项目相关的 html。
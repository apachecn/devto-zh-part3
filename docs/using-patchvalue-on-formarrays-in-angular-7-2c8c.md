# 在 Angular 7 中对格式数组使用 PatchValue

> 原文：<https://dev.to/crazedvic/using-patchvalue-on-formarrays-in-angular-7-2c8c>

2019 年 2 月 14 日

所以我创建了带有嵌套格式数组的奇特表单，并且能够非常容易地将数据作为文档保存在 Firestore 中。具体来说，我正在处理一个周时间表视图，能够看到它以棱角分明的形式保存是非常棒的。然后是时候将数据重新加载到同一个表单中了。疼痛就是从那时开始的...

实际上，我发现 Angular 的反应形式相当可怕——它们很容易上手，而且不费吹灰之力就能运行，但是要在产品代码中实际使用它们，突然需要一个相当陡峭的学习曲线。在 Angular 7 中，据说与 ngModel 的双向绑定不再可能，所以这只会增加混乱。不过，说够了，我们开始吧。

表格和数据的结构如下

```
Team 
  Lines
    Line
      Players
        Player 
```

Enter fullscreen mode Exit fullscreen mode

所以一个队可以有很多线，每条线可以有很多球员。保持简单。对队伍和玩家的数量没有限制。

我们面临的挑战是，当表单第一次呈现时，它不知道需要为多少行或多少玩家呈现表单元素。我们需要查看数据，解析数据并构建表单，以便它包含我们需要的所有元素。然后，如果我们做得正确，我们就可以对 formgroup 进行 patchValue，所有的值都应该出现。

首先让我们看一些样本种子数据:

```
const seedData =  {
  "team_name": "The Team",
  "lines": [
    {
      "name": "line 1",
      "players": [
        {
          "first_name": "1a",
          "last_name": ""
        },
        {
          "first_name": "2a",
          "last_name": ""
        },
        {
          "first_name": "3a",
          "last_name": ""
        }
      ]
    },
    {
      "name": "line 2",
      "players": [
        {
          "first_name": "1b",
          "last_name": ""
        },
        {
          "first_name": "2b",
          "last_name": ""
        },
        {
          "first_name": "3b",
          "last_name": ""
        }
      ]
    },
    {
      "name": "line 3",
      "players": [
        {
          "first_name": "1c",
          "last_name": ""
        },
        {
          "first_name": "2c",
          "last_name": ""
        },
        {
          "first_name": "3c",
          "last_name": ""
        }
      ]
    }
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

在 ngOnInit 中，我首先如下设置表单:

```
ngOnInit() {
    this.dataModel = Object.create(null);

    // created a simple form with no populated formarray
    // this will be handled by the data load instead
    this.cvForm = this._fb.group({
       team_name: ['', [Validators.required]],
       lines: this._fb.array([
         ])      
    });

     //subscribe to value changes on form
     // only way i've found i can manipulate the form data
     // through code and have it update the underlying dataModel
     this.cvForm.valueChanges.subscribe(data=>{
        this.dataModel = data;
     });
  } 
```

Enter fullscreen mode Exit fullscreen mode

我们来看代码:

```
loadForm(data){
    // create lines array first
    for (let line = 0; line < data.lines.length; line++){
       const linesFormArray = this.cvForm.get("lines") as FormArray;
       linesFormArray.push(this.line);

       //for each line, now add all the necessary player formgroups
       for (let player=0; player < data.lines[line].players.length; player++){
          const playersFormsArray = linesFormArray.at(line).get("players") as FormArray;
          playersFormsArray.push(this.player);
       }
    }

    // once we setup the form with all the arrays and such, we can just
    // patch the form:
    this.cvForm.patchValue(data);
  } 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，我检查了数据，遍历了数据中的子数组，并构建了我的表单。它将创建所有它需要的表单元素，这里是它调用的 get 函数，并把它推入表单数组:

```
get player():FormGroup{
    return this._fb.group({
        first_name: '',
        last_name: ''
    });
}

get line():FormGroup{
    return this._fb.group({
        name: '',
        players: this._fb.array([
         ]),
    })
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，在保存时，最后一个技巧是重置表单，换句话说，清除所有控件并重新开始，这样后续的加载就不会一直添加到现有的 formArrays 之上。据我所知，实际上使用 removeAt 删除所有的正式数组和它们的子数组会破坏我的订阅。所以窍门是这样做的:

```
saveForm(){
    //save form values to storeData and reset the form
    this.storeData = this.cvForm.getRawValue();
    this.cvForm.reset();
    // IMPORTANT: reset will not remove form elements only clear them
    // So we need to clear the FormArrays as well 
    // without losing our subscription! 
    // this seems to work fine:
     (this.cvForm.get("lines") as FormArray)['controls'].splice(0);
  } 
```

Enter fullscreen mode Exit fullscreen mode

我希望这能帮助其他人更快地达到我今天的位置。您可以在这里找到工作代码:

[https://stack blitz . com/edit/angular-patch-value-deep-nested-component-utk3qv](https://stackblitz.com/edit/angular-patch-value-deeply-nested-component-utk3qv)
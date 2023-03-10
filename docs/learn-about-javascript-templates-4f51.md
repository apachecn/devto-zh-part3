# 了解 javascript 模板

> 原文：<https://dev.to/dirkncl/learn-about-javascript-templates-4f51>

现代 javascript 已经提供了模板，这意味着 Javascript 编程目前不需要使用库 handelbars 或 mustcache，因为现代 JavaScript 已经提供了``和${}的组合，我无法解释这一点，但仍然很好奇。

这个模板模型真的不需要做成一个库，而是可以直接使用。与直接使用相比，让我们试验一下由库创建的模板。

```
 <script>     
//templatingjs.js
var Template = function Template (template, Obj, Func) {
        return Template.transform (template, Obj, Func)
      }

      Template.convert = function convert (template, Functions) {
        return ExecTemplateFunc (template, Functions)
      }

      Template.transform = function transform (template, Obj, Functions) {
        if (typeof Obj === 'function') {
          Functions = Obj
          Obj = false
        }
        if (typeof Functions === 'function') {
          Template.convert (template, function (err, func) {
            if (err) return Functions (err)
            try {
                func (Obj)
            }
            catch (err) {
              Functions (err)
              return
            }
            Functions (null, func (Obj));

          })
          return
        }
        return ExecTemplateFunc (template) (Obj)
      }

      var ExecTemplate = function ExecTemplate (template) {
        if (typeof template! == 'string') {
          throw new TypeError ('ExecTemplate: expect `template` to be a string')
        }

        return function obj2func (Objs) {
          if (! (Objs! = null && typeof Objs === 'object' && Array.isArray (Objs) === false)) {
            throw new TypeError ('ExecTemplate: expect `Objs' to be an object')
          }

          var keys = []
          var vals = []

          for (var key in Objs) {
            keys.push (key)
            vals.push (Objs [key])
          }
          return new Function (keys, 'return `' + template + '`') .apply (Objs, vals)
        }
      }

      var ExecTemplateFunc = function ExecTemplateFunc (template, Func) {
        if (typeof Func === 'function') {
          try {
              ExecTemplate (template)
          }
          catch (err) {
            Func (err)
            return
          }
          Func (null, ExecTemplate (template));

          return
        }
        return ExecTemplate (template)
      }
    </script> 
```

```
 <div id='test'></div>
    <script>
      //function
      var UCaseFirstWord = function UCaseFirstWord(val) {
        var tmp = val.split(" ");
        var res="";
        for(i=0;i<tmp.length;i++)
          res += tmp[i].charAt(0).toUpperCase() + tmp[i].slice(1);
        return res
      };
      var rand = function rand(x){return (Math.random()*x)};
      var randLU = function randLU(low, up){ return Math.random() * (up - low) + low}
      //object
      var A = {
        num : 25,
        learn: {
          about: 'javascript  programming,'
        },
        stupid: 'the  more  I  did  not  understand.',
        ucfw: UCaseFirstWord,
        me:'Dirk Nicolaas',
        rand: rand,
        randLU: randLU
      }
      //template
      var sentenceFunc = '1\. I learned ${ucfw(learn.about)} but the more I wanted to know, ${stupid} <br> ${me} <br> ${rand(num).toFixed(4)}<br>  ${randLU(20,80)}';

      //
      var sentenc1 = Template(sentenceFunc, A)
      console.log(sentenc1)
      document.write(sentenc1+'<br>')

      Template(sentenceFunc, A, function (err, sentenc2) {
        if (err) return console.error(err)
        console.log(sentenc2)
        document.write(sentenc2+'<br>')
      })
      /////////////////////////////
      //
      var ThreeJS = `https://threejs.org/build/three.js`;
      var sty = `display:inline`;
      var id = `experimen`;

      sentence = `2\. I learned ${A.ucfw(A.learn.about)} but the more I wanted to know, ${A.stupid} <br> ${A.me}<br> ${A.rand(A.num).toFixed(4)}<br>  ${A.randLU(20,80)}`

      //HTML 
      var y = `
        <section>
          <div id=${id} style=${sty}>${sentence}<br></div>
        </section>
      `;
       document.getElementById("test").innerHTML = y;

        //Javascript loader
       document.write(`
         <script src=${ThreeJS}><\/script>
       `);

    </script> 
```

*由于模板冲突的语法与 yaml 冲突，上述程序使用~~~ yaml

乍一看，这篇文章对专业开发人员毫无用处，但对于那些仍在学习的人来说，它非常有用。

# 结论

*   学习 javascript 编程要纯 javascript，避免使用库或框架。

*   使用库或框架就像我们还在初中或高中，意味着我们接受课本提供的东西，而不需要知道一个命题或公式是从课本的哪里得到的，重要的是使用它。

*   对于不太大的程序，你应该立即使用内置语法，不需要做小函数并被收集在一个库中。
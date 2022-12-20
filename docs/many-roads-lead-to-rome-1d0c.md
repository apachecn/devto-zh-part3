# 条条大路通罗马

> 原文：<https://dev.to/dirkncl/many-roads-lead-to-rome-1d0c>

成为一名编程人员，除了好奇心之外，还必须具备智取的智力/技能(如果在某一方面失败了，必须在其他方面尝试等等)

在学习 javascript 编程的过程中，使用简单的编辑器会更有用，因为我们需要探索我们制作的程序的整个流程，而不需要任何自动更正。如果让我推荐，我就推荐我现在用的(Notepad++)。

找参考资料对于一个想成为程序员的人来说是非常必要的，至于我们要学习的基础参考资料我可以推荐的有 MDN*([https://developer.mozilla.org/](https://developer.mozilla.org/))*，W3School*([https://www.w3schools.com/](https://www.w3schools.com/))*和 stack overflow*([https://stackoverflow.com/](https://stackoverflow.com/))*。我们不需要学习所有的东西，因为如果这就是我们所做的，我们可以独自发疯。我们只是寻找我们需要的东西。我们必须妥善记录每一条信息，以供离线参考。

对初学者来说，让我们试试:

挑战在于用一段 javascript 在 html 中的元素中填充一段 HTML。例如，我们想将< div > Hello < /div >放在 html 中的任何地方，或者调用 js 文件或 css 文件将其放在我们的 html 中。

```
<head>
  <link href="css/cvd.css" rel="stylesheet" />
  <link href="css/mn.css" rel="stylesheet" />
  <script src="js/cmn.js"></script>
  <script src="js/cvs.js"></script>
  <script src="js/mn.js"></script>
</head>
<body>
  <div id='test'>
    <div>Hello</div>
  </div>  
</body> 
```

```
<script>
  function include(htmlcontent, toEl){
    //because of the .createRange() factor, then htmlcontent in the form of an array
    //used include(['contentHTM', ...], 'html' || 'body' || head /* default = 'head' */)
    toEl = toEl||"head";
    var range = document.createRange();
    var docElement = (toEl == "html"||toEl == "head"||toEl == "body")? docElement = document.getElementsByTagName(toEl)[0] : docElement = toEl
    /*      
    if(htmlcontent == null){
      alert(new TypeError('The HtmlContent must be filled in'))
    }
    if (htmlcontent.length == 1 ){
      docElement.appendChild(range.createContextualFragment(htmlcontent[0]));
    }
    if (htmlcontent.length >1 ){
      docElement.appendChild(range.createContextualFragment(htmlcontent));
    }*/
    /*simplified to be*/
    switch (true){
       case htmlcontent == null : alert(new TypeError('The HtmlContent must be filled in'));break;
       case htmlcontent.length == 1 : docElement.appendChild(range.createContextualFragment(htmlcontent[0]));break;
       case htmlcontent.length >1 :docElement.appendChild(range.createContextualFragment(htmlcontent));;break
    }
  };
  /* Examples of its use*/
  //window.onload = (function(){
  //or
  //(function(){
  //or

    /* Examples of calling js or css files */
    var cvsCss = "css/cvd.css",
        mnCss  = "css/mn.css",
        cmJs  = "js/cmn.js",
        cvsJs = "js/cvs.js",
        mnJs = "js/mn.js",
        stySht = "stylesheet";
    var tpl_css = function(cont){return `<link href=${cont} rel=${stySht} \/>`} 
    var tpl_scr = function(cont){return `<script src=${cont}><\/script>`} 
    include([
      tpl_css(cvsCss),
      tpl_css(mnCss),
      tpl_scr(cmJs),
      tpl_scr(cvsJs),
      tpl_scr(mnJs)
    ],"head");
    // or
    //],"html");
    //],"body");

    /*Example of filling html in element*/
    include(['<div id=\'test\'></div>'],'body');
    include(['<div>Hello</div>'],document.getElementById('test'));
  //})();
  //or
  //})();
  //or none

</script> 
```

进一步发展，以更加灵活的用户，即 appendChid 可以选择作为 appendChid 或 insertBefore。

这是一个例子，说明只有用纯 javascript，我们才能创建一个多功能的函数。

## 结论。

1.  做程序员，一定要有好奇心。
2.  我们必须有智取的智慧，因为有句话叫“条条大路通罗马”。
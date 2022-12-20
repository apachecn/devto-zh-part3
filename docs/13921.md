# 名称空间和 Javascript:我不知道我在做什么

> 原文：<https://dev.to/jacoby/namespaces-and-javascript-i-dont-know-what-im-doing-4e1f>

下面是我今天写的一个简单的 JS 库:

```
let rr = {};
rr.data = {};
rr.code = {};

rr.code.listen = function (event){
  console.log(this.responseText);
  location.reload();
}

rr.code.remove_request = function () {
  let id_span = document.getElementById("run_id");
  let run_id = id_span.getAttribute("data-run-id");
  console.log([run_id, Date.now()].join("-"))
  let request_id = prompt("Enter the request in Run #" + run_id + " that you want removed");
  if (!(request_id == null || request_id == "")) {
    let url = ["/cgi-bin/dev/dave/env.cgi", run_id, request_id].join("/");
    console.log(url);
    let xhr = new XMLHttpRequest();
    xhr.addEventListener("load",rr.code.listen);
    xhr.open("POST",url);
    xhr.send();
  }
}

// This is part of the *LIMIT TO TEST* aspect of this task.
// I will likely just make this set the onclick
rr.code.start = function () {
  let junk_drawer = document.getElementById("junk_drawer");
  let button = document.createElement('span');
  button.innerText = "Remove Request";
  button.id = "remove_request";
  button.onclick = rr.code.remove_request;
  button.classList.add("button");
  junk_drawer.appendChild(button);
}

window.onload = rr.code.start; 
```

有几个“常规”选择——按照惯例做出的选择。例如，我把`window.onload`放在底部，因为在某些语言中，如果在定义函数之前使用它，就会出现问题。在我选择的语言中，这并不重要，但我不能 100%确定它在 JS 中是否重要。

在其他地方，我使用 Bootstrap 来制作弹出窗口，只给用户实际的、可操作的选择，而不是让他们填写提示并解析它，但我认为无论如何我都必须在客户端解析它，这是快速而简单的 UI 选择。

我随时随地都用`let`代替`var`。我认为第一个`let rr`将 rr 放在全局范围内，这并没有特别限制什么，但是在任何地方使用它都消除了“我应该在这里使用它吗？”的疑问

然后就是`let rr = {}`。很可能是`let rr = new Object`，但这不是重点。我可以把所有的功能都做成`function sub () {...}`而不是`rr.code.sub = function () {...}`，但是这样我们可能会遇到问题。

我们遇到这些问题是因为你的库并不是所有能在你的页面上运行的东西。我刚刚创建了一个这样的网页—

```
<!DOCTYPE html>
<html>
</html> 
```

–Chrome 开发工具显示`content.js`和`antiphishing.js`正在运行。这个以及你或你的同事编写或包含的任何库，你认为你是唯一一个想要编写`start()`的人吗？

然而，我看到了类似`document.getElementsByTagNameNS()`的东西，并怀疑有一种新的、酷的、更合适但对我来说深不可测的东西。我涉及各种各样的事情，我可以在早上上班的路上开一个全技术的员工会议，所以如果在名称空间方面有任何问题，那都是我的鞋在起作用。

所以，我很好奇:处理 JS 名称空间的最好方法是什么？当你把更多的代码放入一个页面时，你如何确保你不会弄坏你用户的玩具？

如果你有任何问题或意见，我将很高兴听到它。在 [Twitter](https://twitter.com/jacobydave) 或[上问我，在我的博客回购](https://github.com/jacoby/jacoby.github.io)上做文章。
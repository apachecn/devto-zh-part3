# LaraCRUDJS

> 原文：<https://dev.to/cha_m_ra/laracrudjs-33n4>

所以在这篇文章中，我想我写的是几个月前我开发的一些东西。这是我写的一个 JavaScript 库，用来管理 CRUD 函数。

你可以找到它没有 GitHub[https://github.com/chamra/laraCRUDJS](https://github.com/chamra/laraCRUDJS)

下载 zip 文件后，您可以将`laraCRUD.js`文件放在 assets 文件夹中，并在`app.balde.php`文件中引用它。

`<script src="{{ assets('js/laraCRUD.js') }}"></script>`

之后，从`CRUD`类创建一个对象只是时间问题

`const crud = new CRUD()`

您可以覆盖构造器的所有默认值。默认值是，

`{
//form element id
this.form = "formData";
//modal id that include the form
this.modal = "#formModal";
//form submit buttom id
this.submitButton = "#btnSubmit";
//propertie for datatables
this.datatables = "";
//resource url
this.url = "";
//unable page refresh afer a form a submit
this.refreshPage = false;
//current status of the form to be update or create
this.update = false;
//csrf token
this.token = document.head.querySelector('meta[name="csrf-token"]');
//selected items auto increment id
this.id = 0;
}`

调用`crud`上的方法`initCRUDFunctions()`将启动实例所需的基本 crud 函数。

`crud.initCRUDFunctions()`

例如-[https://github . com/cha MRA/laraCRUDJS/blob/master/example/main . js](https://github.com/chamra/laraCRUDJS/blob/master/example/main.js)

帮助总是受欢迎的，所以叉我✌！
# 在 react js 中创建定制动态表的简单方法

> 原文：<https://dev.to/abdulbasit313/an-easy-way-to-create-a-customize-dynamic-table-in-react-js-3igg>

在这篇文章中，我将试着教你如何在`react.`
Ya 中创建一个动态表格。我知道这很简单，但是这篇教程是给初学者的，初学者应该知道如何完成这类工作。

我假设你知道如何创建一个项目以及 javascript 类是如何工作的。如果你不知道，请先阅读这篇文章。

**让我们开始**
我们有*数据*以`array of objects`的形式，就像`APIs.`你也可以使用 *API* 。

让我们创建一个简单的*组件*，并在*状态*下存储*数据*。

```
import React, { Component } from 'react'

class Table extends Component {
   constructor(props) {
      super(props) //since we are extending class Table so we have to use super in order to override Component class constructor
      this.state = { //state is by default an object
         students: [
            { id: 1, name: 'Wasif', age: 21, email: 'wasif@email.com' },
            { id: 2, name: 'Ali', age: 19, email: 'ali@email.com' },
            { id: 3, name: 'Saad', age: 16, email: 'saad@email.com' },
            { id: 4, name: 'Asad', age: 25, email: 'asad@email.com' }
         ]
      }
   }

   render() { //Whenever our class runs, render method will be called automatically, it may have already defined in the constructor behind the scene.
      return (
         <div>
            <h1>React Dynamic Table</h1>
         </div>
      )
   }
}

export default Table //exporting a component make it reusable and this is the beauty of react 
```

Enter fullscreen mode Exit fullscreen mode

我们有 4 个学生的 id，姓名，年龄和电子邮件地址。因为我们的桌子是动态的，所以不管我们有 4 个还是 100 个学生。

### 为表格数据

现在我们想打印出 *Dom* 中的学生数据。我们经常用`map function`来表示数组。
让我们为*表格数据*编写一个单独的*函数*，并在我们的*渲染方法*中调用它。这种方法将使我们的代码更加清晰易读。

```
 renderTableData() {
      return this.state.students.map((student, index) => {
         const { id, name, age, email } = student //destructuring
         return (
            <tr key={id}>
               <td>{id}</td>
               <td>{name}</td>
               <td>{age}</td>
               <td>{email}</td>
            </tr>
         )
      })
   }

   render() {
      return (
         <div>
            <h1 id='title'>React Dynamic Table</h1>
            <table id='students'>
               <tbody>
                  {this.renderTableData()}
               </tbody>
            </table>
         </div>
      )
   } 
```

Enter fullscreen mode Exit fullscreen mode

你可能已经注意到我们的`renderTableData`方法只返回`tr`而不是`tr inside table`。因为`tr`不能单独成为`div`的子元素，所以我们必须在渲染方法中将`tr`包裹在`table and tbody`中。

[![table header](img/857e9dba0f36b307db1a0ba00b2646ed.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OpMLkyjv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/skl750p22a202vxr5qms.png)

我们已经完成了表格数据，表格也应该有一个标题。让我们在标题上工作。

### 为表头

现在我们将为表头写另一个方法。

```
 renderTableHeader() {
      let header = Object.keys(this.state.students[0])
      return header.map((key, index) => {
         return <th key={index}>{key.toUpperCase()}</th>
      })
   }

   render() {
      return (
         <div>
            <h1 id='title'>React Dynamic Table</h1>
            <table id='students'>
               <tbody>
                  <tr>{this.renderTableHeader()}</tr>
                  {this.renderTableData()}
               </tbody>
            </table>
         </div>
      )
   } 
```

Enter fullscreen mode Exit fullscreen mode

`Object.Keys`以`array`的形式给我们所有学生的密钥，我们将它存储在一个变量`header`中。所以我们可以用`map method.`和
来迭代`header (array)`，你可能会想为什么我们不用*来迭代*，这是一样的。原因是当我们想要返回结果时，我们使用 *map* 方法，而 *forEach* 不返回任何东西，它只是迭代数组的元素。

### 造型

让我们在我们的表格中添加一点样式，使它看起来更好

```
#title {
  text-align: center;
  font-family: arial, sans-serif;
}

#students {
  text-align: center;
  font-family: "Trebuchet MS", Arial, Helvetica, sans-serif;
  border-collapse: collapse;
  border: 3px solid #ddd;
  width: 100%;
}

#students td, #students th {
  border: 1px solid #ddd;
  padding: 8px;
}

#students tr:nth-child(even){background-color: #f2f2f2;}

#students tr:hover {background-color: #ddd;}

#students th {
  padding-top: 12px;
  padding-bottom: 12px;
  text-align: center;
  background-color: #4CAF50;
  color: white;
} 
```

Enter fullscreen mode Exit fullscreen mode

[![table header](img/c094e3a2f7354d458dc996a2bac0dc37.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PGY5sL3V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/12sp9jutg4k3ugqxvwlq.png)

就这样，我们完成了基本表。在下一篇文章中，我们将在表格中添加一些特性，比如排序、添加和删除数据。[这里的](https://codepen.io/Basit600/pen/JzeZxo?editors=0010)是项目的 codepen 链接。

在这里你可以阅读如何使用 ReactJS 在表格中创建一个可编辑的文本字段。
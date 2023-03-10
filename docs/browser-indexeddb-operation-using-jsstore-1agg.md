# 使用 jsstore 的浏览器索引数据库操作

> 原文：<https://dev.to/ujjwal_kr_gupta/browser-indexeddb-operation-using-jsstore-1agg>

## 简介

IndexedDB 是 HTML5 中的 NoSQL 数据库技术，它允许在浏览器中存储数据和进行不同的数据库操作。

索引的属性 DB :-

*   这是一项 NoSQL 数据库技术。
*   它为不同的数据库操作提供了多个 API。所有 API 都是异步的。
*   它允许以 blobs 的形式存储文件。
*   它使用索引在数据中实现高性能搜索。
*   它是 WebSQL 的替代方案，因为 WebSQL 现在已被弃用。

在这篇文章中，我将描述如何使用 jsstore 在 indexeddb 中进行 CRUD 操作。我将解释 JsStore 给出的例子。所以，如果你想看看或者想用。以下是链接。

[https://github . com/ujjwalgupta official/JsStore/tree/master/examples/Simple % 20 example](https://github.com/ujjwalguptaofficial/JsStore/tree/master/examples/Simple%20Example)

## 咱们码吧

首先，创建一个 HTML 页面，比如说 index.html。将下面的代码粘贴到 index.html 内部。

```
<!DOCTYPE html>  
<html lang="en">  
<head>  
    <meta charset="UTF-8">  
    <meta name="viewport" content="width=device-width, initial-scale=1.0">  
    <meta http-equiv="X-UA-Compatible" content="ie=edge">  
    Crud Demo using jsstore  
</head>  
<body>  

</body>  
</html> 
```

Enter fullscreen mode Exit fullscreen mode

### 安装

安装 JsStore 有不同的方法。检查安装文件-[http://jsstore.net/tutorial/installation/](http://jsstore.net/tutorial/installation/)

在本文中，我将从官方资源库下载 JsStore。从[https://github.com/ujjwalguptaofficial/JsStore](https://github.com/ujjwalguptaofficial/JsStore)下载回购文件并解压。现在，打开文件夹并导航到- dist 文件夹。在那里，你会看到许多文件。对于本文，我们需要复制两个文件——jsstore . min . js、jsstore.worker.min.js。

让我们将脚本包含在 HTML 页面中，所以现在，我们的 HTML 变成了。

```
<!DOCTYPE html>  
<html lang="en">  
<head>  
    <meta charset="UTF-8">  
    <meta name="viewport" content="width=device-width, initial-scale=1.0">  
    <meta http-equiv="X-UA-Compatible" content="ie=edge">  
    Crud Demo using jsstore  
    <script src="jsstore.min.js"></script>  
</head>  
<body>  
<h4>We have included JsStore in this html code.</h4>  
</body>  
</html> 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们需要启动 JsStore 以便使用。您可以在第 13 行看到，我已经创建了一个 JsStore 实例，并将其存储在一个变量连接中。

```
<!DOCTYPE html>  
<html lang="en">  
<head>  
    <meta charset="UTF-8">  
    <meta name="viewport" content="width=device-width, initial-scale=1.0">  
    <meta http-equiv="X-UA-Compatible" content="ie=edge">  
    Crud Demo using jsstore  
    <script src="jsstore.min.js"></script>  
</head>  
<body>  
<h4>We have included JsStore in this html code.</h4>  
<script>  
    var connection = new JsStore.Instance(new Worker('jsstore.worker.js'));  
</script>  
</body>  
</html> 
```

Enter fullscreen mode Exit fullscreen mode

现在，变量“connection”将用于执行数据库操作。

**注意:-** 当你打开 HTML 文件时，你会在浏览器中看到一个错误。这是因为在一些浏览器中，比如 Chrome，文件协议无法访问 worker。因此，请使用 HTTP 服务器使其工作。

### 创建数据库

JsStore 遵循 SQL 方法创建数据库——数据库由表组成，表由列组成。让我们看看如何在 JsStore 中创建数据库模式。

```
function getDatabase() {  
    var tblStudent = {  
        name: "Student",  
        columns: [{  
                name: "Id",  
                primaryKey: true,  
                autoIncrement: true  
            },  
            {  
                name: "Name",  
                notNull: true,  
                dataType: "string"  
            },  
            {  
                name: "Gender",  
                dataType: "string",  
                default: 'male'  
            },  
            {  
                name: "Country",  
                notNull: true,  
                dataType: "string"  
            },  
            {  
                name: "City",  
                notNull: true  
            }  
        ]  
    }  
    var dataBase = {  
        name: "Students",  
        tables: [tblStudent]  
    }  
    return dataBase;  
} 
```

Enter fullscreen mode Exit fullscreen mode

所以我们所做的-

*   创建了将返回数据库模式的函数“getDataBase”。在函数内部，我们创建了一个表- tblStudent。tblStudent 有一个包含列列表的属性 Columns。
*   创建一个名为“students”的数据库，并将 tblStudent 传递给该数据库。
*   有趣的是，我们使用的选项有:自动增量，默认，notNull 等等。在 indexedDb 中默认情况下不支持。但是 jsstore 提供了。

现在，我们已经轻松地创建了 DB 模式。但是数据库还没有创建。

现在，让我们创建数据库。

```
function initiateDb() {  
    var DbName = "Students";  
    connection.isDbExist(DbName).then(function(isExist) {  
        if (isExist) {  
            connection.openDb(DbName).then(function() {  
                console.log('db opened');  
            });  
            showTableData();  
        } else {  
            var DataBase = getDatabase();  
            connection.createDb(DataBase).then(function(tables) {  
                console.log(tables);  
            });  
        }  
    }).catch(function(err) {  
        console.log(err);  
        alert(err.message);  
    });  
} 
```

Enter fullscreen mode Exit fullscreen mode

当调用上述函数时，它会检查是否创建了数据库。如果它被创建，那么我们打开数据库，否则创建数据库。您可以看到，我们正在调用 getDatabase 来获取 db 模式，然后将其传递给 createDb API。

现在，我们需要调用 initiated b——我们将在 onload 事件中调用它。

```
window.onload = function() {  
    initiateDb();  
}; 
```

Enter fullscreen mode Exit fullscreen mode

当页面被加载时——连接变量被初始化，db 被创建或打开。创建 db 时，JsStore 也会打开 db 连接。

因此，现在连接变量知道这是您想要执行操作的数据库。

### 插入数据

JsStore 提供了用于插入数据的插入 API。签出插入文档。我们已经创建了一个“学生”表，它包含 Id、姓名、性别、国家、城市等列。

所以表 student 中的一行(值)将是-

```
var value = {  
    Id: 1,  
    Name: 'durgesh',  
    Gender: 'male',  
    Country: 'India',  
    City: 'efr'  
} 
```

Enter fullscreen mode Exit fullscreen mode

但是 Id 是自动增量列，所以我们不应该提供值。

现在，让我们将上述值插入表中。

```
var value = {  
    // Id: 1,  
    Name: 'ffff',  
    Gender: 'male',  
    Country: 'USA',  
    City: 'efr'  
}  

connection.insert({  
    into: "Student",  // table name
    values: [value]  
}).then(function(rowsAdded) {  
    if (rowsAdded > 0) {  
        alert('Successfully added');  
    }  
}).catch(function(err) {  
    console.log(err);  
    alert('Error Occured while adding data')  
}); 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，我们调用 Insert API，传递表名和要插入的值。

### 读取数据

JsStore 具有用于读取数据的 Select API。查看选择文档了解更多信息。所以，假设我们想读取一个表中的所有数据。查询如下所示。

```
connection.select({  
    from: "Student"  
}).then(function(datas) {  
    console.log(datas);  
}).catch(function(err) {  
    console.log(err);  
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在，假设我们想从 id 为 1 的学生中选择数据。在这种情况下，我们需要过滤行& JsStore 提供- `where`来过滤数据。因此查询如下所示。

```
connection.select({  
    from: "Student",  
    where: {  
        Id: 1  
    }  
}).then(function(datas) {  
    console.log(datas);  
}).catch(function(err) {  
    console.log(err);  
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 更新数据

JStore 提供了更新数据的更新 API。查看更新文档。因此，假设我们想要更新 id 为 1 的学生姓名，那么查询将如下所示。

```
connection.update({  
    in: "Student",  
    where: {  
        Id: 1  
    }  
}).then(function(rowsUpdated) {  
    console.log(rowsUpdated);  
}).catch(function(err) {  
    console.log(err);  
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 删除数据

JsStore 提供了删除数据的删除 API。查看更新文档。假设我们要删除一个 id 为 2 的学生，那么查询将如下所示。

```
connection.remove({  
    from: "Student",  
    where: {  
        Id: 2  
    }  
}).then(function(rowsDeleted) {  
    console.log(rowsDeleted);  
}).catch(function(err) {  
    console.log(err);  
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 总结

JsStore 通过其类似 SQL 的 API 使 IndexedDB 变得非常简单和容易。如果你知道 SQL，那么你已经知道 JsStore，你只需要开始编码。

## 引用

*   [http://jsstore.net/tutorial/get-started/](http://jsstore.net/tutorial/get-started/)
*   [https://developer . Mozilla . org/en-US/docs/Web/API/indexed db _ API](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API)
*   [https://github . com/ujjwalgupta official/JsStore/blob/master/examples/Simple % 20 example](https://github.com/ujjwalguptaofficial/JsStore/blob/master/examples/Simple%20Example)
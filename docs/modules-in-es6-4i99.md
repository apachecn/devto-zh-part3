# ES6 中的模块

> 原文：<https://dev.to/maaajo/modules-in-es6-4i99>

ES6 中的模块:

JavaScript 很久以前就有模块了，但是它们是使用外部库实现的。ES6 (ECMAScript 2015)引入了对模块的原生支持。

下面是如何在 ES6 中使用导入/导出的快速指南

*   条款导出

```
// file exportModule.js

function testFunction(param) {
    console.log(param)
};

class TestClass {
    constructor() {
        this.name = test;
    }
};

export { testFunction, TestClass } 
```

*   导入所有条款导出:

```
// file app.js

// importing all
import { testFunction, TestClass } from '/.exportModule'

testFunction('Imported with success!'); 
```

*   导入选定的条款导出:

```
// file app.js

// importing only selected
import { TestClass } from '/.exportModule'

const importedClass = new TestClass(); 
```

*   导入子句通过重命名导出:

```
// file app.js

// importing all
import { testFunction as myWishedName, TestClass } from '/.exportModule'

myWishedName('Imported with success!'); 
```

*   使用不同名称导出子句:

```
// file exportModule.js

function testFunction(param) {
    console.log(param)
};

class TestClass {
    constructor() {
        this.name = test;
    }
};

export { testFunction as exportedFunction , TestClass as exportedClass } 
```

*   名称已更改的导入条款导出:

```
// file app.js

// importing all
import { exportedFunction, exportedClass } from '/.exportModule'

exportedFunction('Imported with success!'); 
```

*   内联导出(不能使用不同的名称导出，因为子句导出可以使用不同的名称):

```
// file exportModule.js

export function testFunction(param) {
    console.log(param)
};

export class TestClass {
    constructor() {
        this.name = test;
    }
}; 
```

*   导入内嵌导出:

```
// file app.js

// importing all
import { testFunction, TestClass } from '/.exportModule'

testFunction('Imported with success!'); 
```

*   默认导出-如果您希望导出单个值或为模块创建后备值。每个模块只能使用一次。

```
// file exportModule.js

export default const myVariableFunction = param => {console.log(param)};

// the same as:

const myVariableFunction = param => {console.log(param)};

export { myVariableFunction as default }; 
```

*   导入默认导出:

```
// file app.js

import myVariableFunction from '/.exportModule'

myVariableFunction('Imported with success!') 
```

*   默认导出已经是一个别名，因此不需要在导入时将其命名为与导出时相同的名称:

```
// file app.js

// import default doesn't require to be sorrounded by {} when importing
import importDefault from '/.exportModule'

importDefault('Imported with success!') 
```

*   混合导出(默认导出和条款导出):

```
// file exportModule.js

function testFunction(param) {
    console.log(param)
}

class TestClass {
    constructor() {
        this.name = test;
    }
}

export default const myVariableFunction = param => {console.log(param)}

export { testFunction, TestClass } 
```

*   混合进口:

```
// file app.js

import importDefault, { testFunction, TestClass } from '/.exportModule'

importDefault('Log this'); 
```

*   此处需要使用别名进行命名空间导入:

```
// file app.js

import * as myNameSpace from '/.exportModule'

myNameSpace.testFunction('Hello World'); 
```

使用导入/导出的一些观察结果:

*   模块需要在 HTML 的脚本标记中添加 type="module "

-

```
 <script src="somepath.js" type="module"></script>
    ```

  - In order to check your code using LiveServer extension with VSCode and modules you have to type the file extension (.js) when specifying the path to the exported module

  -

 ```javascript
    import * as myNameSpace from '/.exportModule.js'
    ```

  - Default mode of modules is strict mode, so you don't have to include 'use strict' in your modules

  - Modules are executed asynchronously

This is my first article, so if you found it interesting or not, let me know! I would love to know what you think about it. If you have some specific observations about import/export modules it would be great if you could share it in comments. 
```
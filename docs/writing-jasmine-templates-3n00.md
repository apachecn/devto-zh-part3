# 编写 Jasmine 模板

> 原文：<https://dev.to/nograviti/writing-jasmine-templates-3n00>

这开始是为了解决我遇到的一个巨大的重构问题，后来变成了一个更大的架构重构。问题是，一些 e2e 和功能测试已经失去控制，并且基于用户的观点，断言是重复的，有细微的变化。直接的解决方案是显而易见的，更新所有的测试并继续前进。然而，我喜欢做一个懒惰的工程师(感谢 [Jem Young](https://twitter.com/JemYoung) )，真的不想再经历这样的过程。

我想到的解决方案是将测试抽象成一个模块。注意:SupportFunctions 模块只是一组处理登录等等的方法。

开始规格文件:

```
/* base spec file 1 */
'use strict';
const SF = require( '../path/SupportFunctions' );

describe( 'landing page', () => {
  beforeAll( () => {
    SF.login( validUser );
  } );

  describe( 'page header', () => {
    it( 'displays the header', () => {
      expect( element( by.css( '.header' ) ).isDisplayed() ).toBe( true );
    } );

    it( 'displays the menu bar', () => {
      expect( element( by.css( '.menu-bar' ) ).isDisplayed() ).toBe( true );
    } );

    it( 'hides the error page', () => {
      expect( element( by.css( '.error-page' ) ).isDisplayed() ).not.toBe( true );
    } );

    /** remaining test here **/
  } );
} ); 
```

```
/* base spec file 2 */
'use strict';
const SF = require( '../path/SupportFunctions' );

describe( 'landing page', () => {
  beforeAll( () => {
    SF.login( invalidUser );
  } );

  describe( 'page header', () => {
    it( 'displays the header', () => {
      expect( element( by.css( '.header' ) ).isDisplayed() ).not.toBe( true );
    } );

    it( 'displays the menu bar', () => {
      expect( element( by.css( '.menu-bar' ) ).isDisplayed() ).not.toBe( true );
    } );

    it( 'displays the error page', () => {
      expect( element( by.css( '.error-page' ) ).isDisplayed() ).toBe( true );
    } );

    /** remaining test here **/
  } );
} ); 
```

如您所见，工作流是相同的，但是断言有不同的期望。下面是我将合并模块以简化维护的两种方式。第一个是抽象 it()方法。

```
/* it() abstraction module */
'use strict';
let ItModularJasmine = ( () => {
  function ItModularJasmine() {}

  ItModularJasmine.prototype = {
    headerAssertion: function( isTrue ) {
      return it( 'displays the header', () => {
        expect( element( by.css( '.header' ) ).isDisplayed() ).toBe( isTrue );
      } );
    },

    menuBarAssertion: function( isTrue ) {
      return it( 'displays the menu bar', () => {
        expect( element( by.css( '.menu-bar' ) ).isDisplayed() ).toBe( isTrue );
      } );
    },

    errorPageAssertion: function( isTrue ) {
      return it( 'displays the error page', () => {
        expect( element( by.css( '.error-page' ) ).isDisplayed() ).toBe( isTrue );
      } );
    }
  }

  return ItModularJasmine;
} )();

module.exports = new ItModularJasmine(); 
```

现在有了我们的测试抽象模块，我们的规范文件变得更加清晰和易于维护。

```
/* it() modular file 1 */
'use strict';
const MJ = require( '../path/ItModuleJasmine.module' ),
      SF = require( '../path/SupportFunctions' );

describe( 'landing page', () => {
  beforeAll( () => {
    SF.login( validUser );
  } );

  describe( 'page header', () => {
    MJ.headerAssertion( true );

    MJ.menuBarAssertion( true );

    MJ.errorPageAssertion( false );
  } );
} ); 
```

```
/* it() modular file 2 */
'use strict';
const MJ = require( '../path/ItModuleJasmine.module' ),
      SF = require( '../path/SupportFunctions' );

describe( 'landing page', () => {
  beforeAll( () => {
    SF.login( invalidUser );
  } );

  describe( 'page header', () => {
    MJ.headerAssertion( false );

    MJ.menuBarAssertion( false );

    MJ.errorPageAssertion( true );
  } );
} ); 
```

It()块并不是唯一可以抽象成模块的东西。也可以抽象出整个 describe()块。看起来是这样的:

```
/* describe() module abstraction */
'use strict';
let DescribeModule = ( () => {
  function DescribeModule {}

  DescribeModule.prototype = {
    pageHeaderAssertions: function( isHeader, isMenuBar, isErrorPage ) {
      return describe( 'page header', () => {
        it( 'displays the header', () => {
          expect( element( by.css( '.header' ) ).isDisplayed() ).toBe( isHeader );
        } );

        it( 'displays the menu bar', () => {
          expect( element( by.css( '.menu-bar' ) ).isDisplayed() ).toBe( isMenuBar );
        } );

        it( 'displays the error page', () => {
          expect( element( by.css( '.error-page' ) ).isDisplayed() ).toBe( isErrorPage );
        } );
      } );
    }
  }

  return DescribeModule;
} )();

module.exports = new DescribeModule(); 
```

现在，规范文件更加清晰和简短。

```
/* describe modular file 1 */
'use strict';
const MJ = require( '../path/DescribeModule' ),
      SF = require( '../path/SupportFunctions' );

describe( 'landing page', () => {
  beforeAll( () => {
    SF.login( validUser );
  } );

  MJ.pageHeaderAssertions( true, true, false );
} ); 
```

```
/* describe module file 2*/
'use strict';
const MJ = require( '../path/DescribeModule' ),
      SF = require( '../path/SupportFunctions' );

describe( 'landing page', () => {
  beforeAll( () => {
    SF.login( validUser );
  } );

  MJ.pageHeaderAssertions( false, false, true );
} ); 
```

与这种模块化模式中的任何东西一样，您可以混合和匹配。下面是对上述描述模块的最后一次重构。

```
/* structure from before */
--- 
MixedModule.prototype = {
  pageHeaderAssertions: function( isHeader, isMenuBar, isErrorPage ) {
    return describe( 'page header', () => {
      this.headerAssertion( isHeader );
      this.menuBarAssertion( isMenuBar );
      this.errorPageAssertion( isErrorPage );
    } );
  },

  headerAssertion: function( isTrue ) {
    return it( 'displays the header', () => {
      expect( element( by.css( '.header' ) ).isDisplayed() ).toBe( isTrue );
    } );
  },

  menuBarAssertion: function( isTrue ) {
    return it( 'displays the menu bar', () => {
      expect( element( by.css( '.menu-bar' ) ).isDisplayed() ).toBe( isTrue );
    } );
  },

  errorPageAssertion: function( isTrue ) {
    return it( 'displays the error page', () => {
      expect( element( by.css( '.error-page' ) ).isDisplayed() ).toBe( isTrue );
    } );
  }
}
--- 
```

## 更新

现在我已经对我的代码进行了重组，下面是我为使生活更简单而做的一些事情，以及这种类型的结构的一些问题。

和几个模板，很明显，我需要一个容器文件来存放所有的 require 语句，否则规范文件的顶部会像这样:

```
'use strict';
const template1 = require( '../path/template1' ),
      template2 = require( '../path/template2' );
/* and so forth */ 
```

容器是直接安装的；

```
module.exports = function() {
  return {
    template1: require( '../path/template1' ),
    template2: require( '../path/template2' )
  /* and so forth */
  };
}; 
```

在规范中，用法如下:

```
'use strict';
const templates = require( '../path/container' );

templates().template1.method();
templates().template2.method(); 
```

### 早期陷阱

在测试这个概念和尝试各种实现机制时，我使用了 console.log()来调试*(不要翻白眼，你也这样做)*，我有几个这样的变量:

```
let input = input || 0; 
```

由于闭包，变量在使用它的方法中不是一个可靠的值。
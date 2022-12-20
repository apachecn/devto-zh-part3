# 如何将 HTML 表单数据获取到 PHP 函数参数中？

> 原文：<https://dev.to/aveeva/how-to-get-html-form-data-into-php-function-arguments-4hgl>

业务逻辑是:如果已经购买了产品，就升级产品

我有一个客户输入表单来获取信息，以及一些应该根据客户的选择自动填充的字段。

屏幕截图:

```
 https://i.stack.imgur.com/Fkyim.png 
```

注意:价格和运输重量是用 Ajax 完成的，

当我们谈到航运成本，我从 Magento 得到它，使用 PHP 函数。

表单:

```
 https://paste.ofcode.org/335FVUhpBGbazQtrcPLVQUs 
```

PHP:

sp_cost.php

```
 https://paste.ofcode.org/hvG2sP9TW9CEPgMMuKXNuw 
```

从上面的代码中，我得到了预定义的值，

$ results = getshippingiformate(' 14419 '，' 1 '，" IN "，" 642001))；

如何从用户条目中获取国家和邮政编码并返回运费？
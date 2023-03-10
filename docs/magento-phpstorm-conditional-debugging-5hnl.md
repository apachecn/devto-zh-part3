# Magento PHPStorm 条件调试

> 原文：<https://dev.to/asrar7787/magento-phpstorm-conditional-debugging-5hnl>

条件调试是 PHPStorm 中一个有用的特性。当你想让你的断点在特定的条件下被触发时，比如在一次迭代中，这会变得非常方便。

我在调试`vendor/magento/module-customer/Model/Customer/DataProvider.php::getAttributesMeta()`。但是我真的不希望调试器在以下代码中的每个客户属性上停止:

`foreach ($attributes as $attribute) {
$this->processFrontendInput($attribute, $meta);
$code = $attribute->getAttributeCode();
....`

相反，我想要的是，类似于——嘿，调试器，当`$code == "group_id"`出现时，告诉我在那一点上发生了什么。

为此，我在第`$code = $attribute->getAttributeCode();`行的断点上设置了一个条件，如下所示:

[![PHPStorm Conditional Debugging](img/aed25f6279983c795d9f21d662a36284.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8WUPhJ_E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zsfshw810kg9m0wydspp.png)

就这样，现在我的调试器只有在属性代码为`group_id`时才会启动。
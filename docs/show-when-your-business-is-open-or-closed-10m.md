# 显示您的业务何时开始或结束

> 原文：<https://dev.to/eecms/show-when-your-business-is-open-or-closed-10m>

任何商业网站的一个很好的补充就是展示你的营业时间。您可以在任何地方使用它，包括您的电话号码、支持表格或公司地址。

## 基本代码

在本例中，我使用了一个条件，其中的`{current_time}`是针对当前小时查询的，使用的是 24 小时制时间的`%H`日期/时间代码变量，因此“17”是下午 5.00。

```
{if "{current_time format='%H'}" >= 09 AND "{current_time format='%H'}" < 17}
  We're open for business!
{if:else}
  We're closed right now
{/if} 
```

Enter fullscreen mode Exit fullscreen mode

这样做的目的是，如果时间大于或等于上午 9.00 且在下午 5.00 之前(准确地说不晚于下午 4.59)，则显示打开消息，否则显示关闭消息。

## 但是我们周末不工作！

那没问题。我们有一个`%N`日期/时间代码，它给了我们可以玩的日期数字，其中“1”代表周一到“7”代表周日。

我们现在可以做的是用另一个条件来包装我们的基本代码，以检查当天的情况。

```
{if "{current_time format='%N'}" < 6}
  {if "{current_time format='%H'}" >= 09 AND "{current_time format='%H'}" < 17}
    We're open for business!
  {if:else}
    We're closed right now
  {/if}
{if:else}
  We don't work weekends, come back on Monday.
{/if} 
```

Enter fullscreen mode Exit fullscreen mode

所以我们在这里说的是，如果当天少于 6 点(即周六/周日之前)，则输出我们的每日开放/关闭消息，否则说“我们周末关闭”。

## 告诫

*   默认情况下,`{current_time}`将总是使用您的本地服务器时间，因此根据您的访问者在世界上的位置，它可能不会反映他们的真实情况。如果您有国际受众，您可以通过迎合不同时区来进一步调整您的代码，有关各种可用的时间格式和参数，请参见[https://docs . expression engine . com/latest/templates/date-variable-formatting . html](https://docs.expressionengine.com/latest/templates/date-variable-formatting.html)。

## -对于动态的时间，你不应该缓存代码所在的模板。我建议你把代码放在一个未缓存的嵌入模板中，你仍然可以缓存父模板来保持你的页面加载速度。

最初由 u.expressionengine.com[的](https://u.expressionengine.com/article/email-form-setting-a-predefined-subject)[罗布·艾伦](https://u.expressionengine.com/author/rob-allen)发布
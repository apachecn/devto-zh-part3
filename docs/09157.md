# 寻找 JavaScript/jQuery 资源来编写更简洁的代码(提供了代码示例)

> 原文：<https://dev.to/mjericsson/looking-for-javascriptjquery-resources-to-write-cleaner-concise-code-code-examples-provided-kna>

作为一个处于更前端的设计驱动角色的个人，有时需要创建定制的 JavaScript，我发现自己总是希望编写更干净、更简洁的代码。

首先，我不是要你调试下面的代码，而是提供你的见解。当您在寻找编写更简单代码的最佳方法时，有哪些好的参考资源？

* * *

我最近在做一个项目，涉及到为一个逐步的表单编写一些自定义函数(前两步包含在本文中)。这将演变成第 3 步和第 4 步，但它们暂时被搁置。

1.  根据您一年中愿意捐赠的金额选择会员级别。这将根据您在继续第 2 步之前选择的级别提供会员详细信息。
    *   如果你是应届毕业生，会员价值会有 50%的变化。
    *   您不能既是当前学生又是最近毕业的学生，因此如果选中了最近毕业的学生，则当前学生选项将被禁用。
2.  给出一些小细节，如名字和姓氏，以及您的电子邮件。该表单将提交用户在步骤 1 中选择的内容，然后将用户重定向到另一个站点，在那里他们可以执行步骤 3 和 4。
3.  创建您的会员帐户。
4.  通过提供更多详细信息来注册您的帐户。

* * *

**我如何设置它有<u>四个主要功能</u>和较小任务的空间:**

*   *change amount()；*
    *   主要的顶级函数，根据是否选中 50%折扣以及各种基础条件函数来更改成员资格值。
*   *change div()；*
    *   如果选中了某个会员级别，则会显示其各自的会员权益，而其他所有权益将被隐藏。
*   *显示级别()；*
    *   如果选择了会员级别，则基于是否 50%折扣的值将在步骤 2 中填充。
*   *reveal steps()；*
    *   分别展示步骤(1-4)。这基本上隐藏了基于您当前所在步骤的所有其他视图。

鉴于 jQuery 的性质，它看起来太多了，而且代码是重复的。我觉得更干净的方法是尽量减少重复，让它尽可能通用。我在下面做了一个简短的演示，这样你可以更好地理解。提前为没有用正确的语法突出显示道歉！

* * *

如果最近的北卡罗来纳大学毕业生(50%的任何水平)被选中，那么所有会员年度和月度值将改变 50%。

```
if($('input[name=halfoff]').is(':checked')) {
    $("#recentUNCGradLevel2 label").removeClass('bg-white').addClass('bg-secondary  text-white');

    var radioTarHeelAnnualValue = (100 / 2);
    var radioTarHeelMonthlyValue = (8.34 / 2);
    $("#tarHeelLevel3").html("<span class='membership-name'>Tar Heel</span>" + "\n" + "<span class='d-block'>$" + radioTarHeelAnnualValue + " Annually</span>" + "\n" + "<span class='d-block'>$" + radioTarHeelMonthlyValue + "/month</span>");
    $(".display-annual-amount.tarHeelAnnualAmount").html('$' + radioTarHeelAnnualValue);

    $("#currentUNCStudentLevel2").html("<label id='currentUNCStudentLevel4' class='btn btn-primary  rounded  disabled'><input id='currentUNCStudentLevel' type='radio' name='what' value='Current UNC Student' data-divid='B' disabled>Current UNC Student" + "\n" + "<span class='d-block'>$25/Annually</span>" + "\n" + "<span class='d-block'>&nbsp;</span></label>");

    var radioRamAnnualValue = (200 / 2);
    var numRamMonthlyValue = Number(16.67 / 2);
    var roundedRamMonthlyString = numRamMonthlyValue.toFixed(2);
    var roundedRamMonthlyValue = Number(roundedRamMonthlyString);
    $("#ramLevel3").html("<span class='membership-name'>Ram</span>" + "\n" + "<span class='d-block'>$" + radioRamAnnualValue + " Annually</span>" + "\n" + "<span class='d-block'>$" + roundedRamMonthlyValue + "/month</span>");
    $(".display-annual-amount.ramAnnualAmount").html('$' + radioRamAnnualValue);

    ... 
```

如果选择了会员级别，则基于是否 50%折扣的值将在步骤 2 中填充。

```
showLevel: function() {
    $(document).ready(function(){
        $("input[type=radio]").change(function(){
            if($("input[type=radio][name=what][value='Tar Heel']").is(':checked')) {
                $(".display-level.tarHeel").addClass('d-inline-block');
            } else {
                $(".display-level.tarHeel").removeClass('d-inline-block').addClass('d-none');
            }

            if($("input[type=radio][name=what][value='Current UNC Student']").is(':checked')) {
                $(".display-level.currentUNCStudent").removeClass('d-none').addClass('d-inline-block');
            } else {
                $(".display-level.currentUNCStudent").removeClass('d-inline-block').addClass('d-none');
            }

            ... 
```

根据点击最近的 UNC Grad (50%任何级别)时选择的会员级别，这将确保在步骤 2 中显示会员级别和年度金额。

```
$(document).ready(function(){
    $("input[type=radio]").change(function(){
        if($("input[type=radio][name=what][value='Tar Heel']").is(':checked')) {
            $(".display-level.tarHeel").addClass('d-inline-block');
        } else {
            $(".display-level.tarHeel").removeClass('d-inline-block').addClass('d-none');
        }

        if($("input[type=radio][name=what][value='Current UNC Student']").is(':checked')) {
            $(".display-level.currentUNCStudent").removeClass('d-none').addClass('d-inline-block');
        } else {
            $(".display-level.currentUNCStudent").removeClass('d-inline-block').addClass('d-none');
        }

        ... 
```

正如你从上面的所有代码中看到的，如果你有 10 个选项可以选择，这是很多的。如果你有更多，这将是令人生畏的。也许对于这种情况，我的方法是可以的，但我只是想继续改进我的代码。

非常感谢！
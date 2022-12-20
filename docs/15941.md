# 使用外部应用选择查询

> 原文：<https://dev.to/chriscooper01/select-query-using-an-outer-apply-316i>

这是一个使用 SQL **外部 apply** 创建选择查询的简单示例，该查询可以创建可查询的自定义子字段。

在显示外层应用逻辑之前，我使用的是临时表和子查询。子表添加了临时表上所需的数据，然后我根据需要查询临时表。现在，我可以在一个更简单、更快速的 select 查询中完成所有工作。

简单的查询

这个例子是在一个 user_extended 表上完成的，这个表是一个键和值对逻辑，充当以后可能会用到的记录的转储组。当插入一个记录并且它包含与 DB 表中的记录相同的键时，DB 表记录被禁用，并且这个新记录被激活。

这个它的目的是检查创建的日期记录是否是活动的并且没有被禁用。

``SELECT Parent.Username, ActiveDate,DisableDate FROM User AS Parent OUTER APPLY ( SELECT TOP 1 MAX (CASE WHEN Child.Active= 1 AND Child.key = 'LastLoggedIn' THEN Child.DateOfCreation END) AS ActiveDate, MAX (CASE WHEN Child.Active = 0 AND Child.key = 'LastLoggedIn' THEN Child.DateOfCreation END) AS DisableDate FROM [Users_Extended] AS Child WHERE Child.UserId = Parent.Id group by DateOfCreation ORDER BY Child.DateOfCreation DESC ) Child WHERE Parent.key = 'LastLoggedIn' AND Parent.Active = 0 AND ActiveDate < DisableDate; ORDER BY Parent.Id DESC``
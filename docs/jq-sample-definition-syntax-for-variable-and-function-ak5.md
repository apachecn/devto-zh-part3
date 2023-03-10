# jq 示例(变量和函数的定义语法)

> 原文：<https://dev.to/ques0942/jq-sample-definition-syntax-for-variable-and-function-ak5>

# jq

## 从文件中使用查询字符串

您可以在如下所示的文件中编写查询。

```
curl "http://localhost:8080/" | jq -f jq_query 
```

## 变量定义

```
echo '{"a": "valueA"}' |\
  jq '. | {"b": "valueB"} as $v | {"a2": .a, "b2": $v.b}'
# output
# {
#  "a2": "valueA",
#  "b2": "valueB"
#} 
```

## 函数定义和 if 语句

```
echo '[{"a": "valueA"}, {"b": "valueB"}]' |  jq '[.[] | 
 def replace(d): d as $d | 
 if (.a == null and .b != null) then {"a": $d.a, "b": .b}
 elif (.a != null and .b == null) then {"a": .a, "b": $d.b}
 elif (.a == null and .b == null) then {"a": $d.a, "b": $d.b}
 else {"a": .a, "b": .b} end;  replace({"a": "valueA2", "b": "valueB2"})]' #  output
#[
#  {
#  "a": "valueA",
#  "b": "valueB2"
#  },
#  {
#  "a": "valueA2",
#  "b": "valueB"
#  }
#] 
```
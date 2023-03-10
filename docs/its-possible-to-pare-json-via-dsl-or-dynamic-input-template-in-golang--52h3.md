# 在 Golang 中，可以通过 DSL 或动态输入模板来解析 JSON

> 原文：<https://dev.to/udomsak/its-possible-to-pare-json-via-dsl-or-dynamic-input-template-in-golang--52h3>

# 举例

## 输入

```
{
   "name" : "udomsak",
   "dataForm" : { 
     "attribute1": "myname is"
     "gender" : "male",
     "location": "Mars"
} 
```

## 解析器语言

这种解析器语言是动态的，不需要用 Golang 的 Struct 编译。这意味着可以从数据库或文件中获取用作解析器。

```
ParserGet:
  - name
  - dataForm.gender 
  - dataForm.location 
```

## 示例请求

```
ProjectA:

http://api.example.com/parser-rule/parser1
  - parser1 load from parser rule file :  "parser1.rule.conf"

ProjectB:
http://api.example.com/parser-rule/parser2
  - parser1 load from parser rule file :  "parser2.rule.conf" 
```
# 使用 cURL 进行 REST API e2e 测试

> 原文：<https://dev.to/kontrollanten/rest-api-e2e-testing-with-curl-i0j>

不久前，我在寻找一种简单的方法来 e2e 测试我用 Flask 编写的 REST API。由于我只对发出 HTTP 请求和解析 JSON 响应感兴趣，我看不出使用任何断言库或测试框架有什么意义。为了提高我的 bash 技能，我决定用 cURL 和 bash 函数来编写它。

## 可读输出

测试是为了给我们控制和容易发现问题的可能性。因此，我做了一些颜色和一个图标，以方便人眼解析结果。

```
Green='\033[0;32m'
Red='\033[0;31m'
Color_Off='\033[0m'

Check_Mark='\xE2\x9C\x94' 
```

## 断言

为了保持测试的简单性，我用“assert equals”测试了所有东西，它是由这个函数运行的。

```
assert_equals () {
  if [ "$1" = "$2" ]; then
    echo -e "$Green $Check_Mark Success $Color_Off"
  else
    echo -e "$Red Failed $Color_Off"
    echo -e "$Red Expected $1 to equal $2 $Color_Off"
    Errors=$((Errors  + 1))
  fi
} 
```

## 解析 JSON

为了解析 JSON，我使用了 jq，并创建了一个助手函数。例如，第一个参数是 JSON 字符串，第二个参数是路径。[0].标题”。

```
get_json_value () {
  echo $1 | jq -r $2
} 
```

## 测试

所以我的测试终于来了。它们看起来是这样的:

```
echo "### START /api/v2/shelters"
shelters_url="localhost:5000/api/v2/shelters/"

echo "When retrieving a shelter accurate properties should be given"
response=$(curl -s "${shelters_url}142784-8")
assert_equals "$(get_json_value "$response" ".shelterId")" "142784-8"
assert_equals "$(get_json_value "$response" ".address")" "Sockerbruksgatan 3"
assert_equals "$(get_json_value "$response" ".slots")" "80" 
```

## 我更幸福吗？

是的。我想用 Python 写会更容易，但这更有趣。我还认为，我们开发人员可能倾向于使用臃肿的框架，使我们远离核心和控制。这是一个很好的教训，让我跳出自己的小框框思考，我认为这是继续学习的必要条件。

[你可以在这里看到完整的测试套件](https://github.com/hitta-skyddsrum/services/blob/78db77d36a5eddef8ef8b4f8178b64e63e0171d9/e2e/shelters.sh)
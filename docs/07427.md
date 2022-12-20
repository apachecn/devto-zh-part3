# 如何在 Go 中解析 json

> 原文：<https://dev.to/onmyway133/how-to-parse-json-in-go-4n12>

## 使用编码/json 解组

*   结构中的属性需要首字母大写

```
import (
    "net/http"
    "encoding/json"
    "io/ioutil"
    "fmt"
)

type MyJsonObject struct {
    Id string `json:"id"`
    Name string `json:"name"`
}

type MyJsonArray struct {
    Data []MyJsonObject `json:"data"`
}

func FetchJson() {
    url := "https://myapp.com/json"
    client := http.Client{
        Timeout: time.Second * 10
    }

    request, requestError := http.NewRequest(http.MethodGet, url, nil)
    request.Header.Set("User-Agent", "myapp")
    response, responseError := client.Do(request)
    body, readError := ioutil.ReadAll(response.Body)

    fmt.Println(requestError, responseError, readError)

    myJsonArray := MyJsonArray{}
    marshalError := json.Unmarshal(body, &myJsonArray)
    fmt.Println(jsonResponse, marshalError) 
} 
```

## 地图

以及如何映射到另一个结构体
[【https://gobyexample.com/collection-functions】](https://gobyexample.com/collection-functions)

```
func Map(vs []JsonStop, f func(JsonStop) *api.Stop) []*api.Stop {
    vsm := make([]*api.Stop, len(vs))
    for i, v := range vs {
        vsm[i] = f(v)
    }
    return vsm
}

stops := Map(jsonResponse.Data, func(jsonStop JsonStop) *api.Stop {
        stop := api.Stop{
            Id: jsonStop.Id, 
            Name: jsonStop.Name,
            Address: jsonStop.Address,
            Lat: jsonStop.Lat,
            Long: jsonStop.Long}

        return &stop
    }) 
```

原帖[https://github.com/onmyway133/blog/issues/199](https://github.com/onmyway133/blog/issues/199)
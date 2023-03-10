# 带 Go 的 CSV 到 JSON 转换器

> 原文：<https://dev.to/fevziomurtekin/csv-to-json-converter-with-go--h29>

扩展名为 CSV 的文件是按照特定顺序编写和保存的文件，数据库用户使用逗号分隔数据。当您要在 Excel 中传输数据时，需要这种类型的文件。我会用 Go 编程语言开发一个程序，可以转换保存到。

## 用过的 CSV 文件

```
## Used CSV Files
plaka,bolge,il,ilce
1,AKDENİZ,ADANA,ALADAĞ
1,AKDENİZ,ADANA,CEYHAN
1,AKDENİZ,ADANA,ÇUKUROVA
1,AKDENİZ,ADANA,FEKE
1,AKDENİZ,ADANA,İMAMOĞLU
......
......
......
3,EGE,AFYONKARAHİSAR,BAŞMAKÇI
3,EGE,AFYONKARAHİSAR,BAYAT
3,EGE,AFYONKARAHİSAR,BOLVADİN
3,EGE,AFYONKARAHİSAR,ÇAY
3,EGE,AFYONKARAHİSAR,ÇOBANLAR
3,EGE,AFYONKARAHİSAR,DAZKIRI
3,EGE,AFYONKARAHİSAR,DİNAR
3,EGE,AFYONKARAHİSAR,EMİRDAĞ
3,EGE,AFYONKARAHİSAR,EVCİLER
3,EGE,AFYONKARAHİSAR,HOCALAR
3,EGE,AFYONKARAHİSAR,İHSANİYE
3,EGE,AFYONKARAHİSAR,İSCEHİSAR
.....
.....
.....
.....
14,KARADENİZ,BOLU,DÖRTDİVAN
14,KARADENİZ,BOLU,GEREDE
14,KARADENİZ,BOLU,GÖYNÜK
14,KARADENİZ,BOLU,KIBRISCIK
14,KARADENİZ,BOLU,MENGEN
14,KARADENİZ,BOLU,MUDURNU
.....
.....
.....
.....
16,MARMARA,BURSA,GÜRSU
16,MARMARA,BURSA,HARMANCIK
16,MARMARA,BURSA,İNEGÖL
16,MARMARA,BURSA,İZNİK
16,MARMARA,BURSA,KARACABEY
16,MARMARA,BURSA,KELES
....
....
....
....
31,AKDENİZ,HATAY,DEFNE
31,AKDENİZ,HATAY,ARSUZ
31,AKDENİZ,HATAY,PAYAS
45,EGE,MANİSA,ŞEHZADELER
45,EGE,MANİSA,YUNUSEMRE
46,AKDENİZ,KAHRAMANMARAŞ,DULKADİROĞLU
46,AKDENİZ,KAHRAMANMARAŞ,ONİKİŞUBAT
47,GÜNEYDOĞU ANADOLU,MARDİN,ARTUKLU
.....
.....
.....
.....
63,GÜNEYDOĞU ANADOLU,ŞANLURFA,HALİLİYE
63,GÜNEYDOĞU ANADOLU,ŞANLIURFA,KARAKÖPRÜ
65,DOĞU ANADOLU,VAN,TUŞBA
65,DOĞU ANADOLU,VAN,İPEKYOLU
67,KARADENİZ,ZONGULDAK,KİLİMLİ 
```

Enter fullscreen mode Exit fullscreen mode

## 用过的库

```
import (
  "encoding/csv"
  "encoding/json"
  "os"
  "strconv"
) 
```

Enter fullscreen mode Exit fullscreen mode

**注:**编码/csv 和编码/json，csv 到 json 的转换库。

## 编程步骤

*   我们正在打开我们的。首先是 csv 文件。

    ```
    src, err := os.Open("illerilceler.csv")
    if err != nil {
    panic(err)
    }
    defer src.Close() 
    ```

*   的。json 文件已创建。

```
dst, err := os.Create("il-ilce.json")
if err != nil {
  panic(err)
}
defer dst.Close() 
```

Enter fullscreen mode Exit fullscreen mode

*   然后，我们打开。csv 文件，我们将它保存到。我们创建的 json 文件

```
records := make([]Record, 0, len(rows)) // We are opening up a number of rows.
  for _, row := range rows {
    plaka, _ := strconv.ParseInt(row[0], 0, 64) // parsing.
    bolge := row[1]
    il := row[2]
    ilce := row[3]
    /*read the data in the first row and add it to the records recorder.*/
    records = append(records, Record{
      Plaka: plaka,
      Bolge: bolge,
      Il:    il,
      Ilce:  ilce,
    })
  }
  /*We're moving the recorder to json.*/
  err = json.NewEncoder(dst).Encode(records)
  if err != nil {
    panic(err)
  } 
```

Enter fullscreen mode Exit fullscreen mode

## 所有代码

```
package main
import (
  "encoding/csv"
  "encoding/json"
  "os"
  "strconv"
)
type Record struct {
  Plaka int64
  Bolge string
  Il    string
  Ilce  string
  // High, Low, Close
}
func main() {
  src, err := os.Open("illerilceler.csv")
  if err != nil {
    panic(err)
  }
  defer src.Close()
  dst, err := os.Create("il-ilce.json")
  if err != nil {
    panic(err)
  }
  defer dst.Close()
  rows, err := csv.NewReader(src).ReadAll()
  if err != nil {
    panic(err)
  }
  records := make([]Record, 0, len(rows)) 
  for _, row := range rows {
    plaka, _ := strconv.ParseInt(row[0], 0, 64) // parseliyoruz.
    bolge := row[1]
    il := row[2]
    ilce := row[3]

    records = append(records, Record{
      Plaka: plaka,
      Bolge: bolge,
      Il:    il,
      Ilce:  ilce,
    })
  }

  err = json.NewEncoder(dst).Encode(records)
  if err != nil {
    panic(err)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

**注:**项目代码可在[这个地址找到。](https://github.com/fevziomurtekin/csv-to-json)
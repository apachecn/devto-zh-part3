# ้ธ้ ๐:ๅๅปบๅ

> ๅๆ๏ผ<https://dev.to/gdotdesign/mint-creating-a-packages-5e9b>

*่ฟๆฏๅฑ็คบ Mint ็นๆง็็ณปๅๆ็ซ ไธญ็็ฌฌๅญ็ฏ๏ผไฝ ๅฏไปฅๅจ่ฟ้ๆพๅฐไนๅ็ๆ็ซ :*

*   *[่่ท๐:ๅฅ้จ](https://dev.to/gdotdesign/getting-started-with-mint-3k2o)T3ใ*
*   *[่่ท๐:็ปไปถ](https://dev.to/gdotdesign/components-in-mint-4a4n)T3ใ*
*   *[่่ท๐:็ปไปถ็ไบไปถๅ็ถๆ](https://dev.to/gdotdesign/mint-events-and-state-of-components-3j3a)*
*   *[่่ท๐:ๅค็ HTTP ่ฏทๆฑ](https://dev.to/gdotdesign/mint-handling-http-requests-2ep3)T3ใ*
*   *[่่ท๐:้ ๅๅ็ด ](https://dev.to/gdotdesign/mint-styling-elements-295o)*

ๅจ่ฟ็ฏๆ็ซ ไธญ๏ผๆๅฐๅไฝ ๅฑ็คบๅฆไฝๅๅปบไธไธชๅๆฅไธไปไบบๅไบซใ

* * *

Mint ๅ็ฝฎไบๅฏนๅ็ๆฏๆโโๅจไปปไฝๅฌๅฑ Git ๅญๅจๅบไธๅฑไบซ็ไปฃ็ โโ่ฟๅ่ฎธไฝ ไธไปไบบๅฑไบซ็ปไปถใๆจกๅ็่ณๅญๅจใ

## ๅๅปบไธไธชๅ

ๅจ่ฟ็ฏๆ็ซ ไธญ๏ผๆไปฌๅฐไธบไธไธชๆๅ็ฝฎ่ฎกๆฐๅจ็`textarea`ๅๅปบไธไธชๅใ

้ฆๅๆไปฌ้่ฆๅๅปบไธไธชๆฐ็ Mint ๅบ็จ็จๅบ(ๅฎๆฌ่บซๆฏไธไธชๅ):

```
โ  Projects git:(master) โ mint init mint-textarea-counter    
Mint - Initializing a new project
โโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโ
โ Creating directory structure...
โ Writing initial files...

There are no dependencies!

There is nothing to do!
โโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโ 
```

Enter fullscreen mode Exit fullscreen mode

## ๆทปๅ ไธไธช็ปไปถ

ๆไปฌๅฐไธบๅไธบ`Textarea.Counter` ( `source/Texarea.Counter.mint` ):
็`textarea`ๅๅปบไธไธช็ปไปถ๏ผ่ไธๆฏ`Main`

```
component Textarea.Counter {
  property onChange : Function(String, a) = (value : String) : Void { void }
  property value : String = ""

  style base {
    border: 1px solid #DDD;

    flex-direction: column;
    display: inline-flex;
  }

  style textarea {
    font-family: sans-serif;
    font-size: 16px;

    padding: 10px;
    margin: 0;
    border: 0;
  }

  style counter {
    font-family: sans-serif;
    background: #EEE;
    font-size: 14px;
    padding: 10px;
  }

  fun handleChange (event : Html.Event) : a {
    event.target
    |> Dom.getValue()
    |> onChange()
  }

  fun render : Html {
    <div::base>
      <div::counter>
        "Character Count: "

        <{
          value
          |> String.size()
          |> Number.toString()
        }>
      </div>

      <textarea::textarea
        onChange={handleChange}
        value={value}/>
    </div>
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

*ๅจๅผๅๆ้ด๏ผๆๅปบ่ฎฎๅๅปบไธไธช`Main`็ปไปถ็จไบๆต่ฏ๏ผๅฎๆฒกๆ่ขซๆทปๅ ๅฐ Git ๅญๅจๅบไธญใ*

## ๅๅปบ Git ๅญๅจๅบ

ไธบไบไธไปไบบๅฑไบซ่ฟไธช็ปไปถ๏ผๆไปฌ้่ฆๅๅปบไธไธช Git ่ตๆบๅบ๏ผๅจ่ฟ็งๆๅตไธ๏ผๆไปฌๅฐๅจ Github ไธๅๅปบไธไธช๏ผๆไปฌ้่ฆๅฐๆไปฌ็ไปฃ็ ๆจ้ๅฐ่ฟไธช่ตๆบๅบ:

```
โ  mint-textarea-counter โ git init
Initialized empty Git repository in /home/.../mint-textarea-counter/.git/

โ  mint-textarea-counter git:(master) โ git remote add origin .../mint-textarea-counter.git

โ  mint-textarea-counter git:(master) โ git add .

โ  mint-textarea-counter git:(master) โ git commit -m "Initial commit."
[master (root-commit) 5250277] Initial commit.
 3 files changed, 60 insertions(+)
 create mode 100644 .gitignore
 create mode 100644 mint.json
 create mode 100644 source/Textarea.Counter.mint

โ  mint-textarea-counter git:(master) git push origin HEAD 
```

Enter fullscreen mode Exit fullscreen mode

็ฐๅจๆไปฌๅทฒ็ปๅปบ็ซไบๅญๅจๅบ๏ผๆฅไธๆฅ็ไบๆๆฏไธบ็ฌฌไธไธช็ๆฌๅๅปบไธไธชๆ ็ญพ:

```
โ  mint-textarea-counter git:(master) git tag 1.0.0
โ  mint-textarea-counter git:(master) git push origin HEAD --tags           
Total 0 (delta 0), reused 0 (delta 0)
To github.com:mint-lang/mint-textarea-counter.git
 * [new tag]         1.0.0 -> 1.0.0 
```

Enter fullscreen mode Exit fullscreen mode

็ฐๅจ่ฟไธชๅๅทฒ็ปๅฏไปฅไฝฟ็จไบใ

## ไฝฟ็จๅ

ๅจๅฆไธไธช Mint ๅบ็จ็จๅบไธญ๏ผๆไปฌๅฏไปฅ้่ฟๅฐๅฎๅฎไนไธบ`mint.json` :
ไธญ`dependencies`ๅญๆฎต็ไพ่ต้กนๆฅไฝฟ็จ่ฟไธชๅ

```
{
  ...

  "dependencies": {
    "mint-textarea-counter": {
      "repository": "https://github.com/mint-lang/mint-textarea-counter",
      "constraint": "1.0.0 <= v < 2.0.0"
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

ๆๅฐ่งฃ้ไธ่ฟฐไปฃ็ ็ๅซไน:

*   ๆไปฌๅทฒ็ปๅฎไนไบไพ่ตๅณ็ณป:`mint-textarea-counter`(่ฟๅฟ้กปๅน้ๅ`mint.json`ไธญ็`name`ๅญๆฎต)
*   ๆไปฌไฝฟ็จ`repository`ๅญๆฎตๆๅฎไบๅฎๆๅจ็ Git ๅญๅจๅบ
*   ๆไปฌๅจ`constraint`ๅญๆฎตไธญๆๅฎไบ็ๆฌ็บฆๆ

ไนๅ๏ผๆไปฌๅช้่ฆ็จ`mint install`ๅฝไปคๅฎ่ฃไพ่ต้กน:

```
โ  test git:(master) โ mint install
Mint - Installing dependencies
โโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโ
โ Constructing dependency tree...
  โ Cloned mint-textarea-counter(https://github.com/mint-lang/mint-textarea-counter.git)

โ Resolving dependency tree...
  โ mint-textarea-counter โ 1.0.0

โ Copying packages...
โโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโโ
All done in 1.231s! 
```

Enter fullscreen mode Exit fullscreen mode

็ถๅๆไปฌๅฏไปฅไปฅๅๆ ท็ๆนๅผไฝฟ็จ็ปไปถ๏ผๅฆๆๅฎๆฏๅจ้กน็ฎไธญๅฎไน็:

```
component Main {
  state value : String = "Initial value..."

  fun handleChange (value : String) : Promise(Never, Void) {
    next { value = value }
  }

  fun render : Html {
    <Textarea.Counter
      onChange={handleChange}
      value={value}/>
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

ๆจๅฏไปฅๅจ่ฟ้ๆพๅฐๅญๅจๅบ:

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [่่ท้](https://github.com/mint-lang) / [่่ทๅบไธๆ](https://github.com/mint-lang/mint-textarea-counter)

### ๅฑไบซ็ปไปถ็็คบไพๅใ

<article class="markdown-body entry-content container-lg" itemprop="text">

# ้ ๅธๅ-ๆๆฌๅบ-ๆๅฐ

่ฟไธชๅญๅจๅบๆฏไธไธชๅจๆฐๅ็ไพๅญใ

</article>

[View on GitHub](https://github.com/mint-lang/mint-textarea-counter)

ไปๅคฉๅฐฑๅฐ่ฟ้๏ผ่ฐข่ฐขไฝ ็้่ฏป๐

* * *

ๅฆๆไฝ ๆณไบ่งฃๆดๅคๅณไบ Mint ็็ฅ่ฏ๏ผ่ฏทๆฅ็[ๆๅ](https://guide.mint-lang.com)๐

ๅจไธไธ้จๅ๏ผๆๅฐๅๆจๅฑ็คบๅฆไฝ[ไฝฟ็จๅ็ฝฎ่ทฏ็ฑ็ณป็ป](https://dev.to/gdotdesign/mint-routing-2h69)๐ๅจ้ฃ้่ง๐
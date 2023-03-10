# Laravel:使用 AJAX 的国家-州-城市下拉列表

> 原文：<https://dev.to/chandni279/laravel--country---state---city-dropdown-with-ajax-hnn>

许多人在这个问题上有困难，他们得不到输出，有些人还抱怨他们得到空的下拉列表，所以我在这里写我的第二篇文章，分享我自己尝试的 html-javascript 文件、控制器文件和路由文件的代码片段。让我们开始吧...

首先我们需要一份 blade.php 文件:

```
 <select class="form-control" name="country" id="country">
            <option value="">Select Country</option>
            @foreach ($countries as $country) 
                <option value="$country->country_id">
                 $country->country_name
                </option>
            @endforeach
        </select>

        <select class="form-control" name="state" id="state">
        </select>

        <select class="form-control" name="city" id="city">
        </select> 
```

然后我们需要一个 JavaScript 文件:

```
 $('#country').change(function(){
        var cid = $(this).val();
        if(cid){
        $.ajax({
           type:"get",
           url:" url('/state') /"+cid, **//Please see the note at the end of the post**
           success:function(res)
           {       
                if(res)
                {
                    $("#state").empty();
                    $("#city").empty();
                    $("#state").append('<option>Select State</option>');
                    $.each(res,function(key,value){
                        $("#state").append('<option value="'+key+'">'+value+'</option>');
                    });
                }
           }

        });
        }
    });
    $('#state').change(function(){
        var sid = $(this).val();
        if(sid){
        $.ajax({
           type:"get",
           url:"url('/city')/"+sid, **//Please see the note at the end of the post**
           success:function(res)
           {       
                if(res)
                {
                    $("#city").empty();
                    $("#city").append('<option>Select City</option>');
                    $.each(res,function(key,value){
                        $("#city").append('<option value="'+key+'">'+value+'</option>');
                    });
                }
           }

        });
        }
    }); 
```

之后，我们需要创建一个控制器，从数据库中获取我们需要的所有数据。下面是一个控制器文件的代码:

```
//For fetching all countries
public function getCounties()
{
    $countries= DB::table("countries")->get();
    return view('index')->with('countries',$countries);
}

//For fetching states
public function getStates($id)
{
    $states = DB::table("states")
                ->where("country_id",$id)
                ->pluck("state_name","id");
    return response()->json($states);
}

//For fetching cities
public function getCities($id)
{
    $cities= DB::table("cities")
                ->where("state_id",$id)
                ->pluck("city_name","id");
    return response()->json($cities);
} 
```

最后但并非最不重要的是路由文件，它用于访问我们创建的函数。所以让我们来看看代码的死记硬背文件:

```
Route::get('/getCountries','YourController@getCountries');
Route::get('/getStates/{id}','YourController@geStates');
Route::get('/getCities/{id}','YourController@geCities'); 
```

**注意:**请给 **url 加上花括号:“(此处两个开始花括号)url('/state ')(此处两个结束花括号)//+cid。**尝试将它放在代码上，但它向我显示一个错误*液体错误:液体变量被禁用*
如果有人有相同的解决方案，请在这里评论，这样下次我上传代码片段时，我可以使用它。

快乐的编码...
感谢您的阅读...
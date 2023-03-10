# 用 C#向 WebAPI 发送数据对象

> 原文：<https://dev.to/chriscooper01/sending-data-objects-to-webapi-in-c-58i8>

[![Alt text of image](img/7fed7edb78997e039ce9706c1350a015.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Jh_bgyoW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c51sqddbbdccu2fft1xh.jpg)

这是一个简单且可重用的代码，可用于在 C#中将数据对象发送到 WebAPIs。这段代码是对我博客上已经发布的代码的改进。

这现在将与 Get 和 parameter webAPI 操作方法一起工作，并且您希望在您的一端使用数据类。该对象需要与 API 参数同名的参数

**调用**
这是调用 API 逻辑的主要方法。这需要 API 完整 URL、API HTTP 动作类型(GET/POST)、返回对象类型

```
public class JSONAPI 
{

    public static object Invoke(string url, string requestMethod, object dataObject, Type returnType)
    {
        object returnObject = null;

        callAPI(url, requestMethod, dataObject, returnType, updateStatus,  ref returnObject);

        return returnObject;
    }

} 
```

**调用 API**
这是接受参数并返回对象的主方法

```
 private static void callAPI(string url, string requestMethod, object dataObject,Type returnType, ref object returnObject)
    {

        try
        {

            HttpWebRequest request = null;
            FoundServices = true;

            createRequest(url, requestMethod, dataObject, ref request);

            if (request != null)
            {

                WebResponse webResponse = null;

                if (getReponse(request,updateStatus,  ref webResponse))
                {
                    updateStatus(SyncServiceTextResource.JSONDataDownloading, 0, ENUMS.MainProgressENUM.UpdateSecondary);

                    Stream webStream = webResponse.GetResponseStream();
                    using (StreamReader reader = new StreamReader(webStream))
                    {
                        using (var json = new Newtonsoft.Json.JsonTextReader(reader))
                        {
                            Newtonsoft.Json.JsonSerializer serializerJ = new Newtonsoft.Json.JsonSerializer();
                            updateStatus(SyncServiceTextResource.JSONDataConverting, 0, ENUMS.MainProgressENUM.UpdateSecondary);
                            returnObject = serializerJ.Deserialize(json, returnType);
                            updateStatus(SyncServiceTextResource.JSONDataConvertingCompleted, 0, ENUMS.MainProgressENUM.UpdateSecondary);
                            System.GC.Collect();
                        }//END using (var json = new Newtonsoft.Json.JsonTextReader(reader))
                    }//END using (StreamReader reader = new StreamReader(webStream))
                }//END if (getReponse(request,updateStatus,  ref webResponse))
            }//END if (request != null)

        }
        catch (Exception e)
        {

        }

    } 
```

**创建请求**
这是用来取参数对象和 GET 或 POST 类型的方法，用 URL 创建调用 API 所需的字符串。

```
 private static bool createRequest(string url, string requestMethod, object dataObject, ref HttpWebRequest request)
    {
        request = null;

        request = (HttpWebRequest)WebRequest.Create(url);

        request.Method = requestMethod;// "POST";
        request.ContentType = "application/json";
        string jsonDataString = setJSONString(dataObject);

        if (dataObject != null && requestMethod.Equals("POST"))
        {

            request.ContentLength = jsonDataString.Length;
            request.Method = requestMethod;// "POST";
            request.ContentType = "application/json";
            //request.SendChunked = true;

            StreamWriter requestWriter = new StreamWriter(request.GetRequestStream(), System.Text.Encoding.ASCII);

            requestWriter.Write(jsonDataString);
            requestWriter.Close();
        }
        else
        {
            var jObj = (JObject)Newtonsoft.Json.JsonConvert.DeserializeObject(jsonDataString);
            var query = String.Join("&",
                            jObj.Children().Cast()
                            .Select(jp => jp.Name + "=" + HttpUtility.UrlEncode(jp.Value.ToString())));

            bool parameters = true;

            if (!parameters)
                url += "?" + jsonDataString;
            else
                url += "?" + query;

            request = (HttpWebRequest)WebRequest.Create(url);// +"?"+ query);

            request.Method = requestMethod;// "POST";
            request.ContentType = "application/json";

        }

        return request.HaveResponse;

    } 
```

**Json 字符串创建**
该方法获取对象并为该对象创建一个 JString

```
 private static string setJSONString(object dataObject)
    {
        var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
        serializer.MaxJsonLength = MAXJSONLENGTH;
        string jsonDataString = serializer.Serialize(dataObject);
        return jsonDataString;
    } 
```

**获得响应**
这个方法将等待 x 的时间，以确保它给了 API 足够的时间来响应。

```
 private  static bool getReponse(HttpWebRequest request, ref WebResponse response)
    {
        bool responseCompleted = false;                       
        System.Timers.Timer tim = new System.Timers.Timer();
        tim.Interval = 2000;       
        timerElapsed = false;
        tim.Start();

        int milSeconWait = 500;//Start with 0.5 seconds wait
        int maxMilSeconWait = 120000;//MAX of 2 Mins
        int timesWaiting = 0;
        bool stillWaiting = false;
        while (!responseCompleted)
        {

            if (timerElapsed)
            {
               responseCompleted = false;
                break;
            }
            responseCompleted = webResponse.IsCompleted;
            if (responseCompleted)
            {
                break;
            }    
           timesWaiting++;

            Console.WriteLine("JSON GET REPSONE SLEEP AND WAIT: " + milSeconWait.ToString() + " Wait loop " + timesWaiting.ToString());
            System.Threading.Thread.Sleep(milSeconWait);
            milSeconWait = INITIALWAIT * timesWaiting;
        }

        if (responseCompleted && webResponse.Result != null)
        {
            response = webResponse.Result;

        }
        tim.Stop();
        tim.Dispose();
        timerElapsed = false;          

        return responseCompleted;
    } 
```

**使用的常数**
下面是通过全逻辑代码使用的常数

```
 private const int MAXJSONLENGTH = int.MaxValue;
    protected static bool FoundServices;
    private static bool timerElapsed = false;
    private static bool secondAttempt;
    private const int INITIALWAIT = 500;//MIN 0.5 second wait 
```

**WebAPI Get Action side**
为了确保 API 将与上述代码一起工作，将需要在方法调用中使用[FromUri]。

```
public class SIMPLEController : ApiController
{

    [HttpGet]        
    public DataClasses.Returns.OBJECTCLASS GET([FromUri]DataClasses.Parameters.OBJECTCLASS parameter)
    {
        var watch = System.Diagnostics.Stopwatch.StartNew();

        DataClasses.Returns.OBJECTCLASS  returnObject new DataClasses.Returns.OBJECTCLASS ();

        //DO LOGIC

        return returnObject;
    }
} 
```
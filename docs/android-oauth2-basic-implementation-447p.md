# Android OAuth2 基本实现

> 原文：<https://dev.to/mesadhan/android-oauth2-basic-implementation-447p>

# 实施任务清单:-

*   [ ]创建 Android 应用程序。
*   [ ]安装库。
*   [ ]需要一些基本配置。
*   [ ]编码实现。
*   [ ]运行和测试应用程序

# 先决条件:-

*   [ ]需要安装带有 SDK 的 Android Studio
*   [ ]需要 Java 的基本知识

# 实现:-

*   [X]创建 Android 应用程序。
*   [X]安装库: [OkHttp](https://square.github.io/okhttp/)

> 在 anroid `build.gradle`中添加 okhttp 库

```
android {
    //... 

    compileOptions {
        targetCompatibility = "8"
        sourceCompatibility = "8"
    }
}

dependencies {
    //...

    implementation 'com.squareup.okhttp3:okhttp:3.14.1'
} 
```

*   [X]需要一些基本配置。

> 打开`AndroidManifest.xml`文件

```
 <manifest>
  //...
  <uses-permission android:name="android.permission.INTERNET" />

    <application
        android:usesCleartextTraffic="true">

        /...
    </application>
</manifest> 
```

为了简化使用`android:usesCleartextTraffic="true"`进行高级定制，使用`android:networkSecurityConfig="@xml/network_security_config"`文件

> 在`res/xml/network_security_config.xml`中创建文件

```
<?xml version="1.0" encoding="utf-8"?>
<network-security-config>
    <domain-config cleartextTrafficPermitted="true">
        <domain includeSubdomains="true">127.0.0.1</domain>
    </domain-config>
</network-security-config> 
```

*   [X]编码实现:

*   创建`BasicAuthInterceptor.java`

```
 import java.io.IOException;
import okhttp3.Credentials;
import okhttp3.Interceptor;
import okhttp3.Request;
import okhttp3.Response;

public class BasicAuthInterceptor implements Interceptor {
    private String credentials;
    public BasicAuthInterceptor(String user, String password) {
        this.credentials = Credentials.basic(user, password);
    }

    @Override
    public Response intercept(Chain chain) throws IOException {
        Request request = chain.request();
        Request authenticatedRequest = request.newBuilder()
                .header("Authorization", credentials).build();
        return chain.proceed(authenticatedRequest);
    }
} 
```

*   然后将代码块添加到`MainActivity.java`

```
 import java.io.IOException;
import okhttp3.Call;
import okhttp3.Callback;
import okhttp3.MediaType;
import okhttp3.OkHttpClient;
import okhttp3.Request;
import okhttp3.RequestBody;
import okhttp3.Response;

public class MainActivity extends AppCompatActivity {

    public OkHttpClient client;
    public static final MediaType CONTENT_TYPE = MediaType.get("application/x-www-form-urlencoded");
    String apUrl = "www.example.com/oauth/token";       // replace host url through your oauth2 server.

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        client = new OkHttpClient.Builder()
                .addInterceptor(new BasicAuthInterceptor("USER_CLIENT_APP", "password"))
                .build();

        String username = "user";
        String password = "password";
        String grant_type = "password";
        String requestData = "grant_type=" + grant_type + "&username=" + username + "&password=" + password;

        try {
            loginRequest(apUrl, requestData);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public void loginRequest(String apUrl, String requestData) throws IOException {

        RequestBody body = RequestBody.create(CONTENT_TYPE, requestData);
        Request request = new Request.Builder()
                .url(apUrl)
                .addHeader("Content-Type", " application/x-www-form-urlencoded")
                .post(body)
                .build();

        Call call = client.newCall(request);
        call.enqueue(new Callback() {
            @Override
            public void onFailure(Call call, IOException e) {
                Log.e("CallBackResponse", "onFailure() Request was: " + call.request());
                e.printStackTrace();
            }

            @Override
            public void onResponse(Call call, Response response) throws IOException {
                Log.i("CallBackResponse ", "onResponse(): " + response.body().string());
            }

        });
    }
} 
```

*   [X]最后运行并测试您的应用程序，并检查“Logcat”中的响应。

# 从源代码运行并测试应用程序

*   [ ]克隆应用程序[https://github.com/mesadhan/android-oauth2-sample.git](https://github.com/mesadhan/android-oauth2-sample.git)
*   [ ]在 android studio 中导入应用程序。
*   【】将`MainActivity.java`文件中的`apUrl`地址`http://www.example.com/oauth/token";`更改为`oauth2 server host url`。
*   [ ]运行并检查 logcat

# 感谢阅读
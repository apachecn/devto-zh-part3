# Unity Web Request 如何为你的 Rest API 使用 UnityWebRequest？

> 原文：<https://dev.to/balconygames/how-to-use-unitywebrequest-for-your-rest-api-23e0>

当我开始与 unity 合作时，目标是拥有跨平台游戏，并将游戏部署到 App Store、Google Play、亚马逊、脸书、Web、Chrome Extension，以覆盖尽可能多的平台。这个想法是好的，但是它需要关注你计划使用的库。

第一次我决定使用基于`System.Net`名称空间的 [HttpClient](https://assetstore.unity.com/packages/tools/network/http-client-79343) ，但是由于编译成 WebGL `System.Net`和`HttpClient`被排除在构建之外，游戏和 API 之间也没有联系。

解决办法就是用`UnityWebRequest`。

我的例子如何用 JSON 构建 GET，POST 请求。

```
using System;
using System.Collections.Generic;
using UnityEngine.Networking;
using System.Collections;
using Newtonsoft.Json;
using UnityEngine;
using System.Text;

namespace Jumper.Network
{
    public abstract class API
    {
        // TODO: add https certificate verification
#if UNITY_WEBGL
        // WebGL version is loaded by browser under https protocol.
        public const string HOST = "https://api.example.com/client/";
#else
        public const string HOST = "http://api.example.com/client/";
#endif 
        public static readonly string TOKEN_QUERY_PARAM = "?token=";
        public static readonly string REGISTER_PATH = "users/register";

        private const string USER_ID_KEY = ":user_id";

#region internal methods for http client
        internal static string path(string path, Dictionary<string, string> properties)
        {
            // in çase if user has no user_id because of missing internet connection on boot
            // we should use /games namespace for sending requests.
            if (properties.ContainsKey(USER_ID_KEY) && !string.IsNullOrEmpty(properties[USER_ID_KEY]))
            {
                return resolve(
                    GAME_PATH + "/:game_id/sessions/:user_id/" + path + TOKEN_QUERY_PARAM + ":token",
                    properties);
            }

            return resolve(
                GAME_PATH + "/:game_id/" + path + TOKEN_QUERY_PARAM + ":token",
                properties);
        }

        internal static string resolve(string path, Dictionary<string, string> properties)
        {
            foreach(var kv in properties)
            {
                path = path.Replace(kv.Key, kv.Value);
            }

            return path;
        }

        internal static string requestError(UnityWebRequest request)
        {
            string responseBody = string.Empty;
            if (request.downloadHandler != null)
            {
                responseBody = request.downloadHandler.text;
            }

            return string.Format(
                "[api#error] request status code: {0}, data: ======= response: {1}, error: {2} =======",
                request.responseCode, responseBody, request.error);
        }

        internal static T requestResponse<T>(UnityWebRequest request)
        {
            try
            {
                var responseData = request.downloadHandler.text;
                return JsonConvert.DeserializeObject<T>(responseData);
            }
            catch (Exception ex)
            {
                Debug.Log(ex.Message);
                return default(T);
            }
        }

        public const string CONTENT_TYPE_JSON = "application/json";

        /// <summary>
        /// Create the instance of authenticated http client.
        /// </summary>
        /// <returns>The client.</returns>
        internal static IEnumerator Request(string url, string method, string data = null, Action<UnityWebRequest> done = null)
        {
            UnityWebRequest request;

            switch(method)
            {
                case UnityWebRequest.kHttpVerbGET:
                    request = UnityWebRequest.Get(url);
                    yield return request.SendWebRequest();
                    done?.Invoke(request);
                    break;
                case UnityWebRequest.kHttpVerbPOST:
                    request = UnityWebRequest.Post(url, data);
                    request.method = UnityWebRequest.kHttpVerbPOST;
                    request.downloadHandler = new DownloadHandlerBuffer();
                    request.uploadHandler = new UploadHandlerRaw(Encoding.UTF8.GetBytes(data));
                    request.SetRequestHeader("Content-Type", CONTENT_TYPE_JSON);
                    request.SetRequestHeader("Accept", CONTENT_TYPE_JSON);
                    yield return request.SendWebRequest();
                    done?.Invoke(request);
                    break;
            }
        }

        internal static IEnumerator Post(string url, object o, Action<UnityWebRequest> done = null) =>
            Request(url, UnityWebRequest.kHttpVerbPOST, JsonConvert.SerializeObject(o), done);

        internal static IEnumerator Get(string url, Action<UnityWebRequest> done = null) =>
            Request(url, UnityWebRequest.kHttpVerbGET, null, done);

        internal static Action<T1, T2> wrapCallback<T1, T2>(Action<T1, T2> doneCallback)
        {
            // in case of having missing done callback use empty function to skip checks
            // on null or not callback instance.
            return doneCallback ?? ((_arg1, _arg2) => { });
        }
    }
#endregion } 
```

Enter fullscreen mode Exit fullscreen mode

```
// Example of usage Api.cs

using Newtonsoft.Json;
using UnityEngine;
using System.Collections.Generic;
using System;
using System.Collections;

namespace Jumper.Network
{
    public class Users : API
    {
        public static IEnumerator Create(
            Dictionary<string, string> properties, User user,
            Action<User, string> doneCallback = null)
        {
            var done = wrapCallback(doneCallback);

            try
            {
                return Post(path(REGISTER_PATH, properties), user,
                    (request) =>
                    {
                        if (request.isNetworkError || request.responseCode != 201)
                            done(null, requestError(request));
                        else
                            done(requestResponse<User>(request), null);
                    });
            }
            catch (Exception ex)
            {
                // catch here all the exceptions ensure never die
                Debug.Log(ex.Message);
                done(null, ex.Message);
            }

            return null;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
// Example from MonoBehaviour

StartCoroutine(Users.Create(
                Api.GameProperties, Api.CurrentUser, (user, err) =>
                {
                    if (err != null)
                    {
                        Debug.LogError(err);
                    }
                    else
                    {
                        // good to go
                    }
                })); 
```

Enter fullscreen mode Exit fullscreen mode
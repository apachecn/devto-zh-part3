# SAStrutsとJSON

> 原文：<https://dev.to/shiena/sastruts-json-542a>

事到如今，我试着用 SAStruts 处理 JSON。

## JSON 请求

SAStruts 在`S2RequestProcessor.java`中将 HTTP 请求复制到动作表单中，因此 JSON 请求的处理将在此之前进行。
JSON 数据的读取使用了[Jackson](https://github.com/FasterXML/jackson) 。

```
package sample.sastruts.json.action;

import java.io.IOException;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.apache.struts.action.ActionForm;
import org.apache.struts.action.ActionMapping;
import org.seasar.framework.beans.util.Beans;
import org.seasar.framework.util.ReaderUtil;
import org.seasar.struts.action.S2RequestProcessor;
import org.seasar.struts.util.S2ActionMappingUtil;

import com.fasterxml.jackson.databind.DeserializationFeature;
import com.fasterxml.jackson.databind.ObjectMapper;

public class S2JSONRequestProcessor extends S2RequestProcessor {

    @Override
    protected void processPopulate(HttpServletRequest request,
            HttpServletResponse response, ActionForm form, ActionMapping mapping)
            throws ServletException {

        if (form == null) {
            return;
        }

        String contentType = request.getContentType();
        String method = request.getMethod();
        if (contentType != null && contentType.startsWith("application/json")
                && "POST".equalsIgnoreCase(method)) {

            form.setServlet(servlet);
            form.setMultipartRequestHandler(null);
            processExecuteConfig(request, response, mapping);
            form.reset(mapping, request);

            Object actionForm = S2ActionMappingUtil.getActionMapping()
                    .getActionForm();
            try {
                ObjectMapper  mapper = new ObjectMapper();
                mapper.disable(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES);

                String postBody = ReaderUtil.readText(request.getReader());
                Object value = mapper.readValue(postBody, actionForm.getClass());

                Beans.copy(value, actionForm).execute();

            } catch (IOException e) {
                log.error(e.getMessage(), e);
                throw new ServletException(e.getMessage(), e);
            }

        } else {

            super.processPopulate(request, response, form, mapping);

        }
    }

} 
```

最让人着急的是以下部分。
因为将 JSON 请求作为`actionForm.getClass()`型读取到`value`中，所以可以将值复制到`actionForm`中。

```
ObjectMapper  mapper = new ObjectMapper();
mapper.disable(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES);

String postBody = ReaderUtil.readText(request.getReader());
Object value = mapper.readValue(postBody, actionForm.getClass());

Beans.copy(value, actionForm).execute(); 
```

将该类设定为`struts-config.xml`的`processorClass`即可使用。

```
<controller
    maxFileSize="1024K"
    bufferSize="1024"
    processorClass="sample.sastruts.json.action.S2JSONRequestProcessor"
    multipartClass="org.seasar.struts.upload.S2MultipartRequestHandler"/> 
```

## JSON 响应

SAStruts 的动作是将转移目标的路径写在返回值中，与 JSP 等合作，但如果返还`null`，则不转移。 使用它将 JSON 从操作直接导出到 HTTP 响应。
从 Java 对象到 JSON 的变换也使用了[Jackson](https://github.com/FasterXML/jackson) 。

```
import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.seasar.struts.annotation.Execute;
import org.seasar.struts.util.ResponseUtil;

public class XXXAction {
    @Execute(validator = false)
    public String xxx() throws JsonProcessingException {
        String[] result = {"foo", "bar"};
        ObjectMapper mapper = new ObjectMapper();
        ResponseUtil.write(mapper.writeValueAsString(result), "application/json", "UTF-8");

        return null;
    }
} 
```

## Memories

我们解说了智能的 JSON 响应的安装方法，也请看一下。
[在 SAStruts 中返回 JSON 格式的数据(包含验证错误)](https://web.archive.org/web/20160808193714/http://tech-sketch.jp/2014/01/sastrutsjson.html)

## JSON 样本 APP

[在 SAStruts 的加法教程](http://sastruts.seasar.org/tutorial.html#add)中，为了利用 JSON 制作了修改后的样本 APP。
我们还使用 Bean Validation 作为验证器。

[https://bitbucket.org/shiena/sample-sastruts-json-webapps](https://bitbucket.org/shiena/sample-sastruts-json-webapps)
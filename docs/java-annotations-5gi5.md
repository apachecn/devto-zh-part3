# Java 注释。

> 原文：<https://dev.to/saifali40/java-annotations-5gi5>

在 Java 中使用 Spring Framework 的人都知道注释有多酷，甚至当我第一次使用注释时都感到惊讶。我不是说只有 spring 有注释，Java 本身也有很多注释，而且效果很好，减少了很多代码。

当我开始决定至少写一个注释时，我以为注释可以做很多事情而不需要使用任何额外的方法，事实上，我错了。

如果你想写一个注释，你需要一个注释和至少一个便于注释的方法，

下面的例子将展示在没有 Setters 或 Constructors 的情况下，如何轻松地创建一个注释来将模型中的字段映射到 dto。

[https://github.com/saifali40/mapper](https://github.com/saifali40/mapper)

```
import java.lang.annotation.*;

@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
public @interface FieldMapper {
    public String field();
} 
```

这是我为我的演示项目编写的注释，它将在运行时为字段运行，您也可以尝试其他目标和保留策略，对于我的要求，我需要将保留作为运行时，将目标作为字段。

```
import com.google.gson.Gson;
import com.google.gson.reflect.TypeToken;
import in.saifali.mapper.annotations.FieldMapper;

import java.lang.reflect.Field;
import java.util.HashMap;
import java.util.Map;

public class Processor {
    Gson gson = new Gson();
    Map<Object, Object> dtoMap = new HashMap<>();
    Map<Object, Object> objectMap = new HashMap<>();
    public <T> T map(Class<T> t, Object object) {
        try {
            T returnClass = t.getDeclaredConstructor().newInstance();
            for (Field field : returnClass.getClass().getDeclaredFields()) {
                field.setAccessible(true);
                if (field.isAnnotationPresent(FieldMapper.class)) {
                    dtoMap.put(field.getName(), field.getAnnotation(FieldMapper.class).field());
                }else{
                    dtoMap.put(field.getName(), field.getName());
                }
            }
            objectMap = gson.fromJson(gson.toJson(object), new TypeToken<HashMap<Object, Object>>() {
            }.getType());
            dtoMap.forEach((x, y) -> dtoMap.put(x, objectMap.get(y)));
            String json = gson.toJson(dtoMap);
            return gson.fromJson(json, t);
        } catch (Exception e) {
            throw new IllegalArgumentException();
        }
    }

} 
```

因为保留是运行时的，所以我可以使用 Java 反射 API，用注释`@FieldMapper`获取每个字段，并映射到相应的字段。

从上面的 GitHub 存储库中，您可以找到测试用例，并理解如何轻松地使用上面的注释。

注:我不擅长写作。
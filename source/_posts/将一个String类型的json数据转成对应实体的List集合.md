---
title: 将一个String类型的json数据转成对应实体的List集合
date: 2017-09-14 17:15:05
tags:
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

这个借助了`import com.fasterxml.jackson.databind.JavaType;`

使用下面这个方法做转换，可以说是一个工具吧，注意一下，constructParametricType()这个方法其实已经作废了

```java
public static JavaType getCollectionType(Class<?> collectionClass, Class<?>... elementClasses) {
        ObjectMapper mapper = new ObjectMapper();
        return mapper.getTypeFactory().constructParametricType(collectionClass, elementClasses);
}
```

下面来转一下，这里的PsOrg是一个POJO

```java
                String returnObj = jso.get("returnObj").toString();
                JavaType javaType = getCollectionType(ArrayList.class, PsOrg.class);
                List<PsOrg> psOrgList = (List<PsOrg>) objectMapper.readValue(returnObj, javaType);
```

还有一些其他方法，比如google的Gson，方法里的str就是一个json类型的字符串

```java
Gson gson = new Gson();
List<String> mfgLines = gson.fromJson(str, new TypeToken<List<String>>() {}.getType());
```

还有个更方便的方法，使用阿里的fastjson，只需要一行代码

```java
List<Model> list = JSON.parseArray("你的json字符串", Model.class); （Model是你的实体）
```

如果两边的字段数量不一致，可以在你的实体类上添加下面注解`@JsonIgnoreProperties(ignoreUnknown = true)`

附录：我们看一下那个作废的方法以及新的替换方法

```java
    /**
     * @deprecated Since 2.5, use {@link #constructParametrizedType} instead.
     */
    @Deprecated
    public JavaType constructParametricType(Class<?> parametrized, Class<?>... parameterClasses) {
        return constructParametrizedType(parametrized, parametrized, parameterClasses);
    }
```
```java
/**
     * Factory method for constructing {@link JavaType} that
     * represents a parameterized type. For example, to represent
     * type <code>List<Integer></code>, you could
     * call
     *<pre>
     *  TypeFactory.constructParametrizedType(List.class, List.class, Integer.class);
     *</pre>
     *<p>
     * The reason for first two arguments to be separate is that parameterization may
     * apply to a super-type. For example, if generic type was instead to be
     * constructed for <code>ArrayList<Integer></code>, the usual call would be:
     *<pre>
     *  TypeFactory.constructParametrizedType(ArrayList.class, List.class, Integer.class);
     *</pre>
     * since parameterization is applied to {@link java.util.List}.
     * In most cases distinction does not matter, but there are types where it does;
     * one such example is parameterization of types that implement {@link java.util.Iterator}.
     *<p>
     * NOTE: type modifiers are NOT called on constructed type itself; but are called
     * when resolving <code>parameterClasses</code> into {@link JavaType}.
     *
     * @param parametrized Type-erased type of instance being constructed
     * @param parametersFor class or interface for which type parameters are applied; either
     *   <code>parametrized</code> or one of its supertypes
     * @param parameterClasses Type parameters to apply
     * 
     * @since 2.5
     */
    public JavaType constructParametrizedType(Class<?> parametrized, Class<?> parametersFor,
            Class<?>... parameterClasses)
    {
        int len = parameterClasses.length;
        JavaType[] pt = new JavaType[len];
        for (int i = 0; i < len; ++i) {
            pt[i] = _fromClass(parameterClasses[i], null);
        }
        return constructParametrizedType(parametrized, parametersFor, pt);
    }
```
可以仔细看一下注释，说的很清晰，我还没尝试这个

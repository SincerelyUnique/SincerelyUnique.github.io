---
title: 为什么get请求会出现乱码？
date: 2017-09-30 11:12:43
tags:
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

1. 有时候我们在处理get请求时，比如下面的一个url跳转

> window.location.href = "某个url"

当你的url里存在某个参数为中文字符，那么你就可能遇到乱码问题，为什么说可能呢，因为tomcat默认编码其实是ISO-8859-1，如果你在tomcat服务器配置了utf-8编码的话（config目录下的server.xml文件），就不会出乱码了，配置如下（注意在你的使用的端口里配置，一般都是在8080端口下配置一下URIEncoding）

```xml
    <Connector port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" URIEncoding="UTF-8"/>
```

2. 现在我们不在服务器里做上面配置，那么就需要使用到js的编码技术以及java.net包提供的解码技术，首先比如说我们传的url里存在一个参数departName=“教育BG研发中心”，那么我们调用js的方法进行编码encodeURI(encodeURI(departName))，如下：
```javascript
    var departName = encodeURI(encodeURI(departName));
    window.location.href = "http://a.b.c?departName="+departName;
```

为什么做两次编码呢？

原因如下：
1. 利用encodeURI()在javascript中对中文URL参数进行编码时，你的中文参数会被转换为类似于“%E6%B5%8B%E8%AF%95”这种字符串
2. 但是浏览器机制会认为“%”是一个转义字符，浏览器会把地址栏URL中的传递的已转换参数“%”与“%”之间的已转义字符进行处理传递到后台Action（接口）中。这样会造成与实际经过encodeURI()编码后的URL不符，因为浏览器误认为“%”是转义字符字符了，它并未将“%”认为是个普通字符。
3. 要使得通过encodeURI()转换后的URL被浏览器正常处理，必须在外层再用encodeURI()处理一次已被encodeURI()编码后的RUL。这此处理encodeURI()会将已编码后的URL中被浏览器解析为转义字符的“%”再次进行编码，转换为普通字符。

这个时候，在后台接口里使用java.net做一下转换就可以了：

```java
String depName = java.net.URLDecoder.decode(request.getParameter("departName"), "UTF-8");
```

上面的转换方式是：js前端转2次，后端转1次；

网上还有一种方式是：js转一次，后端转2次，这个没有尝试，贴出来如下：

```javascript
url=encodeURI(url)
```

```java
String app_name = java.net.URLDecoder.decode(request.getParameter("name"), "utf-8");
app_name = new String(app_name.getBytes("ISO-8859-1"),"utf-8");
```

3. 至于有些人疑惑post传递的参数里也有中文，为什么从前台传递过来不乱码呢？

原因是post是以数据包的形式将封装好的参数传递给后台，中间不会做编码转换相关的处理，所以后台直接获取也不会出现乱码。

当然有时post是会乱码的（比如使用HttpClient包下的工具，远程调用其它服务器的接口，偶尔会遇到这种乱码的情况），此时可以在接收时设置一下编码，如下几种方式：

1.java代码里设置
```java
request.setCharacterEncoding("UTF-8");
```
2. 从web配置文件设置
```xml
　　　　　<filter>
　　　　　　　　<description>字符集过滤器</description>
　　　　　　　　<filter-name>encodingFilter</filter-name>
　　　　　　　　<filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
　　　　　　　　<init-param>
　　　　　　　　　　<description>字符集编码</description>
　　　　　　　　　　<param-name>encoding</param-name>
　　　　　　　　　　<param-value>UTF-8</param-value>
　　　　　　　　</init-param>
　　　　　</filter>
　　　　　<filter-mapping>
　　　　　　<filter-name>encodingFilter</filter-name>
　　　　　　<url-pattern>/*</url-pattern>
　　　　　</filter-mapping>
```
3. 使用HttpURLConnection远程访问：
```java
HttpURLConnection connection = null;
connection.setRequestProperty("contentType", "utf-8");
```
4. 使用InputStreamReader读取流时
```java
InputStreamReader in = null;
in = new InputStreamReader(connection.getInputStream(),"utf-8");
BufferedReader bufferedReader = new BufferedReader(in);
StringBuffer stringBuffer = new StringBuffer();
String line = null;
while ((line = bufferedReader.readLine()) != null) {
    stringBuffer.append(line);
}
result = stringBuffer.toString();
```
5. 使用HttpClient的HttpGet时
```java
CloseableHttpClient httpclient = httpClientBuilder.build();
HttpGet httpget = new HttpGet("url");
CloseableHttpResponse response = httpclient.execute(httpget);
String content = EntityUtils.toString(response.getEntity(), "UTF-8");
```
6. 使用HttpClient的HttpPost时
```java
CloseableHttpClient httpclient = httpClientBuilder.build();
HttpPost httpPost = new HttpPost(url);
List<BasicNameValuePair> params = new ArrayList<>();
params.add(new BasicNameValuePair("userAccount",userAccount));
params.add(new BasicNameValuePair("token",token));
UrlEncodedFormEntity httpEntity= new UrlEncodedFormEntity(valuePairs, "UTF-8");
httpMethod.setEntity(httpEntity);
CloseableHttpResponse response = httpclient.execute(httpMethod);
String content = EntityUtils.toString(response.getEntity(), "UTF-8");
```

具体前端js编码的使用说明可以参考w3c的文档：http://www.w3school.com.cn/jsref/jsref_encodeuri.asp

语法：encodeURI(URIstring)

参数URIstring描述：必需。一个字符串，含有 URI 或其他要编码的文本。

返回值：URIstring 的副本，其中的某些字符将被十六进制的转义序列进行替换。

说明：
1. 该方法不会对 ASCII 字母和数字进行编码，也不会对这些 ASCII 标点符号进行编码： - _ . ! ~ * ' ( ) 。
2. 该方法的目的是对 URI 进行完整的编码，因此对以下在 URI 中具有特殊含义的 ASCII 标点符号，encodeURI() 函数是不会进行转义的：;/?:@&=+$,#

提示和注释：如果 URI 组件中含有分隔符，比如 ? 和 #，则应当使用 encodeURIComponent() 方法分别对各组件进行编码。

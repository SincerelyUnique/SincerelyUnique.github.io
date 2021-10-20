---
title: HttpURLConnection中文乱码解决
date: 2017-08-04 16:55:34
tags:
- java
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

> 说明：使用HttpURLConnection创建连接，测试时是从main方法调用，没有中文乱码情况，但是当启动服务器后，从远程服务器获取到的数据却乱码了，于是便改成下面的，仅供can'kao

1. 出现乱码的代码：

```java
/**
     * 从xx系统拉取用户信息
     * @param userAccount
     * @return
     */
    public static String getUserInfo(String userAccount,String pathType) {
        String[] serverInfo = getServerInfoForTokenValid(getUserInfo);
        String serverPath = composeServerPath(serverInfo,userAccount,pathType);
 
        String result = "";
        HttpURLConnection connection = null;
 
        try {
            URL url = new URL(serverPath);
            connection = (HttpURLConnection) url.openConnection();
            connection.setDoOutput(true);
            connection.setRequestMethod("GET");
            connection.setRequestProperty("Content-Type", "application/json");
            Scanner scanner;
            if (connection.getResponseCode() != 200) {
                scanner = new Scanner(connection.getErrorStream());
            } else {
                scanner = new Scanner(connection.getInputStream());
            }
            scanner.useDelimiter("\\Z");
            while (scanner.hasNext()){
                result = scanner.next();
            }
            scanner.close();
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            if (connection != null)
                connection.disconnect();
        }
 
        return result;
    }
```

2. 修改后的代码：

```java
/**
     * 从xx系统拉取用户信息
     * @param userAccount
     * @return
     */
    public static String getUserInfo(String userAccount,String pathType) {
        String[] serverInfo = getServerInfo(getUserInfo);
        String serverPath = composeServerPath(serverInfo,userAccount,pathType);
 
        String result = "";
        HttpURLConnection connection = null;
 
        try {
            URL url = new URL(serverPath);
            connection = (HttpURLConnection) url.openConnection();
            connection.setDoOutput(true);
            connection.setRequestMethod(GpsConstants.HTTP_REQUEST_METHOD_GET);
            connection.setRequestProperty("Content-Type", "application/json");
            connection.setRequestProperty("contentType", GpsConstants.ENCODING_UTF8);
            connection.setRequestProperty("Accept-Charset", GpsConstants.ENCODING_UTF8);
            if (connection.getResponseCode() != 200) {
                log.error("连接失败！");
            } else {
                InputStreamReader in = null;
                in = new InputStreamReader(connection.getInputStream(),GpsConstants.ENCODING_UTF8);
                BufferedReader bufferedReader = new BufferedReader(in);
 
                StringBuffer stringBuffer = new StringBuffer();
                String line = null;
                while ((line = bufferedReader.readLine()) != null) {
                    stringBuffer.append(line);
                }
                result = stringBuffer.toString();
            }
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            if (connection != null)
                connection.disconnect();
        }
 
        return result;
    }
```



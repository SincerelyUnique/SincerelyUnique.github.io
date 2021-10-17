---
title: java序列化之transient
date: 2017-11-09 16:23:04
tags:
- java
- transient
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

java序列化是做什么的？

简而言之，就是把你的数据换个时间或换个地点，继续使用，换个时间就是指存盘（存到文件中，等一段时间再去读取之前写入的数据），换个地点就是指网络之间的数据传输。

如果在存盘或传输时想忽略某些数据，就可以使用transient修饰符去修饰这个数据。如下：

```java
import lombok.AllArgsConstructor;
import lombok.ToString;
 
import java.io.Serializable;
 
/**
 * <p>
 * <code>UserInfo</code>
 * </p>
 * Description:实体
 *
 * @author Mcchu
 * @date 2017/11/9 13:46
 */
@AllArgsConstructor
@ToString
public class UserInfo implements Serializable{
 
    private static final long serialVersionUID = 996890129747019948L;
 
    private String name;
 
    private transient String password;
 
    private static String position;
}
```

```java
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;
 
/**
 * <p>
 * <code>TransientTest</code>
 * </p>
 * Description:
 *
 * @author Mcchu
 * @date 2017/11/9 13:45
 */
public class TransientTest {
 
    public static void main(String[] args) {
        UserInfo userInfo = new UserInfo("jalen", "123456");
        System.out.println(userInfo);
 
        try {
            // 序列化，被设置为transient的属性没有被序列化
            ObjectOutputStream o = new ObjectOutputStream(new FileOutputStream(
                    "user"));
            o.writeObject(userInfo);
            o.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
 
        try {
            // 重新读取内容
            ObjectInputStream in = new ObjectInputStream(new FileInputStream(
                    "user"));
            UserInfo readUserInfo = (UserInfo) in.readObject();
            //读取后psw的内容为null
            System.out.println(readUserInfo.toString());
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

当使用transient修饰password字段时，在内存中这个字段并未做序列化，故重新读取内容后输出是为空，下面是输出结果：

```
UserInfo(name=jalen, password=123456)
UserInfo(name=jalen, password=null)
```

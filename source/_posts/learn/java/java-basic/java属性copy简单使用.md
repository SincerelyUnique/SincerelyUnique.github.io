---
title: java属性copy简单使用
date: 2017-10-13 13:46:59
tags:
- java
categories:
- [学习, Java语言学习, Java]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

开发中，操作属性时，经常遇到下面这种情况：

```java
public static void main(String[] args) {
        // tony是新转学的学生，mike是老生
        Student mike = new Student("Mike",12,"四年二班","Kite","1.4","40","合肥市蜀山区");
        Student tony = new Student("Tony",14,null,null,"1.5","50","合肥市包河区");
 
        // 将tony转到了mike所在的班级
        tony.setClassName(mike.getClassName());
        tony.setTeacherName(mike.getTeacherName());
 
        // 结果：Student(studentName=Tony, studentAge=14, className=四年二班, teacherName=Kite, height=1.5, weight=50, homeAddress=合肥市包河区)
        System.out.println(tony.toString());
    }
```

上面简单设置两个属性看上去还好，但是如果几十条甚至上百条属性要变，一个一个set看上去会很糟糕

下面我们对比一下几个第三方工具中对于属性copy的使用，可以借鉴一下：

```java
import com.example.demo.java.other.domain.Student;
import com.example.demo.java.other.domain.Teacher;
import org.apache.commons.beanutils.BeanUtils;
import org.modelmapper.Conditions;
import org.modelmapper.ModelMapper;
import org.springframework.context.annotation.Bean;
//import org.springframework.beans.BeanUtils;
 
/**
 * <p>
 * <code>PropertiesCopyTest</code>
 * </p>
 * Description:java对象拷贝
 * 注意事项：
 * 使用spring的BeanUtils不用抛异常，而使用Apache的BeanUtils必须抛异常（反射），否则编译报错
 * 使用BeanUtils的copyProperties时，如果实体B想要copy实体A，而实体A中某些和B相同的字段是null时，
 *    也会被copy过去，导致实体B本身存在的数据丢失，此时可以考虑使用modelMapper工具
 *
 * @author Mcchu
 * @date 2017/10/13 12:07
 */
public class PropertiesCopyTest {
 
    public static void main(String[] args) {
        // Tony是新转来的学生，还没有分配班级和老师，下面我们要将他分到和mike一个班
        Student mike = mike();
        Student tony = tony();
 
        // 1. 使用spring自带的工具，tony的属性被全部覆盖了，这不是我们想要的
        //BeanUtils.copyProperties(mike,tony);
        // 结果失败：Student(name=Mike, age=12, className=四年二班, teacherName=Kite, height=1.4, weight=40, homeAddress=合肥市蜀山区)
        //System.out.println(tony.toString());
 
        try {
            // 2. 使用apache工具，将属性一次性全部地copy到tony上,可以发现tony的属性被全部覆盖了，这也不是我们想要的
            //BeanUtils.copyProperties(tony,mike);
            // 结果失败：Student(name=Mike, age=12, className=四年二班, teacherName=Kite, height=1.4, weight=40, homeAddress=合肥市蜀山区)
            //System.out.println(tony.toString());
 
            // 3. 使用apache工具，退而求其次，我们将属性一个一个地copy到tony的属性上，这样是可以的
            //BeanUtils.copyProperty( tony, "className", mike.getClassName() );
            //BeanUtils.copyProperty( tony, "teacherName", mike.getTeacherName() );
            // 结果正确：Student(name=Tony, age=14, className=四年二班, teacherName=Kite, height=1.5, weight=50, homeAddress=合肥市包河区)
            //System.out.println(tony.toString());
        }catch (Exception e){
            e.printStackTrace();
        }
 
 
        // 4. 我们反过来想，如果将tony的属性先覆盖到mike的属性上，这里的覆盖有个关键性条件，即只覆盖存在数据的字段，如果为null时，不做覆盖操作，此时得到的mike属性不就是tony需要的真实属性吗？
        //ModelMapper modelMapper = new ModelMapper();
        //modelMapper.getConfiguration().setPropertyCondition(Conditions.isNotNull());
        //modelMapper.map(tony,mike);
        //tony = mike;
        // 结果正确：Student(name=Tony, age=14, className=四年二班, teacherName=Kite, height=1.5, weight=50, homeAddress=合肥市包河区)
        //System.out.println(tony.toString());
 
        // 5. 假如我们可以拿得到kite老师的属性，而老师的属性里又存在tony需要的className和teacherName，那么我们直接copyProperties就好了
        try {
            Teacher kite = kite();
            BeanUtils.copyProperties(tony,kite); //此处等同于org.springframework.beans.BeanUtils.copyProperties(kite,tony);
            // 结果正确：Student(studentName=Tony, studentAge=14, className=四年二班, teacherName=Kite, height=1.5, weight=50, homeAddress=合肥市包河区)
            System.out.println(tony.toString());
        }catch (Exception e){
            e.printStackTrace();
        }
    }
 
    private static Student mike(){
        return new Student("Mike",12,"四年二班","Kite","1.4","40","合肥市蜀山区");
    }
 
    private static Student tony(){
        return new Student("Tony",14,null,null,"1.5","50","合肥市包河区");
    }
 
    private static Teacher kite(){
        return new Teacher("Kite",36,"四年二班");
    }
}
```

上面5种方式中，后3种是可以实现正确结果的

附录：Student实体类和Teacher实体类

```java
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
 
/**
 * <p>
 * <code>Teacher</code>
 * </p>
 * Description:
 *
 * @author Mcchu
 * @date 2017/10/13 13:34
 */
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Teacher {
 
    private String teacherName;
 
    private Integer teacherAge;
 
    private String className;
}
```

```java
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
 
/**
 * <p>
 * <code>Student</code>
 * </p>
 * Description:
 *
 * @author Mcchu
 * @date 2017/10/13 12:07
 */
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Student {
 
    private String studentName;
 
    private Integer studentAge;
 
    private String className;
 
    private String teacherName;
 
    private String height;
 
    private String weight;
 
    private String homeAddress;
}
```

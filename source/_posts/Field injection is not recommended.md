---
title: Field injection is not recommended
date: 2017-10-11 11:33:31
tags:
- java
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

1. 使用@Autowire注入时警告：Field injection is not recommended

2. 被警告代码：
```java
    @Autowired
    private EduWorkInfoMapper eduWorkInfoMapper;
 
    @Autowired
    private IEduExportUserInfoService eduExportUserInfoService;
```

3. 原因：不建议使用属性注入，其实当你鼠标滑动到@Autowired上面，同时按下alt+enter键时，就会看到下面这样的提示xxx to constructor,这说明编辑器建议你使用构造注入的方式进行bean的注入。

4. 说明:

参考地址

http://vojtechruzicka.com/field-dependency-injection-considered-harmful/

https://www.petrikainulainen.net/software-development/design/why-i-changed-my-mind-about-field-injection/

https://stackoverflow.com/questions/39890849/what-exactly-is-field-injection-and-how-to-avoid-it

http://www.bubuko.com/infodetail-2048216.html （从StackOverflow翻译过来的）

5. 解决方法：参考地址：http://blog.csdn.net/jeikerxiao/article/details/77161680
```java
    private IWorkLoadService workLoadService;
    private IEduExportUserInfoService eduExportUserInfoService;
    private IWorkLoadJobsService workLoadJobsService;
 
    @Autowired
    public WorkLoadController( IWorkLoadService workLoadService
            ,IEduExportUserInfoService eduExportUserInfoService
            ,IWorkLoadJobsService workLoadJobsService ){
        this.workLoadService = workLoadService;
        this.eduExportUserInfoService = eduExportUserInfoService;
        this.workLoadJobsService = workLoadJobsService;
    }
```

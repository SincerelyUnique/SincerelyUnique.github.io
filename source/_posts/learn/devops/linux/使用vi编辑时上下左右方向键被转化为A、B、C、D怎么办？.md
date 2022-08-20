---
title: 使用vi编辑时上下左右方向键被转化为A、B、C、D怎么办？
date: 2017-10-14 21:54:14
tags:
- linux
- vim
categories:
- [学习, DevOPS, Linux]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 出现情况（原因）
新装的ubuntu16.04遇到的，尚未动过vi

# 如下如果按右键，会一直出C
```
C
C
C
C
C
C
C
C
C
C
C
C
C
C
Exec=/usr/bin/google-chrome-stable %U –ppapi-flash-path=/home/jalenchu/.config/google-chrome/PepperFlash/libpepflashplayer.so
Terminal=false
Icon=google-chrome
```

# 解决办法
参考网上帖子（ http://blog.csdn.net/stpeace/article/details/49407857），直接打开新的终端，输入以下命令回车即可

```
jalen@jalen-System-Product-Name:~$ echo "set nocp" >> ~/.vimrc
jalen@jalen-System-Product-Name:~$ source ~/.vimrc
```

问题：上面的命令虽然生效了，但是之前输入的C却不知道怎么删除掉？

上面问题参考了博客：http://www.cnblogs.com/jiangjh/archive/2012/09/09/2677696.html

博客上指明原因使：这个问题主要是新系统直装了vi，没有装vim

由于我的也是新装的系统，所以直接安装一下vim就可以了

```
sudo apt-get install vim
```

修改文件时如果提示是readonly文件，是没有权限修改的，记得要加sudo

附录：想要更多了解上面命令含义，可以参考以下链接

http://www.jianshu.com/p/793f7ce26281

http://edyfox.codecarver.org/html/_vimrc_for_beginners.html

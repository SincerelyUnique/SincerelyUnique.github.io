---
title: Linux常用命令
date: 2021-10-27 04:11:00
tags: linux
categories:
- [学习, DevOPS, Linux]
---

# Linux 常用命令大全

## Linux 管理文件和目录的命令

1. `pwd` 该命令的解释为打印工作目录，输入pwd命令，Linux会输出当前目录
2. `cd` cd命令用来改变所在目录
- `cd /` 转到根目录中
- `cd ~` 转到/home/user用户目录下
- `cd /usr` 转到根目录下的usr目录中 - 绝对路径
- `cd test` 转到当前目录下的test子目录中 - 相对路径
3. `ls` ls命令用来查看目录的内容
- `-a` 列举目录中的全部文件
- `-l` 列举目录中细节
- `-f` 列举的文件显示文件类型
- `-s` 大小，按文件大小排序
4. `cat` cat命令用来合并文件和在屏幕上显示整个文件的内容
- `cat snow.txt` 该命令显示文件snow.txt的内容，ctrl+D推出cat
5. `grep` grep命令是在一堆文件中查找一个特定的字符串，grep查找是区分大小写的
- `grep money test.txt` 以上命令在test.txt中查找money这个字符串
6. `touch` touch命令用来创建新文件
- `touch newfile` 该命令创建一个名为newfile的空白文件
7. `cp` cp命令用来拷贝文件
8. `mv` mv命令用来移动文件（或重命名）
- `mv t.txt Document` 把文件t.txt移动到目录Document中
9. `rm` rm命令用来删除文件
- `rm t.txt` 该命令删除文件t.txt

## 有关磁盘空间的命令

1. `mount` 功能是挂载文件系统
- `-a` 挂上/etc/fstab下的全部文件系统
- `-n` 挂上文件系统
- `-t` 制定所挂上来的文件系统的名称
- `-w` 将文件系统设为可读写
- `-r` 挂上来的文件系统设为只读
2. `unmount` 功能是卸载已挂上的文件系统
3. `df` 检查硬盘分区和已挂载的文件系统的磁盘空间
4. `du` 功能是用于显示文件目录或大小
5. `fsck` 功能是检查和修复Linux文件系统

## 压缩/解压

1. `tar -cvf jpg.tar*.jpg` 将目录里所有jpg文件打包成jpg.tar
2. `tar -czf jpg.tar.gz*.jpg` 打包成jpg.tar后用gzip压缩生成名为jpg.tar.gz
3. `tar -cjf jpg.tar.bz2*.jpg` 打包成jpg.tar后用bzip2压缩生成名为jpg.tar.bz2
4. `tar -cZf jpg.tar.Z*.jpg` 打包成jpg.tar后用compress压缩生成名为jpg.tar.gZ
5. `rar a jpg.rar*.jpg` rar格式的压缩，需要先下载rar for linux
6. `zip jpg.zip*.jpg` zip格式的压缩，需要先下载zip for linux
7. *.tar 用`tar xvf` 解压
8. *.gz 用`gzip-d` 或`gunzip`解压
9. `*.tar.gz 和*.tgz`用`tar xzf`解压
10. *.bz2用 `bzip2 -d`或者`bunzip2`解压
11. *.tar.bz2用`tar xjf`解压
12. *.Z用`uncompress`解压
13. *.tar.Z 用`tar xZf`解压
14. *.rar 用`unrar`解压
15. *.zip用`unzip`解压

## 进程

1. `ps` 用来查看在计算机系统中有哪些程序正在执行，及其执行的情况
- `-w` 显示加宽可以显示较多
- `-au` 显示较详细
- `-aux` 显示所有包含其他使用者的进程
2. STAT 该进程的状态
- D 不可中断的静止
- R 正在执行中
- S 静止状态
- T 暂停执行
- Z 不存在但暂时无法消除
- W 没有足够的记忆体分页可分配
- N 低优先序的进程
- L 有记忆体分页分配并锁在记忆体内
3. `ps -ef` 返回系统中所有用户的所有进程的完整列表
4. `ps -aux --sort -pmem | head -n 10` 前10个内存使用最多的进程， +pmem为内存使用最少
5. `kill -9 PID` 杀死进程
6. `killall -9 service` 杀死一类进程

## 管理使用者和设立权限的命令

1. `chmod` 用来改变许可权限
- `chmod a-rwx test.txt` 删除某一文件的所有权限
- `chmod u+rwx test.txt` 为文件所有者添加权限
- r 文件可以被读取
- w 文件可以被写入
- x 文件可以被执行
2. `su` 用来修改用户
- `su user02` 系统返回password: 此时输入user02的指令，就会变为user02
























---
title: Shell编程（兄弟连）
date: 2022-07-30 14:34:43
tags:
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# Shell编程

## 正则表达式（基础）
1. 正则表达式与通配符
   - 正则表达式用来在文件中匹配符合条件的字符串，正则是**包含匹配**。grep、awk、sed等命令可以支持正则表达式。
   - 通配符用来匹配符合条件的文件名，通配符是**完全匹配**。ls、find、cp这些命令不支持正则表达式，所以只能使用shell自己的通配符来进行匹配了。（通配符不多，有`* ? []`）
2. **基础**正则表达式（不包含扩展正则表达式）

    |元字符|作用|
    |---|---|
    |*|前一个字符匹配0次或任意多次|
    |.|匹配除了换行符外任意一个字符|
    |^|匹配行首。例如：^hello会匹配以hello开头的行|
    |$|匹配行尾。例如：hello$会匹配以hello结尾的行|
    |[]|匹配中括号中指定的任意一个字符，只匹配一个字符。例如：[aoeiu]匹配任意一个元音字母，[0-9]匹配任意一个数字，[a-z][0-9]匹配小写字母和一位数字构成的两位字符。|
    |[^]|匹配除中括号的字符以外的任意一个字符。例如：[^0-9]匹配任意一个非数字字符，[^a-z]匹配任意一位非小写字母。|
    |`\`|转义符。用于取消将特殊符号的含义取消。|
    |`\{n\}`|表示其前面的字符恰好出现n次。例如：[0-9]\{4\}匹配4位数字，[1][3-8][0-9]\{9\}匹配手机号码|
    |`\{n,\}`|表示其前面的字符出现不小于n次。例如：[0-9]\{2,\}表示两位及以上的数字|
    |`{n,m\}`|表示其前面的字符至少出现n次，最多出现m次。例如：[a-z]\{6,8\}匹配6到8位的小写字母|
    ```bash
    # "*" 前一个字符匹配0次，或任意多次(其实相当于匹配整篇文档，没啥意义)
    [root@core-pods-3 ~]# cat test_rule.txt 
    Jalen Chu
    Jaalen Chu
    Jaaalen Chu
    Jblen Chu
    [root@core-pods-3 ~]# grep "a*" test_rule.txt 
    Jalen Chu
    Jaalen Chu
    Jaaalen Chu
    Jblen Chu
    [root@core-pods-3 ~]# grep "aa*" test_rule.txt  # 至少出现1个a
    Jalen Chu
    Jaalen Chu
    Jaaalen Chu
    [root@core-pods-3 ~]# grep "aaa*" test_rule.txt   # 匹配至少出现2个a
    Jaalen Chu
    Jaaalen Chu
    [root@core-pods-3 ~]# grep "aaaa*" test_rule.txt  # 匹配至少出现3个a
    Jaaalen Chu
    [root@core-pods-3 ~]# grep "aaaaa*" test_rule.txt  # 匹配至少出现4个a，无匹配结果



    # “.”匹配除了换行符外任意一个字符
    [root@core-pods-3 ~]# cat test_rule.txt 
    Jalen Chu
    Jaalen Chu
    Jaaalen Chu
    Jblen Chu
    [root@core-pods-3 ~]# grep "J...n" test_rule.txt  # 匹配J和n之间有3个字符的单词
    Jalen Chu
    Jblen Chu
    [root@core-pods-3 ~]# grep "J.*n" test_rule.txt  # 匹配J和n之间有任意字符
    Jalen Chu
    Jaalen Chu
    Jaaalen Chu
    Jblen Chu
    [root@core-pods-3 ~]# grep ".*" test_rule.txt  # 匹配所有内容
    Jalen Chu
    Jaalen Chu
    Jaaalen Chu
    Jblen Chu
    [root@core-pods-3 ~]# grep "a.*n" test_rule.txt  # 匹配a和n之间有任意字符
    Jalen Chu
    Jaalen Chu
    Jaaalen Chu


    # “^”匹配行首，“$”匹配行尾
    [root@core-pods-3 ~]# cat test_rule.txt   # 注意5、6行是空白行
    Jalen Chu
    Moe Zhou
    Bella Wang
    Icon Qian


    [root@core-pods-3 ~]# 
    [root@core-pods-3 ~]# grep "^B" test_rule.txt   # 匹配以大写B开头的行
    Bella Wang
    [root@core-pods-3 ~]# grep "n$" test_rule.txt   # 匹配以小写n结尾的行
    Icon Qian
    [root@core-pods-3 ~]# grep -n "^$" test_rule.txt # 匹配空白行，空白行看不出来，加个-n
    5:
    6:
    [root@core-pods-3 ~]# 


    # “[]”匹配中括号中指定的任意一个字符，只匹配一个字符
    [root@core-pods-3 ~]# cat test_rule.txt 
    Jalen Chu
    Moe Zhou
    Bella Wang
    Icon Qian
    test123
    test456
    789
    [root@core-pods-3 ~]# grep "M[aoe]e" test_rule.txt  # 匹配M和e之间出现a，o，e其中1个字符的行
    Moe Zhou
    [root@core-pods-3 ~]# grep "e[lmn]la" test_rule.txt # 匹配e和l之间出现l，m，n其中一个字符的行
    Bella Wang
    [root@core-pods-3 ~]# grep "[0-9]" test_rule.txt  # 匹配出现任意数字的行
    test123
    test456
    789
    [root@core-pods-3 ~]# grep "^[a-z]" test_rule.txt # 匹配小写字母开头的行
    test123
    test456
    [root@core-pods-3 ~]# grep "^[0-9]" test_rule.txt  # 匹配数字开头的行
    789
    [root@core-pods-3 ~]# grep "^[^0-9]" test_rule.txt  # 匹配非数字开头的行
    Jalen Chu
    Moe Zhou
    Bella Wang
    Icon Qian
    test123
    test456
    [root@core-pods-3 ~]# grep "^[^a-z]" test_rule.txt  # 匹配非小写字母开头的行
    Jalen Chu
    Moe Zhou
    Bella Wang
    Icon Qian
    789
    [root@core-pods-3 ~]# grep "^[^a-zA-Z]" test_rule.txt  # 匹配非字母开头的行
    789
    

    # “\”转义符
    [root@core-pods-3 ~]# cat test_rule.txt 
    I am a policeman, I will catch the criminal.
    Do you know?
    My name is Jalen.
    [root@core-pods-3 ~]# grep ".$" test_rule.txt   # 这里没将.转义，匹配所有了
    I am a policeman, I will catch the criminal.
    Do you know?
    My name is Jalen.
    [root@core-pods-3 ~]# grep "\.$" test_rule.txt  # 匹配以.结尾的行
    I am a policeman, I will catch the criminal.
    My name is Jalen.
    [root@core-pods-3 ~]# 

    [root@core-pods-3 ~]# cat test_rule.txt 
    jalen
    jaalen
    jaaalen
    moe
    mooe
    moooe
    [root@core-pods-3 ~]# grep "a\{3\}" test_rule.txt  # 匹配包含3个a的行
    jaaalen
    [root@core-pods-3 ~]# grep "o\{2\}" test_rule.txt  # 匹配包含2个a的行
    mooe
    moooe

    [root@core-pods-3 ~]# cat test_rule.txt 
    my telephone is 1865666666.
    and how about you?
    let me see, it's 138enn, sorry, I don't know.
    [root@core-pods-3 ~]# grep "[0-9]\{3,\}[a-z]" test_rule.txt  # 至少3个数字
    let me see, it's 138enn, sorry, I don't know.
    [root@core-pods-3 ~]# grep "[0-9]\{2,\}[a-z]" test_rule.txt # 至少2个数字
    let me see, it's 138enn, sorry, I don't know.
    [root@core-pods-3 ~]# grep "[0-9]\{4,\}[a-z]" test_rule.txt  # 至少4个数字
    [root@core-pods-3 ~]# grep "6\{5\}" test_rule.txt  # 6至少出现5次
    my telephone is 1865666666.
    [root@core-pods-3 ~]# grep "6\{5,6\}" test_rule.txt  # 6出现至少5次，最多6次
    my telephone is 1865666666.
    [root@core-pods-3 ~]# grep "6\{7,9\}" test_rule.txt # 6出现至少7次，最多9次
    ```

## 字符截取命令
### cut字段提取命令
与grep提取符合条件的行相反，cut命令提取列（制表符）。cut一般不会独立使用，基本都死结合grep命令通过管道符连接使用。命令为`cut [选项] 文件名`。选项有：
- -f：列号，提取第几列
- -d：分隔符，按照指定分隔符分割列
```bash
# 测试命令
[root@core-pods-3 ~]# cat student.txt 
ID      Name    gender  Mark
1       Moe     F       90
2       Jalen   M       80
3       Jones   M       95
[root@core-pods-3 ~]# cut -f 2 student.txt 
Name
Moe
Jalen
Jones
[root@core-pods-3 ~]# cut -f 2,4 student.txt 
Name    Mark
Moe     90
Jalen   80
Jones   95
[root@core-pods-3 ~]# cut -d ":" -f 1,3 /etc/passwd
root:0
bin:1
daemon:2
adm:3
lp:4
sync:5
shutdown:6
halt:7
mail:8
operator:11
games:12
ftp:14
nobody:99
systemd-bus-proxy:999
systemd-network:192
dbus:81
polkitd:998
tss:59
sshd:74
postfix:89
chrony:997
nginx:996
linzhiling:1000
[root@core-pods-3 ~]# 
```

```bash
# 实际使用举例：提取passwd里普通用户并批量删除
[root@core-pods-3 ~]# useradd user1
[root@core-pods-3 ~]# useradd user2
[root@core-pods-3 ~]# useradd user3
[root@core-pods-3 ~]# cat /etc/passwd
... ...
user1:x:1001:1001::/home/user1:/bin/bash
user2:x:1002:1002::/home/user2:/bin/bash
user3:x:1003:1003::/home/user3:/bin/bash
[root@core-pods-3 ~]# 
[root@core-pods-3 ~]# cat /etc/passwd | grep /bin/bash
root:x:0:0:root:/root:/bin/bash
linzhiling:x:1000:1000::/home/linzhiling:/bin/bash
user1:x:1001:1001::/home/user1:/bin/bash
user2:x:1002:1002::/home/user2:/bin/bash
user3:x:1003:1003::/home/user3:/bin/bash
[root@core-pods-3 ~]# cat /etc/passwd | grep /bin/bash | grep -v root
linzhiling:x:1000:1000::/home/linzhiling:/bin/bash
user1:x:1001:1001::/home/user1:/bin/bash
user2:x:1002:1002::/home/user2:/bin/bash
user3:x:1003:1003::/home/user3:/bin/bash
[root@core-pods-3 ~]# cat /etc/passwd | grep /bin/bash | grep -v root | cut -d ":" -f 1
linzhiling
user1
user2
user3
[root@core-pods-3 ~]# 
```

```bash
# cut 命令使用局限
# 例子：判断磁盘根分区使用率，如果超过80%，设置告警
[root@core-pods-3 ~]# df -h
Filesystem      Size  Used Avail Use% Mounted on
devtmpfs        494M     0  494M   0% /dev
tmpfs           504M     0  504M   0% /dev/shm
tmpfs           504M   26M  479M   6% /run
tmpfs           504M     0  504M   0% /sys/fs/cgroup
/dev/sda2        22G  3.4G   17G  17% /
/dev/sda1       380M  257M  104M  72% /boot
tmpfs           101M     0  101M   0% /run/user/0
[root@core-pods-3 ~]# df -h | grep "sda2"    # 精确到sda2行
/dev/sda2        22G  3.4G   17G  17% /
[root@core-pods-3 ~]# df -h | grep "sda2" | cut -f 5   # 因为每列之间不是tab分隔，都是空格分隔，所以下面输出失效
/dev/sda2        22G  3.4G   17G  17% /
[root@core-pods-3 ~]# df -h | grep "sda2" | cut -d " " -f 5  # 即便定义了以空格分隔，也是有问题的，因为我们只定义了1个空格，所以cut会认为我们以1个空格分隔，而因为第五列是空格，所以输出空格

[root@core-pods-3 ~]# 
# 上面这种情况可以使用后面的awk命令，但是awk远比cut复杂，能实现优先使用cut
```

### printf命令
printf严格上来说不算是字符截取命令，因为这个是awk命令里最基本的输出指令，所以先介绍printf命令，printf是格式化输出命令，命令是：`printf '输出类型输出格式' 输出内容`: 
1. 输出类型：
   - %ns：输出字符串。n是数字指代输出几个字符
   - %ni：输出整数。n是数字指代输出几个数字
   - %m.nf：输出浮点数。m和n是数字，指代输出的整数位数和小数位数。如%8.2f代表共输出8位数，其中2位是小数，6位是整数。
2. 输出格式：
   - \a：输出警告声音
   - \b：输出退格键，也就是Backspace键
   - \f：清除屏幕
   - \n：换行
   - \r：回车，也就是Enter键
   - \t：水平输出退格键，也就是Tab键
   - \v：垂直输出退格键，也就是Tab键
3. 在awk命令的输出中支持print和printf命令
   - print：print会在每个输出之后自动加入一个换行符（Linux默认没有print命令）
   - printf：printf是标准格式输出命令，并不会自动加入换行符，如果需要换行，需要手工加入换行符

```bash
# 练习
[root@core-pods-3 ~]# printf %s 1 2 3 4 5 6
123456[root@core-pods-3 ~]# 
[root@core-pods-3 ~]# printf %s %s %s 1 2 3 4 5 6
%s%s123456[root@core-pods-3 ~]# 
[root@core-pods-3 ~]# printf '%s %s %s' 1 2 3 4 5 6
1 2 34 5 6[root@core-pods-3 ~]# printf '%s %s %s\n' 1 2 3 4 5 6
1 2 3
4 5 6
[root@core-pods-3 ~]# printf '%s' student.txt 
student.txt[root@core-pods-3 ~]# 
[root@core-pods-3 ~]# cat student.txt | printf '%s'
[root@core-pods-3 ~]# printf '%s' $(cat student.txt)
IDNamegenderMark1MoeF902JalenM803JonesM95[root@core-pods-3 ~]# 
[root@core-pods-3 ~]# cat student.txt 
ID      Name    gender  Mark
1       Moe     F       90
2       Jalen   M       80
3       Jones   M       95
[root@core-pods-3 ~]# printf '%s\t %s\t %s\t %s\t \n' $(cat student.txt)
ID       Name    gender  Mark    
1        Moe     F       90      
2        Jalen   M       80      
3        Jones   M       95      
[root@core-pods-3 ~]# 
```

### awk命令
#### 说明
awk比较复杂，其实awk的一些复杂命令都可以通过shell脚本实现，而且更简单。awk叫做awk编程更贴切，awk更像是一门语言，awk也是做列截取，并且可以做到cut能做的所有事情，也可以做cut做不到事情，比如：
```bash
[root@core-pods-3 ~]# df -h
Filesystem      Size  Used Avail Use% Mounted on
devtmpfs        494M     0  494M   0% /dev
tmpfs           504M     0  504M   0% /dev/shm
tmpfs           504M   26M  479M   6% /run
tmpfs           504M     0  504M   0% /sys/fs/cgroup
/dev/sda2        22G  3.4G   17G  17% /
/dev/sda1       380M  257M  104M  72% /boot
tmpfs           101M     0  101M   0% /run/user/0
[root@core-pods-3 ~]# df -h | cut -d " " -f 5








[root@core-pods-3 ~]# 
```

#### 命令格式
> awk '条件1{动作1} 条件2{动作2}...' 文件名
- 条件（Pattern）：一般使用关系表达式作为条件
  - x>10：判断变量x是否大于10
  - x>=10：大于等于
  - x<=10：小于等于
- 动作（Action）：
  - 格式化输出
  - 流程控制语句
```bash
[root@core-pods-3 ~]# cat student.txt 
ID      Name    gender  Mark
1       Moe     F       90
2       Jalen   M       80
3       Jones   M       95
[root@core-pods-3 ~]# awk '{printf $2 "\t" $4 "\n"}' student.txt  # 指定格式
Name    Mark
Moe     90
Jalen   80
Jones   95
[root@core-pods-3 ~]# awk '{printf $2 $4}' student.txt  # 不指定格式
NameMarkMoe90Jalen80Jones95[root@core-pods-3 ~]# 
[root@core-pods-3 ~]# 

# 查看根分区占比
[root@core-pods-3 ~]# df -h | awk '{print $1 "\t" $5 "\t" $6}'
Filesystem      Use%    Mounted
devtmpfs        0%      /dev
tmpfs   0%      /dev/shm
tmpfs   6%      /run
tmpfs   0%      /sys/fs/cgroup
/dev/sda2       17%     /
/dev/sda1       72%     /boot
tmpfs   0%      /run/user/0
[root@core-pods-3 ~]# df -h | awk '{print $1 "\t" $3}'
Filesystem      Used
devtmpfs        0
tmpfs   0
tmpfs   26M
tmpfs   0
/dev/sda2       3.4G
/dev/sda1       257M
tmpfs   0
[root@core-pods-3 ~]# 

# 提取sda2分区使用占比
[root@core-pods-3 ~]# df -h | grep sda2
/dev/sda2        22G  3.4G   17G  17% /
[root@core-pods-3 ~]# df -h | grep sda2 | awk '{print $5}'
17%
[root@core-pods-3 ~]# df -h | grep sda2 | awk '{print $5}' | cut -d "%" -f 1
17
[root@core-pods-3 ~]#

# BEGIN，在所有数据读取之前处理这个命令
[root@core-pods-3 ~]# awk 'BEGIN {print "This is a test transcript!"} {print $2 "\t" $4}' student.txt 
This is a test transcript!   # 这里输出开始打印语句
Name    Mark
Moe     90
Jalen   80
Jones   95
[root@core-pods-3 ~]# cat /etc/passwd | grep "/bin/bash" | awk '{FS=":"} {print $1 "\t" $3}'
root:x:0:0:root:/root:/bin/bash   # 这里可以发现第一行未处理，因为awk从第2行开始执行
linzhiling      1000
user1   1001
user2   1002
user3   1003
[root@core-pods-3 ~]# 
[root@core-pods-3 ~]# cat /etc/passwd | grep "/bin/bash" | awk 'BEGIN {FS=":"} {print $1 "\t" $3}'
root    0           # 添加BEGIN命令后发现第1行执行了
linzhiling      1000
user1   1001
user2   1002
user3   1003
[root@core-pods-3 ~]#

# END使用和BEGIN相反，在末尾操作
[root@core-pods-3 ~]# cat /etc/passwd | grep "/bin/bash" | awk 'BEGIN {FS=":"} END{print "This is the end!"} {print $1 "\t" $3}'
root    0
linzhiling      1000
user1   1001
user2   1002
user3   1003
This is the end!   # 这里输出结尾打印语句
[root@core-pods-3 ~]# 

# 读取student文件，统计分数大于等于90的同学姓名
[root@core-pods-3 ~]# cat student.txt 
ID      Name    gender  Mark
1       Moe     F       90
2       Jalen   M       80
3       Jones   M       95
[root@core-pods-3 ~]# cat student.txt | grep -v Name | awk '$4 >= 90 {printf $2 "\n"}'
Moe
Jones
[root@core-pods-3 ~]# 
```

### sed命令
#### 说明
其实sed命令并不是一个截取命令，它是一种几乎包括在所有unix平台（包括linux）的轻量级流编辑器。sed主要是用来将数据进行选取、替换、删除、新增的命令。这有点类似vi编辑，但是vi不能直接得到命令的最终结果，而是要将命令的结果先输出到一个文件里，才能使用vi进行二次编辑。而sed不仅可以修改文件，还可以从管道符接收数据源，即支持管道符操作。写shell脚本时候sed是一个比较重要的命令，但是sed远没有awk强大。
#### 格式
> sed [选项] '[动作]' 文件名
1. 选项
   - -n：一般sed命令会把所有数据都输出到屏幕，如果加入此选择，则只会把经过sed命令处理的行输出到屏幕
   - -e：允许对输入数据应用多条sed命令编辑
   - -i：用sed的修改结果直接修改读取数据的文件，而不是由屏幕输出
2. 动作
   - a \：追加，在当前行后添加一行或多行。添加多行时，除最后一行外，每行末尾需要用“\”代表数据未完结
   - c \：行替换，用c后面的字符串替换原数据行，替换多行时，除最后一行外，每行末尾需用“\”代表数据未完结。
   - i \：插入，在当前行前插入一行或多行。插入多行时，除最后一行外，每行末尾需要用“\”代表数据未完结
   - d：删除，删除指定的行
   - p：打印，输出指定的行
   - s：字串替换，用一个字符串替换另外一个字符串。格式为“行范围s/旧字串/新字串/g” (和vim中的替换格式类似)
```bash
[root@core-pods-3 ~]# ls \      # 反斜杠代表命令未完结
> /root
student.txt
[root@core-pods-3 ~]# cat student.txt 
ID      Name    gender  Mark
1       Moe     F       90
2       Jalen   M       80
3       Jones   M       95
[root@core-pods-3 ~]# sed '2p' student.txt   # 发现输出第2行后又重复打印了表格
ID      Name    gender  Mark
1       Moe     F       90
1       Moe     F       90
2       Jalen   M       80
3       Jones   M       95
[root@core-pods-3 ~]# sed -n '2p' student.txt # 使用-n只输出经过sed处理过的行
1       Moe     F       90
[root@core-pods-3 ~]# df -h | sed -n '2p'  # sed可以配合管道符一起使用
devtmpfs        494M     0  494M   0% /dev
[root@core-pods-3 ~]# cat student.txt 
ID      Name    gender  Mark
1       Moe     F       90
2       Jalen   M       80
3       Jones   M       95
[root@core-pods-3 ~]# sed '2,4d' student.txt  # 删除第2行到第4行，输出剩余行
ID      Name    gender  Mark
[root@core-pods-3 ~]# cat student.txt   # 上述不改变文件内容
ID      Name    gender  Mark
1       Moe     F       90
2       Jalen   M       80
3       Jones   M       95
[root@core-pods-3 ~]# sed '2,3d' student.txt  # 删除第2行到第3行，输出剩余行
ID      Name    gender  Mark
3       Jones   M       95
[root@core-pods-3 ~]# cat student.txt   # 上述不改变文件内容
ID      Name    gender  Mark
1       Moe     F       90
2       Jalen   M       80
3       Jones   M       95
[root@core-pods-3 ~]# sed '2a hello' student.txt   # 在第2行后加hello
ID      Name    gender  Mark
1       Moe     F       90
hello
2       Jalen   M       80
3       Jones   M       95
[root@core-pods-3 ~]# sed '2i hello world' student.txt    # 在第2行前加hello world
ID      Name    gender  Mark
hello 
world
1       Moe     F       90
2       Jalen   M       80
3       Jones   M       95
[root@core-pods-3 ~]# sed '2c No such person' student.txt # 替换第2行
ID      Name    gender  Mark
No such person
2       Jalen   M       80
3       Jones   M       95
[root@core-pods-3 ~]# cat student.txt 
ID      Name    gender  Mark
1       Moe     F       90
2       Jalen   M       80
3       Jones   M       95
[root@core-pods-3 ~]# sed '3s/80/60/g' student.txt   # 第3行80替换为60
ID      Name    gender  Mark
1       Moe     F       90
2       Jalen   M       60
3       Jones   M       95
[root@core-pods-3 ~]# cat student.txt 
ID      Name    gender  Mark
1       Moe     F       90
2       Jalen   M       80
3       Jones   M       95
[root@core-pods-3 ~]# sed -i '3s/80/65/g' student.txt  # 第3行80替换为65并写入文件
[root@core-pods-3 ~]# cat student.txt 
ID      Name    gender  Mark
1       Moe     F       90
2       Jalen   M       65
3       Jones   M       95
[root@core-pods-3 ~]# sed -e 's/Moe/Moe1/g;s/Jalen//g;s/Jones/Bella/g' student.txt    # 多条sed 替换命令
ID      Name    gender  Mark
1       Moe1    F       90
2               M       65
3       Bella   M       95
[root@core-pods-3 ~]# cat student.txt 
ID      Name    gender  Mark
1       Moe     F       90
2       Jalen   M       65
3       Jones   M       95
```

## 字符处理命令

### 排序命令sort
> sort [选项] 文件名
1. 选项
   - -f：忽略大小写
   - -n：以数值型进行排序。（因为默认使用字符串型排序）
   - -r：反向排序
   - -t：指定分隔符，默认的分隔符是制表符
   - -k n[,m]：按照指定的字段范围排序。从第n个字段开始，到第m个字段结束（默认到行尾）
```bash
# 练习
[root@core-pods-3 ~]# sort /etc/passwd      # 按字母顺序排序
[root@core-pods-3 ~]# sort -r /etc/passwd   # 按字母顺序倒序排序
[root@core-pods-3 ~]# sort -t ":" -k 3,3 /etc/passwd  # 指定分隔符是:，用第3个字段开头，第3个字段结尾排序，就是只用第3个字段排序 （注意这里没有把uid当成数字来排序，而是当成字符串来排序的）
[root@core-pods-3 ~]# sort -n -t ":" -k 3,3 /etc/passwd  # 和上面类似，这里当成数值来排序
```

### 统计命令wc
> wc [选项] 文件名
1. 选项
   - -l：只统计行数
   - -w：只统计单词数
   - -m：只统计字符数
```bash
# 练习
[root@core-pods-3 ~]# wc /etc/passwd
  26   48 1244 /etc/passwd
[root@core-pods-3 ~]# wc -l /etc/passwd
26 /etc/passwd
[root@core-pods-3 ~]# df -h | wc -l
8
[root@core-pods-3 ~]# 
```

## 条件判断

1. 按照文件类型进行判断

|测试选项|作用|
|---|---|
|-b 文件|判断该文件是否存在，并且是否为块设备文件（是块设备文件为真）|
|-c 文件|判断该文件是否存在，并且是否为字符设备文件（是字符设备文件为真）|
|**-d 文件**|判断该文件是否存在，并且是否为**目录文件**（是目录为真）|
|**-e 文件**|***判断文件是否存在***（存在为真）|
|**-f 文件**|判断该文件是否存在，并且是否为**普通文件**（是普通文件为真）|
|-L 文件|判断该文件是否存在，并且是否为符号链接文件（是符号链接文件为真）|
|-p 文件|判断该文件是否存在，并且是否为管道文件（是管道文件为真）|
|-s 文件|判断该文件是否存在，并且是否为非空（非空为真）|
|-S 文件|判断该文件是否存在，并且是否为套接字文件（是套接字文件为真）|

两种判断格式
```bash
[root@core-pods-3 ~]# ls
student.txt
[root@core-pods-3 ~]# test -e student.txt   # 方式1
[root@core-pods-3 ~]# echo $?
0
[root@core-pods-3 ~]# test -e xxx.txt
[root@core-pods-3 ~]# echo $?
1
[root@core-pods-3 ~]# [ -e student.txt ]    # 方式2，脚本中常用方式
[root@core-pods-3 ~]# echo $?
0
[root@core-pods-3 ~]# [ -e xxx.txt ]
[root@core-pods-3 ~]# echo $?
1

# 判断目录并输出
[root@core-pods-3 ~]# [ -d /root ] && echo "yes" || echo "no"
yes
[root@core-pods-3 ~]# [ -d /jalen ] && echo "yes" || echo "no"
no
[root@core-pods-3 ~]# [ -f /root/student.txt ] && echo 'yes' || echo 'no'
yes
```

2. 按照文件权限进行判断

|测试选项|作用|
|---|---|
|**-r 文件**|判断该文件是否存在，并且是否该文件拥有**读**权限（有读权限为真）|
|**-w 文件**|判断该文件是否存在，并且是否该文件拥有**写**权限（有写权限为真）|
|**-x 文件**|判断该文件是否存在，并且是否该文件拥有**执行**权限（有执行权限为真）|
|-u 文件|判断该文件是否存在，并且是否该文件拥有SUID权限（有SUID权限为真）|
|-g 文件|判断该文件是否存在，并且是否该文件拥有SGID权限（有SGID权限为真）|
|-k 文件|判断该文件是否存在，并且是否该文件拥有SBit权限（有SBit权限为真）|

```bash
# 注意u，g，o三个里面只要有一个有w权限，就会返回yes，r和x类似，不会细分
[root@core-pods-3 ~]# ll
total 4
-rw-r--r-- 1 root root 57 Aug  2 03:14 student.txt
[root@core-pods-3 ~]# [ -w student.txt ] && echo "yes" || echo "no"
yes
[root@core-pods-3 ~]# [ -r student.txt ] && echo "yes" || echo "no"
yes
[root@core-pods-3 ~]# [ -x student.txt ] && echo "yes" || echo "no"
no
[root@core-pods-3 ~]# 
```

3. 两个文件之间进行比较

|测试选项|作用|
|---|---|
|文件1 -nt 文件2|判断文件1的修改时间是否比文件2的新（如果新则为真）|
|文件1 -ot 文件2|判断文件1的修改时间是否比文件2的老（如果老则为真）|
|文件1 -ef 文件2|判断文件1是否和文件2的Inode号一致，可以理解为两个文件是否为同一个文件。这个判断用于判断硬链接是很好的方法

```bash
[root@core-pods-3 ~]# ll
total 4
-rw-r--r-- 1 root root 57 Aug  2 03:14 student.txt
-rw-r--r-- 1 root root  0 Aug  2 04:06 teacher.txt
[root@core-pods-3 ~]# ln /root/student.txt /tmp/stu.txt  # 创建硬链接

# 硬链接直接通过ll命令看不出来
[root@core-pods-3 ~]# ll
total 4
-rw-r--r-- 2 root root 57 Aug  2 03:14 student.txt
-rw-r--r-- 1 root root  0 Aug  2 04:06 teacher.txt
[root@core-pods-3 tmp]# ll /tmp/
total 4
-rw-r--r-- 2 root root 57 Aug  2 03:14 stu.txt

# 相反，软连接通过ll可以看出来
[root@core-pods-3 tmp]# ll /etc/
total 1448
... ...
lrwxrwxrwx    1 root root       22 Jul  7 03:03 grub2.cfg -> ../boot/grub2/grub.cfg
lrwxrwxrwx    1 root root       11 Jul  7 03:02 init.d -> rc.d/init.d
lrwxrwxrwx    1 root root       21 Jul  7 03:02 os-release -> ../usr/lib/os-release
... ...

# 硬链接通过查Inode确认，如下，都是286
[root@core-pods-3 ~]# ll -i student.txt 
286 -rw-r--r-- 2 root root 57 Aug  2 03:14 student.txt
[root@core-pods-3 ~]# ll -i /tmp/stu.txt 
286 -rw-r--r-- 2 root root 57 Aug  2 03:14 /tmp/stu.txt

# 程序里判断Inode号
[root@core-pods-3 ~]# [ /root/student.txt -ef /tmp/stu.txt ] && echo "yes" || echo "no"
yes
```

4. 两个整数之间比较

|测试选项|作用|
|---|---|
|整数1 -eq 整数2|判断整数1和整数2相等（相等为真）|
|整数1 -ne 整数2|判断整数1和整数2是否不相等（不相等为真）|
|整数1 -gt 整数2|判断整数1是否大于整数2（大于为真）|
|整数1 -lt 整数2|判断整数1是否小于整数2（小于为真）|
|整数1 -ge 整数2|判断整数1是否大于等于整数2（大于等于为真）|
|整数1 -le 整数2|判断整数1是否小于等于整数2（小于等于为真）|

```bash
# 练习
[root@core-pods-3 ~]# [ 10 -eq 10 ] && echo "yes" || echo "no"
yes
[root@core-pods-3 ~]# [ 10 -eq 11 ] && echo "yes" || echo "no"
no
[root@core-pods-3 ~]# [ 10 -gt 11 ] && echo "yes" || echo "no"
no
[root@core-pods-3 ~]# [ 10 -gt 9 ] && echo "yes" || echo "no"
yes
```

5. 字符串的判断

|测试选项|作用|
|---|---|
|-z 字符串|判断字符串是否为空（为空返回真）|
|-n 字符串|判断字符串是否为非空（非空返回真）|
|字串1==字串2|判断字符串1是否和字符串2相等（相等返回真），单等于号其实在shell中也可以，但是为了不和赋值操作搞混淆，推荐使用双等于号|
|字串1!=字串2|判断字符串1是否和字符串2不相等（不相等返回真）|

```bash
[root@core-pods-3 ~]# name=jalen
[root@core-pods-3 ~]# echo $name
jalen
[root@core-pods-3 ~]# [ -z "$name" ] && echo "yes" || echo "no"
no
[root@core-pods-3 ~]# [ -n "$name" ] && echo "yes" || echo "no"
yes
[root@core-pods-3 ~]# name1=jalen
[root@core-pods-3 ~]# echo $name1
jalen
[root@core-pods-3 ~]# [ "$name" != "$name1" ] && echo "yes" || echo "no"
no
[root@core-pods-3 ~]# [ "$name" == "$name1" ] && echo "yes" || echo "no"
yes
[root@core-pods-3 ~]# 
```

6. 多重条件判断

|测试选项|作用|
|---|---|
|判断1 -a 判断2|逻辑与，判断1和判断2都成立，最终的结果才为真|
|判断1 -o 判断2|逻辑或，判断1和判断2有一个成立，最终的结果就为真|
|! 判断|逻辑非，使原始的判断式取反|

```bash
[root@core-pods-3 ~]# aa=11
[root@core-pods-3 ~]# [ -n "$aa" -a "$aa" -gt 23 ] && echo "yes" || echo "no"
no
[root@core-pods-3 ~]# aa=24
[root@core-pods-3 ~]# [ -n "$aa" -a "$aa" -gt 23 ] && echo "yes" || echo "no"
yes
```

## 流程控制

### if语句

1. 单分支if条件语句
   ```bash
   if [ 条件判断式 ];then
   程序
   fi
   或者
   if [ 条件判断式 ]
   then
      程序
   fi
   ```

   单分支条件语句注意以下几点：
   - if语句使用fi结尾，和一般语言使用大括号结尾不同
   - [ 条件判断式 ]就是使用test命令判断，所以中括号和条件判断式之间必须有空格
   - then后面跟符合条件之后执行的程序，可以放在`[之后]`，用“;”分割。也可以换行写入，就不需要“;”了

   例子：判断分区使用率
   ```bash
   [root@core-pods-3 ~]# vi test.sh
   #!/bin/bash
   # Statistics on root partition usage
   # Author: Jalen Chu

   rate=$(df -h | grep "/dev/sda2" | awk '{print $5}' | cut -d "%" -f 1)

   if [ $rate -ge 15 ]
   then
      echo "Warning! /dev/sda2 is full! now is ${rate}%"
   fi
   [root@core-pods-3 ~]# chmod u+x test.sh 
   [root@core-pods-3 ~]# ./test.sh 
   Warning! /dev/sda2 is full! now is 17%
   ```

2. 双分支if条件语句
   ```bash
   if [ 条件判断式 ]
   then
      条件成立时，执行的程序
   else
      条件不成立时，执行的另一个程序
   fi
   ```

   例子1：备份mysql数据库
   ```bash
   # 安装mariadb
   [root@core-pods-3 ~]# yum install -y mysql
   [root@core-pods-3 ~]# yum install -y mariadb-server
   [root@core-pods-3 ~]# yum list installed | grep mariadb
   [root@core-pods-3 ~]# systemctl start mariadb
   [root@core-pods-3 ~]# mysql -u root -p   # 默认mariadb root账号无密码，直接回车登录
   MariaDB [(none)]> show databases;
   MariaDB [(none)]> create database demo;
   MariaDB [(none)]> use demo;
   MariaDB [demo]> create table test(id integer , name char(64), age integer);
   MariaDB [demo]> insert test values(1, "Jalen Chu", 18);
   MariaDB [demo]> select * from test;
   MariaDB [demo]> exit
   [root@core-pods-3 ~]# ls /var/lib/mysql/demo/   # mysql数据存储位置-刚刚建的demo数据库
   [root@core-pods-3 ~]# vi test.sh   # 写备份脚本

   #!/bin/bash
   # Description: mysql database backup
   # Author: Jalen Chu

   ntpdate asia.pool.ntp.org &>/dev/null
   date=$(date +%y%m%d)
   size=$(du -sh /var/lib/mysql)

   if [ -d /tmp/dbbak ]
   then
      echo "Date: $date!" > /tmp/dbbak/dbinfo.txt
      echo "Data size: $size" >> /tmp/dbbak/dbinfo.txt
      cd /tmp/dbbak
      tar -zcf mysql-lib-$date.tar.gz /var/lib/mysql dbinfo.txt &>/dev/null
      rm -rf /tmp/dbbak/dbinfo.txt
   else
      mkdir /tmp/dbbak
      echo "Date: $date!" > /tmp/dbbak/dbinfo.txt
      echo "Data size: $size" >> /tmp/dbbak/dbinfo.txt
      cd /tmp/dbbak
      tar -zcf mysql-lib-$date.tar.gz /var/lib/mysql dbinfo.txt &>/dev/null
      rm -rf /tmp/dbbak/dbinfo.txt
   fi

   [root@core-pods-3 ~]# chmod u+x test.sh
   [root@core-pods-3 ~]# ./test.sh 
   [root@core-pods-3 ~]# ls /tmp/dbbak/
   mysql-lib-220804.tar.gz
   ```

   例子2：判断apache是否启动
   ```bash
   # 注意这里没有用ps aux|grep httpd，是因为有时候apache进程还在但是不能访问了，比如恶意流量攻击
   # 这里也没有用netstat -tlun，和上面同样问题，不能确定你的apache是否能够正常连接，只能确认apache启动，并且也可能其他服务占用了80端口
   # 最有效的是通过nmap，远程扫描工具，扫描指定服务器开启的端口，如果能正确连接apache，会返回open
   [root@core-pods-3 ~]# yum install nmap
   [root@core-pods-3 ~]# yum install -y httpd
   [root@core-pods-3 ~]# systemctl start httpd
   [root@core-pods-3 ~]# service httpd status
   [root@core-pods-3 ~]# /usr/sbin/httpd -k start  # 测试启动
   [root@core-pods-3 ~]# nmap -sT 104.225.149.222 | grep tcp | grep http | awk '{print $2}'  # 80服务处于open状态
   [root@core-pods-3 ~]# vi test.sh 

   #!/bin/bash
   # Description: Check apache process start
   # Author: Jalen Chu

   port=$(nmap -sT 104.225.149.222 | grep tcp | grep http | awk '{print $2}' )

   if [ "$port" == "open" ]
   then
      echo "$(date) httpd is ok!" >> /tmp/autostart-apache.log
   else
      /usr/sbin/httpd -k start &>/dev/null
      echo "$(date) restart httpd!!" >> /tmp/autostart-apache.log
   fi

   [root@core-pods-3 ~]# ./test.sh 
   [root@core-pods-3 ~]# cat /tmp/autostart-apache.log 
   Thu Aug  4 02:39:02 EDT 2022 httpd is ok!

   [root@core-pods-3 ~]# /usr/sbin/httpd -k stop   # 关闭
   [root@core-pods-3 ~]# nmap -sT 104.225.149.222   # 看不到80端口open了
   [root@core-pods-3 ~]# ./test.sh 
   [root@core-pods-3 ~]# cat /tmp/autostart-apache.log 
   Thu Aug  4 02:39:02 EDT 2022 httpd is ok!
   Thu Aug  4 02:42:24 EDT 2022 restart httpd!!
   ```

3. 多分支if条件语句
   ```bash
   if [ 条件判断式1 ]
     then
       当条件判断式1成立时，执行程序1
   elif [ 条件判断式2 ]
     then
       当条件判断式2成立时，执行程序2
     ... ...
   else
     当所有条件都不成立时，最后执行此程序
   fi
   ```

   例子：判断输入的文件类型
   ```bash
   [root@core-pods-3 ~]# vi test.sh
   #!/bin/bash
   # Description: Check user input
   # Author: Jalen Chu

   read -p "Please input a filename: " file

   if [ -z "$file" ]
   then
      echo "Error, please input a filename."
      exit 1
   elif [ ! -e "$file" ]
   then
      echo "Your input is not a file!"
      exit 2
   elif [ -f "$file" ]
   then
      echo "$file is a regulare file!"
   elif [ -d  "$file" ]
   then
      echo "$file is a directory!"
   else
   echo "$file is an other file!"
   fi

   [root@core-pods-3 ~]# ./test.sh 
   Please input a filename: 
   Error, please input a filename.
   [root@core-pods-3 ~]# ./test.sh 
   Please input a filename: xxxx
   Your input is not a file!
   [root@core-pods-3 ~]# ./test.sh 
   Please input a filename: /root  
   /root is a directory!
   [root@core-pods-3 ~]# ./test.sh 
   Please input a filename: /etc/passwd
   /etc/passwd is a regulare file!
   ```

### case语句
多分支case条件语句，case语句和if...elif...else语句一样都是多分支条件语句，不过和if多分支条件语句不同的是，case语句只能判断一种条件关系，而if语句可以判断多种条件关系。格式：
```bash
case $变量名 in
  "值1")
    如果变量的值等于值1，则执行顺序1
    ;;
  "值2")
    如果变量的值等于值2，则执行顺序2
    ;;
  ... 省略其他分支 ...
  *)
    如果变量的值都不是以上的值，则执行此程序
    ;;
esac
```

例子：检查用户输入
```bash
[root@core-pods-3 ~]# vi test.sh
#!/bin/bash
# Description: Check user input
# Author: Jalen Chu

read -p "Please choose yes/no:" -t 30 cho

case $cho in
  "yes")
    echo "Your choose is yes!"
    ;;
  "no")
    echo "Your choose is no!"
    ;;
  *)
    echo "Your choose is error!"
    ;;
esac

[root@core-pods-3 ~]# ./test.sh 
Please choose yes/no:xxx
Your choose is error!
[root@core-pods-3 ~]# ./test.sh 
Please choose yes/no:yes
Your choose is yes!
[root@core-pods-3 ~]# ./test.sh 
Please choose yes/no:no
Your choose is no!
```

### for循环
1. 语法一
   ```bash
   for 变量 in 值1 值2 值3...
   do
      程序
   done
   ```

   例子1：打印时间
   ```bash
   [root@core-pods-3 ~]# vi test.sh
   #!/bin/bash
   # Description: Print time
   # Author: Jalen Chu

   for time in morning noon afternoon evening
   do
      echo "This time is $time!"
   done

   [root@core-pods-3 ~]# ./test.sh 
   This time is morning!
   This time is noon!
   This time is afternoon!
   This time is evening!
   [root@core-pods-3 ~]# 
   ```

   例子2：批量解压文件
   ```bash
   [root@core-pods-3 ~]# vi test.sh
   #!/bin/bash
   # Description: De compress files
   # Author: Jalen Chu

   cd /lamp
   ls *.tar.gz > ls.log
   for i in $(cat ls.log)
   do 
      tar -zxf $i &>/dev/null
   done
   rm -rf /lamp/ls.log
   ```

2. 语法二
   ```bash
   for((初始值;循环控制条件;变量变化))
   do
      程序
   done
   ```

   例子1：从1加到100
   ```bash
   #!/bin/bash
   # Description: 1+2+3+...+99+100
   # Author: Jalen Chu

   sum=0
   for((i=1;i<=100;i=i+1))
   do
      sum=$(($sum+$i))
   done
   echo "The sum of 1+2+3+...+100 is $sum"

   [root@core-pods-3 ~]# ./test.sh 
   The sum of 1+2+3+...+100 is 5050
   [root@core-pods-3 ~]# 
   ```

   例子2：批量添加指定数量的用户
   ```bash
   [root@core-pods-3 ~]# vi test.sh
   #!/bin/bash
   # Description: Batch add users
   # Author: Jalen Chu

   read -p "Please input username: " -t 30 name
   read -p "Please input the number of users: " -t 30 num
   read -p "Please input the password of users: " -t 30 pass

   if [ ! -z "$name" -a ! -z "$num" -a ! -z "$pass" ]
     then
       y=$(echo $num | sed 's/^[0-9]*$//g')
       if [ -z "$y" ]
         then
         for((i=1;i<=$num;i=i+1))
            do
               /usr/sbin/useradd $name$i &>/dev/null
               echo $pass | /usr/bin/passwd --stdin $name$i &>/dev/null
            done
       fi
   fi

   [root@core-pods-3 ~]# ./test.sh
   Please input username: Jalen
   Please input the number of users: 5
   Please input the password of users: 123456
   [root@core-pods-3 ~]# cat /etc/passwd | grep Jalen
   Jalen1:x:1004:1004::/home/Jalen1:/bin/bash
   Jalen2:x:1005:1005::/home/Jalen2:/bin/bash
   Jalen3:x:1006:1006::/home/Jalen3:/bin/bash
   Jalen4:x:1007:1007::/home/Jalen4:/bin/bash
   Jalen5:x:1008:1008::/home/Jalen5:/bin/bash
   [root@core-pods-3 ~]# 
   ```

### while循环和until循环
#### while循环
while循环是不定循环，也称作条件循环。只要条件判断式成立，循环就会一直继续，直到条件判断式不成立，循环才会停止。这就和for的固定循环不一样了。格式：
```bash
while [ 条件判断式 ]
  do
    程序
  done
```

例子1：从1加到100
```bash
[root@core-pods-3 ~]# vi test.sh
#!/bin/bash
# Description: 1+2+3+...+100
# Author: Jalen Chu

i=1
s=0
while [ $i -le 100 ]
  do
    s=$(($s+$i))
    i=$(($i+1))
  done
echo $s

[root@core-pods-3 ~]# ./test.sh 
5050
```

#### until循环
until循环，和while循环相反，until循环时只要条件判断式不成立则进行循环，并执行循环程序。一旦循环条件成立，则终止循环。
```bash
[root@core-pods-3 ~]# vi test.sh
#!/bin/bash
# Description: 1+2+3+...+100
# Author: Jalen Chu

i=1
s=0
until [ $i -gt 100 ]
  do
    s=$(($s+$i))
    i=$(($i+1))
  done
echo $s

[root@core-pods-3 ~]# ./test.sh 
5050
```

## 结束语
整体大概10节课左右，总算看完了，虽然有些本来就会一些，但也有许多新get的知识点，谢谢沈超老师~
Shell的学习主要还是多练习，包括它的语法结构，以及一些变量、程序语句的使用，不过目前实际工作中用到的场景不多，也许后面会忘记，暂且记录下，方便若干时间后查缺补漏。

## 课程视频地址
[请点击此处](https://www.bilibili.com/video/BV1mW411i7Qf?p=79)

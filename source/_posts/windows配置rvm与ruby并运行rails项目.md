---
title: windows配置rvm与ruby并运行rails项目(WSL)
date: 2021-11-03 14:34:43
tags: 
- rvm
- ruby 
- rails
categories:
valine:
  placeholder: "祝食用愉快💪"
---

> Microsoft建议您使用Windows子系统Linux（WSL）和Bash来进行Ruby开发。WSL是Windows 10的一项功能，可让您在Windows上运行本机Linux命令行工具。许多Ruby库设计为在Linux上运行，并且在Windows上运行时可能会出现问题。

> 以项目：`https://github.com/yigger/jiezhang/releases/download/open-source-v1/backend-rails.zip` 为例

# 在Windows上安装Bash，启用Developer模式
首先，在您的计算机上启用Developer模式。为此，请打开“ 设置”应用，选择“ 更新和安全”，然后在侧栏中选择“ 开发人员”条目。然后检查开发人员模式选项并接受提示，要求您验证此更改。

# 启用Subsystem For Linux
打开“ 控制面板”并选择“ 程序”。然后选择打开或关闭Windows功能。在显示的组件列表中，选中Windows Subsystem For Linux（Beta）选项。然后单击“ 确定”并等待Windows安装其他组件，这可能需要几分钟时间。系统将提示您重新启动计算机以确保正确配置所有新组件。

# 重启后输入bash进行命令行安装
```bash
Microsoft Windows [Version 10.0.19042.1288]
(c) Microsoft Corporation. All rights reserved.

C:\Users\jalchu>bash
Windows Subsystem for Linux has no installed distributions.
Distributions can be installed by visiting the Microsoft Store:
https://aka.ms/wslstore

C:\Users\jalchu> 
```

# 安装ubuntu
根据上面提示自行去Microsoft app store下载，一般会选择下载ubuntu命令行进行安装，安装时会设置用户名和密码，安装后重新输入bash测试

# 使用该gpg命令联系公钥服务器并请求RVM项目的密钥
使用该gpg命令联系公钥服务器并请求RVM项目的密钥，该密钥用于对每个RVM版本进行签名。如果报gpg: keyserver receive failed: No name尝试切换其他server获取
```bash
jalchu@DESKTOP-4S53KD0:/mnt/c/Users/jalchu$ gpg --keyserver hkp://pgp.mit.edu --recv-keys 409B6B1796C275462A1703113804BB
82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB
gpg: /home/jalchu/.gnupg/trustdb.gpg: trustdb created
gpg: key 105BD0E739499BDB: public key "Piotr Kuczynski <piotr.kuczynski@gmail.com>" imported
gpg: Total number processed: 1
gpg:               imported: 1
```

# 安装rvm
```bash
sudo apt-get install software-properties-common
sudo apt-add-repository -y ppa:rael-gc/rvm
sudo apt-get update
sudo apt-get install rvm
sudo usermod -a -G rvm $USER
```

# 重启下命令行，测试rvm
```bash
jalchu@DESKTOP-4S53KD0:/mnt/c/Users/jalchu$ rvm -v
rvm 1.29.12 (manual) by Michal Papis, Piotr Kuczynski, Wayne E. Seguin [https://rvm.io]
jalchu@DESKTOP-4S53KD0:/mnt/c/Users/jalchu$
```

# rvm install ruby报日志目录权限问题
尝试执行下面几个命令后好转
```bash
rvmsudo rvm get stable --auto-dotfiles
rvm fix-permissions system
rvm group add rvm $USER

rvm fix-permissions system
rvm fix-permissions user
rvm fix-permissions
```
```bash
jalchu@DESKTOP-4S53KD0:/mnt/c/Users/jalchu$ rvm install ruby
Searching for binary rubies, this might take some time.
Found remote file https://rvm_io.global.ssl.fastly.net/binaries/ubuntu/20.04/x86_64/ruby-3.0.0.tar.bz2
Checking requirements for ubuntu.
Installing requirements for ubuntu.
Updating system....
Installing required packages: libncurses5-dev, libgmp-dev, libssl-dev.......
Requirements installation successful.
ruby-3.0.0 - #configure
ruby-3.0.0 - #download
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 26.7M  100 26.7M    0     0  4881k      0  0:00:05  0:00:05 --:--:-- 6341k
ruby-3.0.0 - #validate archive
ruby-3.0.0 - #extract
ruby-3.0.0 - #validate binary
ruby-3.0.0 - #setup
ruby-3.0.0 - #gemset created /usr/share/rvm/gems/ruby-3.0.0@global
ruby-3.0.0 - #importing gemset /usr/share/rvm/gemsets/global.gems..................................
ruby-3.0.0 - #generating global wrappers........
ruby-3.0.0 - #gemset created /usr/share/rvm/gems/ruby-3.0.0
ruby-3.0.0 - #importing gemsetfile /usr/share/rvm/gemsets/default.gems evaluated to empty gem list
ruby-3.0.0 - #generating default wrappers........
jalchu@DESKTOP-4S53KD0:/mnt/c/Users/jalchu$
jalchu@DESKTOP-4S53KD0:/mnt/c/Users/jalchu$ ruby -v
ruby 3.0.0p0 (2020-12-25 revision 95aff21468) [x86_64-linux]
jalchu@DESKTOP-4S53KD0:/mnt/c/Users/jalchu$
jalchu@DESKTOP-4S53KD0:/mnt/c/Users/jalchu$ pwd
/mnt/c/Users/jalchu
jalchu@DESKTOP-4S53KD0:/mnt/c/Users/jalchu$ nano hello.rb   # 输入puts "Hello, world!"
jalchu@DESKTOP-4S53KD0:/mnt/c/Users/jalchu$ ls hello.rb
hello.rb
jalchu@DESKTOP-4S53KD0:/mnt/c/Users/jalchu$ ruby hello.rb
Hello, world!
jalchu@DESKTOP-4S53KD0:/mnt/c/Users/jalchu$
```

# rubymine添加Ruby SDK and Gems，New remote，选择WSL，选择刚刚自己WSL里ruby的路径
```bash
jalchu@DESKTOP-4S53KD0:/mnt/c/Users/jalchu$ whereis ruby
ruby: /usr/share/rvm/rubies/ruby-3.0.0/bin/ruby /mnt/d/ruby/Ruby27-x64/bin/ruby.exe
```

# rubymine根据提示信息install gem失败，版本报错
ruby_dep-1.5.0 requires ruby version >= 2.2.5, ~> 2.2, which is incompatible with the current version, ruby 3.0.0p0

卸载3.0.0p0，安装2.7.2
```bash
jalchu@DESKTOP-4S53KD0:/mnt/c/Users/jalchu$ rvm uninstall 3.0.0p0
ruby-3.0.0p0 - #already gone
jalchu@DESKTOP-4S53KD0:/mnt/c/Users/jalchu$ rvm install 2.7.2
Searching for binary rubies, this might take some time.
Found remote file https://rvm_io.global.ssl.fastly.net/binaries/ubuntu/20.04/x86_64/ruby-2.7.2.tar.bz2
Checking requirements for ubuntu.
Requirements installation successful.
ruby-2.7.2 - #configure
ruby-2.7.2 - #download
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 21.3M  100 21.3M    0     0  4800k      0  0:00:04  0:00:04 --:--:-- 5083k
ruby-2.7.2 - #validate archive
ruby-2.7.2 - #extract
ruby-2.7.2 - #validate binary
ruby-2.7.2 - #setup
ruby-2.7.2 - #gemset created /usr/share/rvm/gems/ruby-2.7.2@global
ruby-2.7.2 - #importing gemset /usr/share/rvm/gemsets/global.gems..................................
ruby-2.7.2 - #generating global wrappers........
ruby-2.7.2 - #gemset created /usr/share/rvm/gems/ruby-2.7.2
ruby-2.7.2 - #importing gemsetfile /usr/share/rvm/gemsets/default.gems evaluated to empty gem list
ruby-2.7.2 - #generating default wrappers........
jalchu@DESKTOP-4S53KD0:/mnt/c/Users/jalchu$ ruby -v
ruby 2.7.2p137 (2020-10-01 revision 5445e04352) [x86_64-linux]
jalchu@DESKTOP-4S53KD0:/mnt/c/Users/jalchu$ rvm -v
rvm 1.29.12 (manual) by Michal Papis, Piotr Kuczynski, Wayne E. Seguin [https://rvm.io]
```

# bundle install安装mysql报错

An error occurred while installing mysql2 (0.5.3), and Bundler cannot continue.

参考：https://stackoverflow.com/questions/45474191/an-error-occurred-while-installing-mysql2-0-4-8-and-bundler-cannot-continue

执行：sudo apt-get install libmysqlclient-dev

# bundle install安装rmagick报错

An error occurred while installing rmagick (2.13.4), and Bundler cannot continue.

参考：https://stackoverflow.com/questions/21901657/an-error-occurred-while-installing-rmagick-2-13-2-and-bundler-cannot-continue/21901885

执行：sudo apt-get install imagemagick libmagickcore-dev libmagickwand-dev

# gem bundle install 后就可以启动ruby项目啦

# rails项目启动报错

Uncaught exception: Could not find a JavaScript runtime. See https://github.com/rails/execjs for a list of available runtimes.

参考： https://github.com/sstephenson/execjs/issues/71

执行： sudo apt-get install nodejs

# rails项目启动报错

Uncaught exception: Operation now in progress - connect(2) would block

参考：https://stackoverflow.com/questions/58918869/ruby-on-rails-operation-now-in-progress-connect2-would-block

执行：sudo apt install redis-server

启动redis server

# 启动项目后日志
```log
C:\Windows\system32\wsl.exe --distribution Ubuntu --exec /bin/sh -c "export HOME=/home/jalchu && export RUBYMINE_TESTUNIT_REPORTER='/mnt/d/rubymine/RubyMine 2020.3.4/plugins/ruby/rb/testing/patch/testunit' && export WSL_DISTRO_NAME=Ubuntu && export TEAMCIY_RAKE_TU_AUTORUNNER_PATH=/usr/share/rvm/rubies/ruby-2.7.2/lib/ruby/gems/2.7.0/gems/test-unit-3.3.4/lib/test/unit/autorunner.rb && export SHELL=/bin/sh && export LOGNAME=jalchu && export WSLENV='' && export HOSTTYPE=x86_64 && export ANSICON='' && export TERM=xterm-256color && export NAME=DESKTOP-4S53KD0 && export RUBYLIB='/mnt/d/rubymine/RubyMine 2020.3.4/plugins/ruby/rb/testing/patch/common:/mnt/d/rubymine/RubyMine 2020.3.4/plugins/ruby/rb/testing/patch/rake:/mnt/d/rubymine/RubyMine 2020.3.4/plugins/ruby/rb/testing/patch/testunit:/home/jalchu/.gem/ruby/2.7.0/gems/debase-2.3.2/lib:/home/jalchu/.gem/ruby/2.7.0/gems/ruby-debug-ide-2.3.1/lib' && export TEAMCITY_RAKE_RUNNER_USED_FRAMEWORKS=':test_unit :shoulda ' && export USER=jalchu && export PWD='/mnt/d/rubymine/RubyMine 2020.3.4/jbr/bin' && export PATH='$PATH:/usr/share/rvm/rubies/ruby-2.7.2/bin:/usr/share/rvm/rubies/ruby-2.7.2/bin:/usr/share/rvm/rubies/ruby-2.7.2/lib/ruby/gems/2.7.0/bin:/home/jalchu/.gem/ruby/2.7.0/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/mnt/d/vmware/bin/:/mnt/c/Program Files/Python36/:/mnt/c/Program Files/Python36/Scripts/:/mnt/c/Program Files (x86)/Common Files/Oracle/Java/javapath:/mnt/c/Windows/system32:/mnt/c/Windows:/mnt/c/Windows/System32/Wbem:/mnt/c/Windows/System32/WindowsPowerShell/v1.0/:/mnt/c/Windows/System32/OpenSSH/:/mnt/d/xftp/:/mnt/d/xshell/:/mnt/c/Program Files/Java/jdk1.8.0_291/bin:/mnt/d/git/Git/cmd:/mnt/d/nodejs/:/mnt/d/instantclient/instantclient_12_2:/mnt/d/go/bin:/mnt/c/Program Files/dotnet/:/mnt/d/putty/:/mnt/d/ruby/Ruby27-x64/bin:/mnt/d/ruby/Ruby27-x64/bin:/mnt/d/ruby/Ruby30/bin:/mnt/c/Users/jalchu/AppData/Local/Microsoft/WindowsApps:/mnt/d/vscode/Microsoft VS Code/bin:/mnt/c/Users/jalchu/AppData/Roaming/npm:/mnt/d/bandizip/:/mnt/c/Users/jalchu/go/bin:/mnt/c/Users/jalchu/.dotnet/tools' && export LANG=C.UTF-8 && export RM_INFO=RM-203.8084.28 && export TEAMCITY_RAKE_RUNNER_MODE=idea && export TEAMCITY_RAKE_TU_TESTRUNNERMADIATOR_PATH=/usr/share/rvm/rubies/ruby-2.7.2/lib/ruby/gems/2.7.0/gems/test-unit-3.3.4/lib/test/unit/ui/testrunnermediator.rb && export SPRING_SERVER_COMMAND='spring server' && cd /mnt/d/pycharm/workspace/rms/rms-rubyend/jiezhang-back-open && /usr/share/rvm/rubies/ruby-2.7.2/bin/ruby /home/jalchu/.gem/ruby/2.7.0/gems/ruby-debug-ide-2.3.1/bin/rdebug-ide --key-value --step-over-in-blocks --disable-int-handler --evaluation-timeout 10 --evaluation-control --time-limit 100 --memory-limit 0 --rubymine-protocol-extensions --port 64020 --host 0.0.0.0 --dispatcher-port 64021 -- /mnt/d/pycharm/workspace/rms/rms-rubyend/jiezhang-back-open/bin/rails server -b 0.0.0.0 -p 3000 -e development"
Fast Debugger (ruby-debug-ide 2.3.1, debase 2.3.2, file filtering is supported, block breakpoints supported, smart steps supported, obtaining return values supported, partial obtaining of instance variables supported) listens on 0.0.0.0:64020
=> Booting WEBrick
=> Rails 5.1.7 application starting in development on http://0.0.0.0:3000
=> Run `rails server -h` for more startup options
config.eager_load is set to nil. Please update your config/environments/*.rb files accordingly:

  * development - set it to false
  * test - set it to false (unless you use a tool that preloads your test environment)
  * production - set it to true

[2021-11-03 04:37:02] INFO  WEBrick 1.6.0
[2021-11-03 04:37:02] INFO  ruby 2.7.2 (2020-10-01) [x86_64-linux]
[2021-11-03 04:37:02] INFO  WEBrick::HTTPServer#start: pid=1272 port=3000
```


参考：
1.https://cloud.tencent.com/developer/article/1354726 （配置WSL-dev mode和开启WSL）
2.https://rvm.io/rvm/install
3.https://rvm.io/rvm/security  （替换gpg host）
4.https://github.com/rvm/ubuntu_rvm
5.https://rvm.io/rubies/installing （降低版本到2.7.2）

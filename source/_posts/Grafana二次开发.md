---
title: Grafana二次开发
date: 2022-03-16 14:34:43
tags: grafana
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# Grafana二次开发流程记录

1. Grafana Github地址： https://github.com/grafana/grafana
2. git clone https://github.com/grafana/grafana.git
3. git checkout 23956557d8
4. 详细Dev文档地址: https://github.com/grafana/grafana/blob/main/contribute/developer-guide.md

# 前端Frontend

前端依赖yarn，23956557d8 branch需要yarn3.1.1，如果使用其他版本有可能在执行yarn start时候卡在94%（同事有遇到过）。

> $> yarn set version <version>

1. yarn install --immutable
2. yarn start

设置yarn版本参考： https://yarnpkg.com/cli/set/version

# 后端Backend

后端以windows为例，依赖go，gcc(下载TDM-GCC即可)，wire，另外这些安装好后，需要将他们配置到环境变量，方便使用

```
# 系统变量
GOPATH：D:\go\path
GOROOT：D:\go
```

```
# PATH
D:\gcc\bin
C:\Users\jalchu\go\bin
```

TDM-GCC下载地址： https://github.com/jmeubank/tdm-gcc/releases/download/v10.3.0-tdm64-2/tdm64-gcc-10.3.0-2.exe

wire Github地址： https://github.com/google/wire#installing

安装wire: 

> go install github.com/google/wire/cmd/wire@latest

将 $GOPATH/bin/wire.exe 添加到PATH方便下面使用

```
# 执行
cd grafana
wire.exe gen -tags oss ./pkg/server ./pkg/cmd/grafana-cli/runner
go run build.go build
```

运行结束后会在 grafana根目录下生成 /bin/windows-amd64 文件夹，该文件夹下有grafana-server.exe可执行文件，通过以下命令启动即可

> D:\webstorm\workspace\grafana\bin\windows-amd64> .\grafana-server.exe -homepath="D:\webstorm\workspace\grafana"

# 测试

浏览器访问 localhost:3000

登录默认账号和密码： admin/admin

修改前端登录表单，看浏览器是否有变化

D:\webstorm\workspace\grafana\public\app\core\components\Login\LoginForm.tsx   

modify some label to see the login page changes.


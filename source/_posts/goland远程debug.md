---
title: goland远程debug
date: 2024-01-02 14:34:43
tags:
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

1.Install golang

Because my local machine golang version is 1.20.6, so I will install the same version on Alma8 Linux

> mkdir -p /home/jalchu/golang
> wget https://go.dev/dl/go1.20.6.linux-amd64.tar.gz
> tar -zxvf go1.20.6.linux-amd64.tar.gz
> vi ~/.bashrc    (set environment like below)
> source ~/.bashrc
> go version
> touch hello.go  (a go test file)
> go run -x hello.go
> go build -x hello.go
> GOTMPDIR=/home/jalchu go run -x hello.go  (will set GOTMPDIR to env)


2.Install dlv

> git clone https://github.com/go-delve/delve
> cd delve
> go install github.com/go-delve/delve/cmd/dlv
> go install github.com/go-delve/delve/cmd/dlv@latest   (go version > 1.16)
> echo $GOPATH
> ll /home/jalchu/golang/path/bin/dlv    (dlv command is under $GOPATH)
> vi ~/.bashrc  (export PATH=$PATH:/home/jalchu/golang/go/bin:/home/jalchu/golang/path/bin)


3.Start debug

> git clone your code
> go build -gcflags "all=-N -l" /home/jalchu/gotest/goflow/cmd/netflow/netflow.go
> dlv --listen=:2345 --headless=true --api-version=2 --accept-multiclient exec ./netflow
> dlv debug cmd/netflow/netflow.go --headless --listen=:9900 --api-version=2    (same as above 2 commands)
> service iptables status
> iptables -L -n
> vi /etc/sysconfig/iptables
> iptables -A INPUT -p tcp --dport 2345 -j ACCEPT
> iptables -A OUTPUT -p tcp --sport 2345 -j ACCEPT
> iptables -L -n | grep 2345
> service iptables stop
> code sync to linux server with GoLand Deployment
> telnet ip port to judge whether you can connect to the linux server port
> set remote debug from GoLand
> export your env variables on linux server before you run the dlv, so that you can get the initial variable from your code





Reference

> https://linux.how2shout.com/how-to-install-go-language-on-almalinux-or-rocky-linux-8-9/
> https://almalinux.org/blog/almalinux-88-now-available/
> https://groups.google.com/g/golang-nuts/c/4bJez8NPfXM?pli=1
> https://github.com/go-delve/delve
> https://golangforall.com/en/post/go-docker-delve-remote-debug.html







Issues:
Linux Alma8 go build(run) error：

[root@iany1zces0012 golang]# go build hello.go
go: error obtaining buildID for go tool compile: fork/exec /home/jalchu/golang/go/pkg/tool/linux_amd64/compile: operation not permitted


Fix：

1. vi /etc/fapolicyd/rules.d/80-allow-go.rules

allow perm=execute all : ftype=application/x-executable trust=0 exe=/opt/golang/go/bin/go

2. systemctl restart fapolicyd

3.vi ~/.bashrc

export GOROOT=/home/jalchu/golang/go
export GOPATH=/home/jalchu/golang/path
export GOTMPDIR=/home/jalchu/golang/tmp
export PATH=$PATH:/home/jalchu/golang/go/bin

go run默认会在tmp下执行 tmp目录没有执行权限 需要指定GOTMPDIR

4.hello.go

package main

import "fmt"

func main(){
fmt.Printf("Hello world")
}





export GOROOT=/home/jalchu/golang/go
export GOPATH=/home/jalchu/golang/path
export GOTMPDIR=/home/jalchu/golang/tmp
export PATH=$PATH:/home/jalchu/golang/go/bin:/home/jalchu/golang/path/bin


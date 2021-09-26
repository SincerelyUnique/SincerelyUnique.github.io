---
title: centos docker简单操作
date: 2021-07-29 01:24:39
tags:
- linux
- docker
categories:
---

# centos docker简单操作

## 安装（以官网https://docs.docker.com/engine/install/centos/为主）

```shell script
[root@master jalchu]# sudo yum install -y yum-utils
[root@master jalchu]# sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
[root@master jalchu]# sudo yum-config-manager --enable docker-ce-nightly
[root@master jalchu]# sudo yum-config-manager --enable docker-ce-test
[root@master jalchu]# sudo yum install docker-ce docker-ce-cli containerd.io
[root@master jalchu]# sudo systemctl enable docker
[root@master jalchu]# sudo systemctl start docker
[root@master jalchu]# sudo docker run hello-world
```

## dockers安装mysql，运行mysql，进入mysql容器

```shell script
[root@master docker]# docker pull mysql:5.6
[root@master docker]# docker run -p 3306:3306 --name mymysql -v /home/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.6
[root@master docker]# docker ps
CONTAINER ID   IMAGE       COMMAND                  CREATED         STATUS         PORTS                                       NAMES
13d593602133   mysql:5.6   "docker-entrypoint.s…"   5 minutes ago   Up 5 minutes   0.0.0.0:3306->3306/tcp, :::3306->3306/tcp   mymysql
[root@master docker]# docker exec -it 13d593602133 /bin/bash
root@13d593602133:/# mysql -uroot -p
root@13d593602133:/# exit
[root@master docker]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
mysql         5.6       eb0e825dc3cf   6 days ago     303MB
hello-world   latest    d1165f221234   4 months ago   13.3kB
```

## 查看容器日志

```shell script
[root@master docker]# docker ps
CONTAINER ID   IMAGE       COMMAND                  CREATED          STATUS          PORTS                                       NAMES
13d593602133   mysql:5.6   "docker-entrypoint.s…"   26 minutes ago   Up 26 minutes   0.0.0.0:3306->3306/tcp, :::3306->3306/tcp   mymysql
[root@master docker]# docker logs -f 13d593602133
```

## 登录仓库

```shell script
[root@master docker]# docker login
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: chumingcheng
Password: 
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store
Login Succeeded
```

## 镜像构建（https://hub.docker.com/repositories）

```shell script
[root@master k8s]# git clone https://github.com/cloudnativedevops/demo.git
[root@master k8s]# cd demo-master/hello
[root@master hello]# ls
Dockerfile  go.mod  main.go  README.md
[root@master hello]# cat Dockerfile 
FROM golang:1.14-alpine AS build
 
WORKDIR /src/
COPY main.go go.* /src/
RUN CGO_ENABLED=0 go build -o /bin/demo
 
FROM scratch
COPY --from=build /bin/demo /bin/demo
ENTRYPOINT ["/bin/demo"]
[root@master hello]# docker image build -t myhello .
Sending build context to Docker daemon  11.78kB
Step 1/7 : FROM golang:1.14-alpine AS build
1.14-alpine: Pulling from library/golang
4c0d98bf9879: Pull complete 
9e181322f1e7: Pull complete 
6422294da7d3: Pull complete 
eb57f1833670: Pull complete 
bd57f1a80d4e: Pull complete 
Digest: sha256:e196a762bd349b188d66c539354fc23f0e5a0178c214e6dfe4d7894c3c2753f1
Status: Downloaded newer image for golang:1.14-alpine
 ---> 32dc91e030ac
Step 2/7 : WORKDIR /src/
 ---> Running in c8068f810890
Removing intermediate container c8068f810890
 ---> c738c5e990ae
Step 3/7 : COPY main.go go.* /src/
 ---> a35e7d921196
Step 4/7 : RUN CGO_ENABLED=0 go build -o /bin/demo
 ---> Running in 5fcecf11a2db
Removing intermediate container 5fcecf11a2db
 ---> ad1e2da24557
Step 5/7 : FROM scratch
 ---> 
Step 6/7 : COPY --from=build /bin/demo /bin/demo
 ---> 8630928238d7
Step 7/7 : ENTRYPOINT ["/bin/demo"]
 ---> Running in 51620f8f05af
Removing intermediate container 51620f8f05af
 ---> dea3c5956e88
Successfully built dea3c5956e88
Successfully tagged myhello:latest
[root@master hello]# ls
Dockerfile  go.mod  main.go  README.md
[root@master hello]# docker images
REPOSITORY   TAG           IMAGE ID       CREATED          SIZE
myhello      latest        dea3c5956e88   19 seconds ago   7.44MB
<none>       <none>        ad1e2da24557   19 seconds ago   303MB
mysql        5.6           eb0e825dc3cf   6 days ago       303MB
golang       1.14-alpine   32dc91e030ac   5 months ago     287MB
[root@master hello]# docker ps -a -q
[root@master hello]# docker container run -p 9999:8888 myhello
^C[root@master hello]# 
[root@master hello]# docker image tag myhello chumingcheng/myhello
[root@master hello]# docker images
REPOSITORY             TAG           IMAGE ID       CREATED         SIZE
chumingcheng/myhello   latest        dea3c5956e88   2 minutes ago   7.44MB
myhello                latest        dea3c5956e88   2 minutes ago   7.44MB
<none>                 <none>        ad1e2da24557   2 minutes ago   303MB
mysql                  5.6           eb0e825dc3cf   6 days ago      303MB
golang                 1.14-alpine   32dc91e030ac   5 months ago    287MB
[root@master hello]# docker image push chumingcheng/myhello
Using default tag: latest
The push refers to repository [docker.io/chumingcheng/myhello]
5b3a79de5427: Pushed 
latest: digest: sha256:9e8276428ae126ee3c5a0742b11b4a2f42a01e1c4ccec9f0fae2b07eab62c731 size: 528
[root@master hello]# 
```

## docker删除images

```shell script
[root@master docker]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
mysql         5.6       eb0e825dc3cf   6 days ago     303MB
hello-world   latest    d1165f221234   4 months ago   13.3kB
[root@master docker]# docker rmi hello-world
Error response from daemon: conflict: unable to remove repository reference "hello-world" (must force) - container 8b16c436d2bf is using its referenced image d1165f221234
[root@master docker]# docker image rm hello-world
Error response from daemon: conflict: unable to remove repository reference "hello-world" (must force) - container 8b16c436d2bf is using its referenced image d1165f221234
[root@master docker]# docker ps -a -q
13d593602133
c5af3da47da0
8b16c436d2bf
[root@master docker]# docker stop 8b16c436d2bf
8b16c436d2bf
[root@master docker]# docker rm 8b16c436d2bf
8b16c436d2bf
[root@master docker]# docker ps -a -q
13d593602133
c5af3da47da0
[root@master docker]# docker rmi hello-world
Error response from daemon: conflict: unable to remove repository reference "hello-world" (must force) - container c5af3da47da0 is using its referenced image d1165f221234
[root@master docker]# docker stop c5af3da47da0
c5af3da47da0
[root@master docker]# docker rm c5af3da47da0
c5af3da47da0
[root@master docker]# docker ps -a -q
13d593602133
[root@master docker]# docker rmi hello-world
Untagged: hello-world:latest
Untagged: hello-world@sha256:df5f5184104426b65967e016ff2ac0bfcd44ad7899ca3bbcf8e44e4461491a9e
Deleted: sha256:d1165f2212346b2bab48cb01c1e39ee8ad1be46b87873d9ca7a4e434980a7726
Deleted: sha256:f22b99068db93900abe17f7f5e09ec775c2826ecfe9db961fea68293744144bd
[root@master docker]# docker images
REPOSITORY   TAG       IMAGE ID       CREATED      SIZE
mysql        5.6       eb0e825dc3cf   6 days ago   303MB
[root@master docker]# 
```

## mysql数据备份，删除容器重启可恢复（验证）

```shell script
[root@master hello]# docker run -p 3306:3306 --name mymysql -v /home/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.6
1ee5505141519010431a3e2d9d64d1697e1eab88a78f318c60e3bdc2ef8d9b51
[root@master hello]# docker ps
CONTAINER ID   IMAGE       COMMAND                  CREATED          STATUS          PORTS                                       NAMES
1ee550514151   mysql:5.6   "docker-entrypoint.s…"   12 seconds ago   Up 11 seconds   0.0.0.0:3306->3306/tcp, :::3306->3306/tcp   mymysql
[root@master hello]# docker exec -it 1ee550514151 /bin/bash
root@1ee550514151:/# mysql -uroot -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 1
Server version: 5.6.51 MySQL Community Server (GPL)
 
Copyright (c) 2000, 2021, Oracle and/or its affiliates. All rights reserved.
 
Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.
 
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
 
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| jalen              |
| mysql              |
| performance_schema |
+--------------------+
4 rows in set (0.00 sec)
 
mysql> Ctrl-C -- exit!
Aborted
root@1ee550514151:/# create database grimmdb;
bash: create: command not found
root@1ee550514151:/# mysql -uroot -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 2
Server version: 5.6.51 MySQL Community Server (GPL)
 
Copyright (c) 2000, 2021, Oracle and/or its affiliates. All rights reserved.
 
Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.
 
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
 
mysql> create database grimmdb;
Query OK, 1 row affected (0.01 sec)
 
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| grimmdb            |
| jalen              |
| mysql              |
| performance_schema |
+--------------------+
5 rows in set (0.00 sec)
 
mysql> exit
Bye
root@1ee550514151:/# exit
exit
[root@master hello]# docker ps
CONTAINER ID   IMAGE       COMMAND                  CREATED         STATUS         PORTS                                       NAMES
1ee550514151   mysql:5.6   "docker-entrypoint.s…"   2 minutes ago   Up 2 minutes   0.0.0.0:3306->3306/tcp, :::3306->3306/tcp   mymysql
[root@master hello]# docker stop 1ee550514151
1ee550514151
[root@master hello]# docker ps -a
CONTAINER ID   IMAGE       COMMAND                  CREATED          STATUS                      PORTS     NAMES
1ee550514151   mysql:5.6   "docker-entrypoint.s…"   2 minutes ago    Exited (0) 6 seconds ago              mymysql
1494c41201d6   myhello     "/bin/demo"              14 minutes ago   Exited (2) 13 minutes ago             interesting_goldwasser
[root@master hello]# docker rm 1ee550514151
1ee550514151
[root@master hello]# docker ps -a
CONTAINER ID   IMAGE     COMMAND       CREATED          STATUS                      PORTS     NAMES
1494c41201d6   myhello   "/bin/demo"   14 minutes ago   Exited (2) 13 minutes ago             interesting_goldwasser
[root@master hello]# cd /home/mysql/data/
[root@master data]# ls
auto.cnf  grimmdb  ibdata1  ib_logfile0  ib_logfile1  jalen  mysql  performance_schema
[root@master data]# docker run -p 3306:3306 --name mymysql -v /home/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.6
8cc3dbaa0b79da8f3650e1665e85b59c13b0455c5deee2ab52599ae7add7fdc9
[root@master data]# docker ps
CONTAINER ID   IMAGE       COMMAND                  CREATED          STATUS          PORTS                                       NAMES
8cc3dbaa0b79   mysql:5.6   "docker-entrypoint.s…"   10 seconds ago   Up 10 seconds   0.0.0.0:3306->3306/tcp, :::3306->3306/tcp   mymysql
[root@master data]# docker exec -it 8cc3dbaa0b79 /bin/bash
root@8cc3dbaa0b79:/# mysql -uroot -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 2
Server version: 5.6.51 MySQL Community Server (GPL)
 
Copyright (c) 2000, 2021, Oracle and/or its affiliates. All rights reserved.
 
Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.
 
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
 
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| grimmdb            |
| jalen              |
| mysql              |
| performance_schema |
+--------------------+
5 rows in set (0.00 sec)
 
mysql> 
```

---
title: linux 批量删除某个前缀文件
date: 2020-05-04 09:48:05
tags:
- linux
categories:
---

1.命令 （参考：https://blog.csdn.net/kl28978113/article/details/80271831） 

> find ./ -name 'updatesites*-*' -exec rm {} \;

2.举例

```
[root@admin batch-create-sites]# ls
2020-02-13-10-10.out       logs-2020-04-07-08-00.out             updatesites-2020-02-12-01-49-25.xlsx  updatesites-2020-02-12-06-01-38.xlsx
command.txt                logs-2020-04-07-08-01.out             updatesites-2020-02-12-01-49-48.xlsx  updatesites-2020-02-12-06-05-12.xlsx
insert_sites.py            logs-2020-04-16-12-51.out             updatesites-2020-02-12-01-50-05.xlsx  updatesites-2020-02-12-06-13-54.xlsx
logs-2020-02-10-12-08.out  logs-2020-04-16-12-58.out             updatesites-2020-02-12-01-50-34.xlsx  updatesites-2020-02-12-06-13-56.xlsx
logs-2020-02-11-10-09.out  logs-2020-04-26-09-19.out             updatesites-2020-02-12-01-50-57.xlsx  updatesites-2020-02-12-06-16-27.xlsx
logs-2020-02-11-10-10.out  logs.out                              updatesites-2020-02-12-01-54-50.xlsx  updatesites-2020-02-12-06-16-32.xlsx
logs-2020-02-12-01-53.out  updatesites-2020-02-11-02-16-13.xlsx  updatesites-2020-02-12-02-54-34.xlsx  updatesites-2020-02-12-06-16-48.xlsx
logs-2020-02-12-02-04.out  updatesites-2020-02-11-02-16-24.xlsx  updatesites-2020-02-12-03-09-37.xlsx  updatesites-2020-02-26-08-37-02.xlsx
logs-2020-02-12-02-15.out  updatesites-2020-02-11-02-16-58.xlsx  updatesites-2020-02-12-03-16-06.xlsx  updatesites-2020-02-26-08-37-33.xlsx
logs-2020-02-12-03-38.out  updatesites-2020-02-11-02-18-07.xlsx  updatesites-2020-02-12-03-18-08.xlsx  updatesites-2020-04-09-12-25-08.xlsx
logs-2020-02-12-09-47.out  updatesites-2020-02-11-02-18-34.xlsx  updatesites-2020-02-12-05-54-37.xlsx  updatesites-2020-04-09-12-26-56.xlsx
logs-2020-02-12-10-52.out  updatesites-2020-02-11-03-44-41.xlsx  updatesites-2020-02-12-05-55-23.xlsx  updatesites-2020-04-09-12-27-01.xlsx
logs-2020-02-13-10-47.out  updatesites-2020-02-11-03-56-01.xlsx  updatesites-2020-02-12-05-58-29.xlsx  updatesites-2020-04-09-12-49-21.xlsx
logs-2020-02-14-17-04.out  updatesites-2020-02-11-13-30-08.xlsx  updatesites-2020-02-12-05-59-04.xlsx  updatesites-2020-04-09-12-53-38.xlsx
logs-2020-03-03-05-54.out  updatesites-2020-02-12-01-48-39.xlsx  updatesites-2020-02-12-05-59-50.xlsx  updatesites-2020-04-09-12-54-10.xlsx
logs-2020-04-07-07-37.out  updatesites-2020-02-12-01-48-50.xlsx  updatesites-2020-02-12-06-00-31.xlsx  updatesites-2020-04-16-12-46-04.xlsx
logs-2020-04-07-07-43      updatesites-2020-02-12-01-49-03.xlsx  updatesites-2020-02-12-06-01-36.xlsx
[root@admin batch-create-sites]# find ./ -name 'updatesites*-*' -exec rm {} \;
[root@admin batch-create-sites]# ls
2020-02-13-10-10.out       logs-2020-02-11-10-09.out  logs-2020-02-12-02-15.out  logs-2020-02-13-10-47.out  logs-2020-04-07-07-43      logs-2020-04-16-12-58.out
command.txt                logs-2020-02-11-10-10.out  logs-2020-02-12-03-38.out  logs-2020-02-14-17-04.out  logs-2020-04-07-08-00.out  logs-2020-04-26-09-19.out
insert_sites.py            logs-2020-02-12-01-53.out  logs-2020-02-12-09-47.out  logs-2020-03-03-05-54.out  logs-2020-04-07-08-01.out  logs.out
logs-2020-02-10-12-08.out  logs-2020-02-12-02-04.out  logs-2020-02-12-10-52.out  logs-2020-04-07-07-37.out  logs-2020-04-16-12-51.out
```

尝试删除前可以先备份一下，cp batch-create-sites/* bak/） 

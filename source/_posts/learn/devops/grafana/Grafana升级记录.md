---
title: Grafana升级
date: 2022-12-08 14:34:43
tags: grafana
categories:
- [学习, DevOPS, Grafana]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 升级原因

安全问题，3个security bug

# 波及版本和升级版本
```
Affected Versions:
Grafana 9.x prior to 9.2.4
Grafana 8.x prior to 8.5.15
```

# 官方解释

Grafana Security Advisory

Grafana has released patch to address the vulnerability. For more information please refer to：

https://grafana.com/blog/2022/11/08/security-release-new-versions-of-grafana-with-critical-and-moderate-fixes-for-cve-2022-39328-cve-2022-39307-and-cve-2022-39306/


# 升级步骤

参考： 

https://grafana.com/docs/grafana/latest/setup-grafana/upgrade-grafana/

https://grafana.com/grafana/download/9.2.4

https://raghavendar-d.medium.com/upgrade-grafana-to-letest-version-5a17c37b4c18

老版本： v8.4.4

备份：

```bash
# 备份grafana db和plugins
[root@xxx-sj-job-3 jalchu]# tar -zcvf /users/jalchu/varlibgrafana.tar.gz /var/lib/grafana
# 备份grafana 配置
[root@xxx-sj-job-3 jalchu]# tar -zcvf /users/jalchu/etcgrafana.tar.gz /etc/grafana
# 停止服务
[root@xxx-sj-job-3 jalchu]# systemctl stop grafana-server
```

```bash
# 获取老版本备用，方便rollback
[root@xxx-sj-job-3 jalchu]# wget https://dl.grafana.com/enterprise/release/grafana-enterprise-8.4.4-1.x86_64.rpm
[root@xxx-sj-job-3 jalchu]# sudo yum install grafana-enterprise-8.4.4-1.x86_64.rpm 

# 获取推荐升级的最新版本9
[root@xxx-sj-job-3 jalchu]# wget https://dl.grafana.com/enterprise/release/grafana-enterprise-9.2.4-1.x86_64.rpm
[root@xxx-sj-job-3 jalchu]# sudo yum install grafana-enterprise-9.2.4-1.x86_64.rpm

# 获取推荐升级的最新版本8
[root@xxx-sj-job-3 jalchu]# wget https://dl.grafana.com/enterprise/release/grafana-enterprise-8.5.15-1.x86_64.rpm
[root@xxx-sj-job-3 jalchu]# sudo yum install grafana-enterprise-8.5.15-1.x86_64.rpm
```

```bash
# 升级9版本后发现不支持es7，版本rollback
[root@xxx-sj-job-3 jalchu]# rpm -qa|grep grafana
[root@xxx-sj-job-3 jalchu]# rpm -e grafana-enterprise-9.2.4-1.x86_64
[root@xxx-sj-job-3 jalchu]# sudo yum install grafana-enterprise-8.4.4-1.x86_64.rpm 
[root@xxx-sj-job-3 jalchu]# systemctl status grafana-server
[root@xxx-sj-job-3 jalchu]# sudo yum install grafana-enterprise-8.5.15-1.x86_64.rpm
[root@xxx-sj-job-3 jalchu]# sudo yum install grafana-enterprise-8.5.15-1.x86_64.rpm
[root@xxx-sj-job-3 jalchu]# systemctl restart grafana-server
```


升级后发现es的密码都无效了，需更新下密码

长期计划，升级es，参考

https://www.elastic.co/guide/en/elasticsearch/reference/current/setup-upgrade.html

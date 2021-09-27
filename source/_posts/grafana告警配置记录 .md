---
title: Grafana告警配置记录
date: 2020-10-28 15:06:51
tags:
- grafana
categories:
---

```
1. 设置SMTP
邮件告警需要设置smtp，smtp配置查看需要admin权限：点击左侧Server Admin，选择Settings，
ctrl+F搜索smtp查看具体信息。
修改smtp配置需要到linux服务器修改grafana.ini配置文件中[smtp]节点，大致如下：
 
[smtp]
enabled = true
host = xxx.xxx.com:port
user = xxxxxx@qq.com  （注意特殊字符用三引号括起来，如#）
password =
skip_verify = true
from_address = grafana@xxx.com
from_name = Grafana
 
 
 
2. 添加Alerting
左侧边栏选择Alerting，选择Notification channels，添加New channel。
Name自定义，Type选择email，勾选Default（所有alert都通过email发送），勾选Include image，
保证同时发送告警图片。
发送告警图片需要安装插件：
参考：https://grafana.com/docs/grafana/latest/administration/image_rendering/
 
安装图形渲染插件
参考：https://grafana.com/grafana/plugins/grafana-image-renderer/installation
 
[root@xxx jalchu]# whereis grafana-cli
grafana-cli: /usr/sbin/grafana-cli
[root@xxx jalchu]# /usr/sbin/grafana-cli plugins install grafana-image-renderer
installing grafana-image-renderer @ 2.0.0
from: https://grafana.com/api/plugins/grafana-image-renderer/versions/2.0.0/download
into: /var/lib/grafana/plugins
 
Installed grafana-image-renderer successfully 
 
Restart grafana after installing plugins . <service grafana-server restart>
 
[root@xxx jalchu]# service grafana-server restart
 
插件相关配置
（1）设置通知图片的临时过期时间（grafana.ini）
[root@xxx jalchu]# vi /etc/grafana/grafana.ini
修改
# Temporary files in `data` directory older than given duration will be removed
;temp_data_lifetime = 24h
说明：
也可以通过点击panel上方箭头，选择Share-Direct link rendered image来渲染图片。
（2）超载配置（grafana.ini）
渲染图片需要很大内存，因为如果你有很多dashboard或很多panel，这里涉及到并行渲染，
所以官方建议最小内存为16G。
对于同时需要渲染多个图片，grafana内部可通过concurrent_render_limit参数配置，防止超载。
# Alert notifications can include images, but rendering many images at the same time can overload the server
# This limit will protect the server from render overloading and make sure notifications are sent out quickly
;concurrent_render_limit = 5
 
 
 
3. 添加Alert
打开具体的dashboard，选择一个panel，点edit进入编辑页面。
找到Alert，添加Alert规则：
Rule：评估时间，比如每5分钟评估一次，瞧瞧在这5分钟内是否存在告警
Conditions：评估条件，比如5分钟内这5个点的平均值超过多少，或者最小值低于多少
No Data & Error Handling：如果没有数据或者所有数据都是null，或者发生了异常或超时
Notifications：默认会显示刚刚配置好的Notification channel，并输入该alert的邮件内容说明
 
 
 
 
附一个未解决的问题：
以上安装方式在我们prod环境尝试时，
按照官网安装后重启，发现创建channel勾选Include image时任然显示：
No image renderer available/installed
参考：https://github.com/grafana/grafana-image-renderer/issues/165
尝试执行ldd /var/lib/grafana/plugins/grafana-image-renderer/chrome-linux/chrome后重启，仍然报
No image renderer available/installed
 
尝试修改log的filters配置：filters = oauth.generic_oauth:debug
改为filters = rendering:debug，然后查询log，发现log里直接报
No image renderer found/installed
 
尝试去/var/lib/grafana/plugins/grafana-image-renderer目录下执行make & make install，执行失败
 
这个目前在linux上尚未解决
```

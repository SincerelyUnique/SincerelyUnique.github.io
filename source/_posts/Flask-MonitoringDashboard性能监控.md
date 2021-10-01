---
title: Flask-MonitoringDashboard性能监控
date: 2019-10-22 16:56:53
tags:
- flask
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

1.安装

> pip install flask_monitoringdashboard

2.引入

```python
import flask_monitoringdashboard as dashboard
dashboard.config.init_from(file='config.cfg')
dashboard.bind(app)
```

3.项目根目录下创建配置文件config.cfg

```
[dashboard]
APP_VERSION=1.0
GIT=../.git/                                      # 我的git在上一级目录
CUSTOM_LINK=dashboard
MONITOR_LEVEL=3
OUTLIER_DETECTION_CONSTANT=2.5
SAMPLING_PERIOD=20
ENABLE_LOGGING=True
 
[authentication]
USERNAME=admin
PASSWORD=admin
GUEST_USERNAME=guest
GUEST_PASSWORD=['dashboardguest!', 'second_pw!']
SECURITY_TOKEN=cc83733cb0af8b884ff6577086b87909
 
[database]
TABLE_PREFIX=fmd
DATABASE=sqlite:///dashboard.db
 
[visualization]
TIMEZONE=Europe/Amsterdam
COLORS={'main':'[0,97,255]',
        'static':'[255,153,0]'}
```

4.启动flask server，使用admin账号登录

> http://127.0.0.1:8080/dashboard

5.参考

https://flask-monitoringdashboard.readthedocs.io/en/latest/installation.html

https://github.com/flask-dashboard/Flask-MonitoringDashboard

6.遗憾的是没有找到在哪里可以监控sqlalchemy执行状况（不是指开启sqlalchemy慢日志）

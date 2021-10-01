---
title: Flask-APScheduler API endpoints
date: 2020-01-09 15:34:31
tags:
- python
- flask
- apscheduler
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

Flask-APScheduler 直接提供API，只要在配置文件里面配置SCHEDULER_API_ENABLED =True 就行

API 如下

```python
self._add_url_route('get_scheduler_info', '', api.get_scheduler_info, 'GET')
 
self._add_url_route('add_job', '/jobs', api.add_job, 'POST')
 
self._add_url_route('get_job', '/jobs/<job_id>', api.get_job, 'GET')
 
self._add_url_route('get_jobs', '/jobs', api.get_jobs, 'GET')
 
self._add_url_route('delete_job', '/jobs/<job_id>', api.delete_job, 'DELETE')
 
self._add_url_route('update_job', '/jobs/<job_id>', api.update_job, 'PATCH')
 
self._add_url_route('pause_job', '/jobs/<job_id>/pause', api.pause_job, 'POST')
 
self._add_url_route('resume_job', '/jobs/<job_id>/resume', api.resume_job, 'POST')
 
self._add_url_route('run_job', '/jobs/<job_id>/run', api.run_job, 'POST')
```

需要加上默认的前缀 /schedule，当然修改也是可以的，配置文件里面配置SCHEDULER_API_PREFIX ='/your_path' 就行，如

```shell
http://xxx.xxx.xxx:8080/scheduler/jobs/<job_id>/pause
http://xxx.xxx.xxx:8080/your_path/jobs/<job_id>/pause
```

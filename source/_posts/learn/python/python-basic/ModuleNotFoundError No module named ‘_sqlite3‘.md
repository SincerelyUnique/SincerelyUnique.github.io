---
title: ModuleNotFoundError No module named ‘_sqlite3‘
date: 2021-01-04 14:13:37
tags:
- python
categories:
- [学习, Python语言学习, Python]
---

```
[2021-01-04 05:24:00 +0000] [13884] [ERROR] Exception in worker process
Traceback (most recent call last):
  File "/opt/venv3/lib/python3.8/site-packages/gunicorn/arbiter.py", line 583, in spawn_worker
    worker.init_process()
  File "/opt/venv3/lib/python3.8/site-packages/gunicorn/workers/ggevent.py", line 162, in init_process
    super().init_process()
  File "/opt/venv3/lib/python3.8/site-packages/gunicorn/workers/base.py", line 119, in init_process
    self.load_wsgi()
  File "/opt/venv3/lib/python3.8/site-packages/gunicorn/workers/base.py", line 144, in load_wsgi
    self.wsgi = self.app.wsgi()
  File "/opt/venv3/lib/python3.8/site-packages/gunicorn/app/base.py", line 67, in wsgi
    self.callable = self.load()
  File "/opt/venv3/lib/python3.8/site-packages/gunicorn/app/wsgiapp.py", line 49, in load
    return self.load_wsgiapp()
  File "/opt/venv3/lib/python3.8/site-packages/gunicorn/app/wsgiapp.py", line 39, in load_wsgiapp
    return util.import_app(self.app_uri)
  File "/opt/venv3/lib/python3.8/site-packages/gunicorn/util.py", line 358, in import_app
    mod = importlib.import_module(module)
  File "/opt/venv3/lib/python3.8/importlib/__init__.py", line 127, in import_module
    return _bootstrap._gcd_import(name[level:], package, level)
  File "<frozen importlib._bootstrap>", line 1014, in _gcd_import
  File "<frozen importlib._bootstrap>", line 991, in _find_and_load
  File "<frozen importlib._bootstrap>", line 975, in _find_and_load_unlocked
  File "<frozen importlib._bootstrap>", line 671, in _load_unlocked
  File "<frozen importlib._bootstrap_external>", line 783, in exec_module
  File "<frozen importlib._bootstrap>", line 219, in _call_with_frames_removed
  File "/opt/provision/manage.py", line 9, in <module>
    app = create_app(os.getenv('PROVISION_CONFIG') or os.path.join(basedir, "config.py"))
  File "/opt/provision/dashboard/__init__.py", line 100, in create_app
    from .knowledgebase import kbase as kb_blueprint
  File "/opt/provision/dashboard/knowledgebase/__init__.py", line 4, in <module>
    from . import views
  File "/opt/provision/dashboard/knowledgebase/views.py", line 6, in <module>
    from dashboard.biz.kbase import kbasebiz
  File "/opt/provision/dashboard/biz/kbase/kbasebiz.py", line 4, in <module>
    from nltk.corpus import stopwords
  File "/opt/venv3/lib/python3.8/site-packages/nltk/__init__.py", line 149, in <module>
    from nltk.translate import *
  File "/opt/venv3/lib/python3.8/site-packages/nltk/translate/__init__.py", line 23, in <module>
    from nltk.translate.meteor_score import meteor_score as meteor
  File "/opt/venv3/lib/python3.8/site-packages/nltk/translate/meteor_score.py", line 10, in <module>
    from nltk.stem.porter import PorterStemmer
  File "/opt/venv3/lib/python3.8/site-packages/nltk/stem/__init__.py", line 29, in <module>
    from nltk.stem.snowball import SnowballStemmer
  File "/opt/venv3/lib/python3.8/site-packages/nltk/stem/snowball.py", line 29, in <module>
    from nltk.corpus import stopwords
  File "/opt/venv3/lib/python3.8/site-packages/nltk/corpus/__init__.py", line 66, in <module>
    from nltk.corpus.reader import *
  File "/opt/venv3/lib/python3.8/site-packages/nltk/corpus/reader/__init__.py", line 105, in <module>
    from nltk.corpus.reader.panlex_lite import *
  File "/opt/venv3/lib/python3.8/site-packages/nltk/corpus/reader/panlex_lite.py", line 15, in <module>
    import sqlite3
  File "/usr/local/lib/python3.8/sqlite3/__init__.py", line 23, in <module>
    from sqlite3.dbapi2 import *
  File "/usr/local/lib/python3.8/sqlite3/dbapi2.py", line 27, in <module>
    from _sqlite3 import *
ModuleNotFoundError: No module named '_sqlite3'
[2021-01-04 05:24:00 +0000] [13884] [INFO] Worker exiting (pid: 13884)
```

[参考](https://stackoverflow.com/questions/51081994/while-installing-nltk-package-getting-modulenotfounderror-no-module-named-sql)

```shell script
[root@jalen ~]# yum install sqlite-devel
 
[root@jalen ~]# cd Python-3.8.6
[root@jalen ~]# sudo ./configure --enable-optimizations 
[root@jalen ~]# sudo make altinstall
 
[root@jalen ~]# virtualenv -p /usr/local/bin/python3.8 venv3
[root@jalen ~]# source ./venv3/bin/activate
[root@jalen ~]# pip install -r provision/requirements.txt
```

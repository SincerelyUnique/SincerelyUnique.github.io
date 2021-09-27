---
title: Failed to build gevent Could not build wheels for gevent which use PEP 517 ... ...
date: 2021-01-04 18:14:18
tags:
- python
categories:
---

```
(venv) [root@jalen opt]# pip install -r sfs/requirements.txt
  ... ...
  ... ...
  ... ...
  Building wheel for gevent (PEP 517) ... error
  ERROR: Command errored out with exit status 1:
   command: /opt/venv/bin/python3 /opt/venv/lib/python3.6/site-packages/pip/_vendor/pep517/_in_process.py build_wheel /tmp/tmpo99kgt3g
       cwd: /tmp/pip-install-omlwwepw/gevent_c4622d7b40544f0cbd97eb7ee7291aaa
  Complete output (300 lines):
  running bdist_wheel
  running build
  running build_py
  creating build
  creating build/lib.linux-x86_64-3.6
  creating build/lib.linux-x86_64-3.6/gevent
  copying src/gevent/__init__.py -> build/lib.linux-x86_64-3.6/gevent
  copying src/gevent/_abstract_linkable.py -> build/lib.linux-x86_64-3.6/gevent
  copying src/gevent/_compat.py -> build/lib.linux-x86_64-3.6/gevent
  ... ...
  ... ...
  copying src/gevent/tests/server.key -> build/lib.linux-x86_64-3.6/gevent/tests
  copying src/gevent/tests/test_server.key -> build/lib.linux-x86_64-3.6/gevent/tests
  running build_ext
  generating cffi module 'build/temp.linux-x86_64-3.6/gevent.libuv._corecffi.c'
  creating build/temp.linux-x86_64-3.6
  Running '(cd  "/tmp/pip-install-omlwwepw/gevent_c4622d7b40544f0cbd97eb7ee7291aaa/deps/libev"  && sh ./configure -C > configure-output.txt )' in /tmp/pip-install-omlwwepw/gevent_c4622d7b40544f0cbd97eb7ee7291aaa
  generating cffi module 'build/temp.linux-x86_64-3.6/gevent.libev._corecffi.c'
  Not configuring libev, 'config.h' already exists
  Not configuring libev, 'config.h' already exists
  building 'gevent.libev.corecext' extension
  creating build/temp.linux-x86_64-3.6/src
  creating build/temp.linux-x86_64-3.6/src/gevent
  creating build/temp.linux-x86_64-3.6/src/gevent/libev
  gcc -pthread -Wno-unused-result -Wsign-compare -DNDEBUG -O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions -fstack-protector-strong --param=ssp-buffer-size=4 -grecord-gcc-switches -m64 -mtune=generic -D_GNU_SOURCE -fPIC -fwrapv -fPIC -DLIBEV_EMBED=1 -DEV_COMMON= -DEV_CLEANUP_ENABLE=0 -DEV_EMBED_ENABLE=0 -DEV_PERIODIC_ENABLE=0 -DEV_USE_REALTIME=1 -DEV_USE_MONOTONIC=1 -DEV_USE_FLOOR=1 -Isrc/gevent/libev -I/usr/include/python3.6m -I/opt/venv/include/python3.6m -I/tmp/pip-install-omlwwepw/gevent_c4622d7b40544f0cbd97eb7ee7291aaa/deps -I/tmp/pip-install-omlwwepw/gevent_c4622d7b40544f0cbd97eb7ee7291aaa/src/gevent/libev -I/tmp/pip-install-omlwwepw/gevent_c4622d7b40544f0cbd97eb7ee7291aaa/deps/libev -Isrc/gevent -Isrc/gevent/libev -Isrc/gevent/resolver -I. -I/usr/include/python3.6m -c src/gevent/libev/corecext.c -o build/temp.linux-x86_64-3.6/src/gevent/libev/corecext.o
  src/gevent/libev/corecext.c:91:20: fatal error: Python.h: No such file or directory
   #include "Python.h"
                      ^
  compilation terminated.
  error: command 'gcc' failed with exit status 1
  ----------------------------------------
  ERROR: Failed building wheel for gevent
  Building wheel for pyrsistent (setup.py) ... done
  Created wheel for pyrsistent: filename=pyrsistent-0.17.3-cp36-cp36m-linux_x86_64.whl size=55876 sha256=3007376edec8d870de841a49a5b7aa075810771df6ff8e3af9cb818d47673461
  Stored in directory: /root/.cache/pip/wheels/34/13/19/294da8e11bce7e563afee51251b9fa878185e14f4b5caf00cb
  Building wheel for PyYAML (setup.py) ... done
  Created wheel for PyYAML: filename=PyYAML-5.3.1-cp36-cp36m-linux_x86_64.whl size=44621 sha256=d9b322488a3b9f9e11abef107f4d64c2763cd0c4d8068bd51e35442908e7c661
  Stored in directory: /root/.cache/pip/wheels/e5/9d/ad/2ee53cf262cba1ffd8afe1487eef788ea3f260b7e6232a80fc
  Building wheel for flask-apscheduler (setup.py) ... done
  Created wheel for flask-apscheduler: filename=Flask_APScheduler-1.11.0-py3-none-any.whl size=18953 sha256=07d964c194edb64a882a385c4c36597584deb9427c175c6d12b864d031de4fed
  Stored in directory: /root/.cache/pip/wheels/b2/22/78/e72344262e38a3ddcef6d0fc0bff1a73760a18fa47f8f6a151
  Building wheel for visitor (setup.py) ... done
  Created wheel for visitor: filename=visitor-0.1.3-py3-none-any.whl size=3929 sha256=a495e29d2e65e489655370f29a9fb823d8a8b6e03755c5b6ecd2da428a7b4676
  Stored in directory: /root/.cache/pip/wheels/a9/77/93/3b8a22ac46f57a22d0fd7b4912fff740bcbddbd720bd48508e
Successfully built business-duration flask-compress itsdangerous nltk flask-bootstrap Flask-Script pyrsistent PyYAML flask-apscheduler visitor
Failed to build gevent
ERROR: Could not build wheels for gevent which use PEP 517 and cannot be installed directly
 
 
 
 
(venv) [root@jalen opt]# pip install gevent==20.6.2
 
 
 
 
(venv) [root@jalen opt]# pip list
Package              Version
-------------------- ----------
... ...
Flask                1.1.2
gevent               20.6.2
gunicorn             20.0.4
MarkupSafe           1.0
pip                  20.2.3
setuptools           45.2.0
srsly                1.0.5
Werkzeug             1.0.1
wheel                0.36.2
... ...
WARNING: You are using pip version 20.2.3; however, version 20.3.3 is available.
You should consider upgrading via the '/opt/venv/bin/python3 -m pip install --upgrade pip' command.
(venv) [root@jalen opt]# 
 
```

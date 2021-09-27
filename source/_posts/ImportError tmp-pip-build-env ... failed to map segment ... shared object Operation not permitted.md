---
title: ImportError tmp pip-build-env ... failed to map segment ... shared object Operation not permitted
date: 2021-01-04 18:02:48
tags:
- python
categories:
---

```
(venv) [root@jalen opt]# pip install -r sfs/requirements.txt
... ...
... ...
... ...
Collecting gevent>=1.2.2
  Using cached gevent-20.12.1.tar.gz (5.9 MB)
  Installing build dependencies ... done
  Getting requirements to build wheel ... done
    Preparing wheel metadata ... error
    ERROR: Command errored out with exit status 1:
     command: /opt/venv/bin/python3 /opt/venv/lib/python3.6/site-packages/pip/_vendor/pep517/_in_process.py prepare_metadata_for_build_wheel /tmp/tmp5scrieo3
         cwd: /tmp/pip-install-c1jwly1_/gevent_169db4aaf61b42bca39ccc50b43bbda5
    Complete output (40 lines):
    Traceback (most recent call last):
      File "/opt/venv/lib/python3.6/site-packages/pip/_vendor/pep517/_in_process.py", line 280, in <module>
        main()
      File "/opt/venv/lib/python3.6/site-packages/pip/_vendor/pep517/_in_process.py", line 263, in main
        json_out['return_val'] = hook(**hook_input['kwargs'])
      File "/opt/venv/lib/python3.6/site-packages/pip/_vendor/pep517/_in_process.py", line 133, in prepare_metadata_for_build_wheel
        return hook(metadata_directory, config_settings)
      File "/tmp/pip-build-env-7a7gi3xg/overlay/lib/python3.6/site-packages/setuptools/build_meta.py", line 161, in prepare_metadata_for_build_wheel
        self.run_setup()
      File "/tmp/pip-build-env-7a7gi3xg/overlay/lib/python3.6/site-packages/setuptools/build_meta.py", line 254, in run_setup
        self).run_setup(setup_script=setup_script)
      File "/tmp/pip-build-env-7a7gi3xg/overlay/lib/python3.6/site-packages/setuptools/build_meta.py", line 145, in run_setup
        exec(compile(code, __file__, 'exec'), locals())
      File "setup.py", line 478, in <module>
        run_setup(EXT_MODULES)
      File "setup.py", line 462, in run_setup
        "signal_os_incompat = gevent.monkey:_subscribe_signal_os",
      File "/tmp/pip-build-env-7a7gi3xg/overlay/lib/python3.6/site-packages/setuptools/__init__.py", line 153, in setup
        return distutils.core.setup(**attrs)
      File "/usr/lib64/python3.6/distutils/core.py", line 108, in setup
        _setup_distribution = dist = klass(attrs)
      File "/tmp/pip-build-env-7a7gi3xg/overlay/lib/python3.6/site-packages/setuptools/dist.py", line 424, in __init__
        k: v for k, v in attrs.items()
      File "/usr/lib64/python3.6/distutils/dist.py", line 281, in __init__
        self.finalize_options()
      File "/tmp/pip-build-env-7a7gi3xg/overlay/lib/python3.6/site-packages/setuptools/dist.py", line 695, in finalize_options
        ep(self)
      File "/tmp/pip-build-env-7a7gi3xg/overlay/lib/python3.6/site-packages/setuptools/dist.py", line 702, in _finalize_setup_keywords
        ep.load()(self, ep.name, value)
      File "/tmp/pip-build-env-7a7gi3xg/overlay/lib64/python3.6/site-packages/cffi/setuptools_ext.py", line 219, in cffi_modules
        add_cffi_module(dist, cffi_module)
      File "/tmp/pip-build-env-7a7gi3xg/overlay/lib64/python3.6/site-packages/cffi/setuptools_ext.py", line 49, in add_cffi_module
        execfile(build_file_name, mod_vars)
      File "/tmp/pip-build-env-7a7gi3xg/overlay/lib64/python3.6/site-packages/cffi/setuptools_ext.py", line 25, in execfile
        exec(code, glob, glob)
      File "src/gevent/libev/_corecffi_build.py", line 31, in <module>
        ffi = FFI()
      File "/tmp/pip-build-env-7a7gi3xg/overlay/lib64/python3.6/site-packages/cffi/api.py", line 48, in __init__
        import _cffi_backend as backend
    ImportError: /tmp/pip-build-env-7a7gi3xg/overlay/lib64/python3.6/site-packages/_cffi_backend.cpython-36m-x86_64-linux-gnu.so: failed to map segment from shared object: Operation not permitted
    ----------------------------------------
ERROR: Command errored out with exit status 1: /opt/venv/bin/python3 /opt/venv/lib/python3.6/site-packages/pip/_vendor/pep517/_in_process.py prepare_metadata_for_build_wheel /tmp/tmp5scrieo3 Check the logs for full command output.
 
 
(venv) [root@jalen opt]# sudo mount /tmp -o remount,exec
(venv) [root@jalen opt]# pip install -r sfs/requirements.txt
```

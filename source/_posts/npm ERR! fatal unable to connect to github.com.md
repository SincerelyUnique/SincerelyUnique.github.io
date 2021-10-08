---
title: npm ERR! fatal unable to connect to github.com
date: 2019-01-17 10:57:07
tags:
- npm
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# Error Info

```raw
C:\Users\jalchu\WebstormProjects\vue-element-admin>npm install
npm WARN deprecated bfj-node4@5.3.1: Switch to the `bfj` package for fixes and new features!
npm WARN deprecated nomnom@1.8.1: Package no longer supported. Contact support@npmjs.com for more info.
npm ERR! Error while executing:
npm ERR! C:\Program Files\Git\cmd\git.EXE ls-remote -h -t git://github.com/adobe-webplatform/eve.git
npm ERR!
npm ERR! fatal: unable to connect to github.com:
npm ERR! github.com[0: 192.30.255.112]: errno=No such file or directory
npm ERR! github.com[1: 192.30.255.113]: errno=No such file or directory
npm ERR!
npm ERR!
npm ERR! exited with error code: 128
 
npm ERR! A complete log of this run can be found in:
npm ERR!     C:\Users\jalchu\AppData\Roaming\npm-cache\_logs\2019-01-17T02_46_11_928Z-debug.log
```

# Solution

```raw
C:\Users\jalchu\WebstormProjects\vue-element-admin>git config --global url."https://".insteadOf git://
```

# Re Install

```raw
C:\Users\jalchu\WebstormProjects\vue-element-admin>npm install
npm WARN deprecated nomnom@1.8.1: Package no longer supported. Contact support@npmjs.com for more info.
npm WARN deprecated bfj-node4@5.3.1: Switch to the `bfj` package for fixes and new features!
 
> husky@0.14.3 install C:\Users\jalchu\WebstormProjects\vue-element-admin\node_modules\husky
> node ./bin/install.js
 
husky
setting up Git hooks
done
 
 
> node-sass@4.11.0 install C:\Users\jalchu\WebstormProjects\vue-element-admin\node_modules\node-sass
> node scripts/install.js
 
Downloading binary from https://github.com/sass/node-sass/releases/download/v4.11.0/win32-x64-64_binding.node
Download complete..] - :
Binary saved to C:\Users\jalchu\WebstormProjects\vue-element-admin\node_modules\node-sass\vendor\win32-x64-64\binding.node
Caching binary to C:\Users\jalchu\AppData\Roaming\npm-cache\node-sass\4.11.0\win32-x64-64_binding.node
 
> node-sass@4.11.0 postinstall C:\Users\jalchu\WebstormProjects\vue-element-admin\node_modules\node-sass
> node scripts/build.js
 
Binary found at C:\Users\jalchu\WebstormProjects\vue-element-admin\node_modules\node-sass\vendor\win32-x64-64\binding.node
Testing binary
Binary is fine
npm WARN script-ext-html-webpack-plugin@2.0.1 requires a peer of html-webpack-plugin@^3.0.0 but none is installed. You must install peer dependencies yourself.
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@1.2.6 (node_modules\fsevents):
npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@1.2.6: wanted {"os":"darwin","arch":"any"} (current: {"os":"win32","arch":"x64"})
 
added 1431 packages from 1753 contributors and audited 17034 packages in 178.63s
found 0 vulnerabilities
```

# Reference

https://github.com/angular/angular-phonecat/issues/235 （Solution）

https://blog.csdn.net/itguangzhi/article/details/77771903 （Reason）

# Check .gitconfig location

```raw
git config --list --show-origin
```

https://stackoverflow.com/questions/2114111/where-does-git-config-global-get-written-to    (.gitconfig location)

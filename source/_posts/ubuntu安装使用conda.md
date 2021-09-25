---
title: ubuntu安装使用conda
date: 2021-06-20 22:01:22
tags:
- ubuntu
- python
categories:
---

# ubuntu安装使用conda

```shell script
Ubuntu install conda with usage
 
1.sudo apt-get update
2.sudo apt-get install curl
3.cd /tmp
4.curl –O https://repo.anaconda.com/archive/Anaconda3-2020.02-Linux-x86_64.sh
5.sha256sum Anaconda3–2020.02–Linux–x86_64.sh
6.bash Anaconda3-2020.02-Linux-x86_64.sh
7.source ~/.bashrc
8.conda info
 
注：
第四步下载地址，挑选自己需要的版本：https://repo.anaconda.com/archive/
第五步是校验，校验地址如下，记得改成自己安装的版本号：
https://docs.anaconda.com/anaconda/install/hashes/Anaconda3-2021.05-Linux-x86_64.sh-hash/
 
 
 
conda使用命令
$ conda --version
$ conda info
 
$ conda info --envs
$ conda env list
 
$ conda create -n test python=3.6
$ conda create --name test1 python=2.7
$ conda env remove --name test
$ conda remove -n venv --all
 
$ conda activate test
$ conda deactivate
 
$ conda install --yes --file requirements.txt
$ while read requirement; do conda install --yes $requirement; done < requirements.txt
$ while read requirement; do conda install --yes $requirement || pip install $requirement; done < requirements.txt
 
$ anaconda search -t conda pyqt5
$ anaconda show astrofrog/pyqt5
$ conda install --channel https://conda.anaconda.org/astrofrog pyqt5
$ conda install -n test --channel https://conda.anaconda.org/astrofrog pyqt5
```


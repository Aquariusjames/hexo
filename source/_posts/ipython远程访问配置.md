---
layout: java
title: ipython远程访问配置
date: 2018-11-06 11:11:51
tags:
    - python
    - 教程
---

ubuntu 中实现ipython notebook的远程访问

# 安装ipython 和 ipython notebook

1. 安装 [Anoconda3](https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/)

        bash ./Anoconda-xx.sh

2. 直接使用pip 安装

        pip install ipython 
        pip install notebook

# 创建登录用户和密码

``` python 
In [1]: from IPython.lib import passwd
In [2]: passwd()
Enter password:
Verify password:
Out[2]: 'sha1:026678de36b2:e4b83078e02c470b15789ade069359a20b0385dd'

```

# 创建服务器

``` shell
ipython profile create myserver
cd ~/.ipython/profile_myserver/
vim ipython_notebooke_config.py

>>>>>
c = get_config()

# Kernel config
c.IPKernelApp.pylab = 'inline'

# Notebook config
c.NotebookApp.ip='*'
c.NotebookApp.open_browser = False
c.NotebookApp.password = u'sha1:026678de36b2:e4b83078e02c470b15789ade069359a20b0385dd'

# It's a good idea to put it on a know,fixed port
c.NotebookApp.port = 6789
<<<<<<


```

# 启动ipython服务器

        ipython notebook --config=~/.ipython/profile_myserver/ipython_notebook_config.py

# ipython notebook 设置多个虚拟环境

``` shell
# 查找kernel.json的位置
find . -name "kernel.json"

<<<<
# xrr @ Cloud-06 in ~ [11:53:53] 
$ find . -name "kernel.json"
./anaconda3/share/jupyter/kernels/python3/kernel.json
./anaconda3/envs/py36/share/jupyter/kernels/python3/kernel.json
./anaconda3/pkgs/ipykernel-4.9.0-py37_1/share/jupyter/kernels/python3/kernel.json
(base) 
>>>>

cd ~/anaconda3/envs/py36/share/jupyter/kernels
mkdir your_env_name && cd your_env_name
cp ../python3/kernel.json .
vim kernel.json

<<<
{
 "argv": [
  "/home/xrr/anaconda3/envs/py36/bin/python", # 替换为虚拟环境的python
  "-m",
  "ipykernel_launcher",
  "-f",
  "{connection_file}"
 ],
 "display_name": "py36", # 修改为你的虚拟环境的名称
 "language": "python"
}
>>>

```

# jupyter 切换虚拟环境

![](/images/dl/TIM截图20181106121037.jpg)

**Enjoy yourself！**
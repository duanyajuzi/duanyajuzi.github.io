---
layout: post
title: 记述ubuntu下安装docker管理工具
date: 2017-11-13 16:46:29
categories: blog
author:     "jarvis"
catalog:    true
tags:
    - docker
    - rancher
---

>安装docker管理工具,能够更直观的查看和管理docker容器

### 环境
* Ubuntu 16.04
* docker 17.09.0-ce

### 安装rancher
```
sudo docker run -d --restart=unless-stopped -p 8080:8080 rancher/server:stable
```
安装完成后,输入http://127.0.0.1:8080 访问
./configure --with-features=huge \
            --enable-multibyte \
            --enable-rubyinterp=yes \
            --enable-pythoninterp=yes \
            --with-python-config-dir=/usr/lib/python2.7/config \
            --enable-python3interp=yes \
            --with-python3-config-dir=/usr/lib/python3.5/config \
            --enable-perlinterp=yes \
            --enable-luainterp=yes \
            --with-lua-prefix=/usr/include/lua5.3 \
            --enable-fail-if-missing \
            --with-luajit –enable-fail-if-missing \
            --enable-gui=gtk2 --enable-cscope --prefix=/usr

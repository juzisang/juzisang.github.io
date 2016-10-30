---
title: 使用NVM来管理Node
date: 2016-10-30 16:56:49
categories: Node
tags:
- Node
- Nvm
---

## 为什么要使用NVM？
由于node的生态圈的繁华，基于node的工具或者项目层出不穷。  
各种工具使用的node版本可能不一致，就会出现一些奇怪的问题。  
比如：你电脑安装的是最新的node，但是有个工具用最新的node就会报错。解决办法是使用低版本的node。  
**NVM就是用来帮助我们快速切换node版本的。**  
<!--more-->
[node所有版本列表](https://nodejs.org/en/download/releases/)

## 安装NVM

我使用的**Window系统**。所以用
[nvm-windows](https://github.com/coreybutler/nvm-windows)来使用NVM  
[nvm-window下载](https://github.com/coreybutler/nvm-windows/releases),上面贴的是**nvw-window**在Github的主页，找不到下载地址的同学看这里。进去看到**nvm-setup.zip**，下载后会得到.exe的安装程序。
一路next  


**nvm安装路径**  
![nvm安装路径](http://odlhbbkmh.bkt.clouddn.com/16-10-30/54735866.jpg)  


**当前使用的node会放在这里**  
![](http://odlhbbkmh.bkt.clouddn.com/16-10-30/12257774.jpg)  
**验证是否安装成功**
```
nvm # 输出所有命令
```

## 安装切换各版本 node/npm

```
nvm list #查看本地所有node版本

nvm install 4.2.2 #安装 4.2.2 版本

nvm use 4.2.2 #切换至 4.2.2 版本

nvm uninstall 4.2.2 #卸载4.2.2 版本
```

## 配置node的下载路径
nvm默认的下载地址是http://nodejs.org/dist/，这是国外的服务器，在国内下载速度很慢。
在控制台输入nvm，我们看到了
![](http://odlhbbkmh.bkt.clouddn.com/16-10-30/26752405.jpg)  
好像是有设置下载镜像的命令，但是我配置了一下，不行。查看issues发现好像是作者忘记加上去了= =。

**解决办法：**  
在你nvm的安装路径下，找到**settings.txt**打开，在后面加加上
```
node_mirror: https://npm.taobao.org/mirrors/node/
npm_mirror: https://npm.taobao.org/mirrors/npm/
```

![](http://odlhbbkmh.bkt.clouddn.com/16-10-30/28136746.jpg)


## 参考

* [使用 nvm 管理不同版本的 node 与 npm](http://www.cnblogs.com/kaiye/p/4937191.html)
* [nvm，nodejs和npm安装使用](http://www.kancloud.cn/summer/nodejs-install/71975)
* [https://github.com/coreybutler/nvm-windows/pull/160](https://github.com/coreybutler/nvm-windows/pull/160)
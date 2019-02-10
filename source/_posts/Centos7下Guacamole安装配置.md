---
title: Centos7下Guacamole安装配置
date: 2017-09-23
categories:
  - Linux
tags:
  - Linux
  - Guacamole
---

Guacamole 是一个提供远程桌面解决方案的开源项目，通过浏览器就能操作虚拟机，适用于 Chrome、Firefox、IE9+ 等浏览器（浏览器需要支持 HTML5）。只要在一个服务器成功安装 Guancamole，就可以通过访问一个 web 浏览器去操作我们配置好的虚拟机。当然，作为一个开源的项目，我们可以对 Guacamole 做相应的定制修改，使其适配我们的项目需求，比如做成自动登录的，加上项目权限验证等。

<!--more-->

## 一、介绍

Guacamole 是一个提供远程桌面解决方案的开源项目，通过浏览器就能操作虚拟机，适用于 Chrome、Firefox、IE9+ 等浏览器（浏览器需要支持 HTML5）。只要在一个服务器成功安装 Guancamole，就可以通过访问一个 web 浏览器去操作我们配置好的虚拟机。当然，作为一个开源的项目，我们可以对 Guacamole 做相应的定制修改，使其适配我们的项目需求，比如做成自动登录的，加上项目权限验证等。

![images](/images/Centos7下Guacamole安装配置/1535020262371.png)

## 二、准备

- Centos7 x86-64
- JDK8
- Tomcat7
- Guacamole 服务器：guacamole-server-0.9.9.tar.gz
- Guacamole 客户端：guacamole-0.9.9.war

## 三、JDK 安装

```bash
yum update
yum install java
```

## 四、安装 Tomcat

```bash
# 创建用户组
groupadd tomcat
useradd -s /bin/bash -g tomcat tomcat

# 下载Tomcat
yum install wget
wget https://archive.apache.org/dist/tomcat/tomcat-8/v8.5.9/bin/apache-tomcat-8.5.9.tar.gz
tar -zxvf apache-tomcat-8.5.9.tar.gz
mv apache-tomcat-8.5.9 tomcat
mv ./tomcat /usr/local/

# 修改权限
chown -R tomcat:tomcat /usr/local/tomcat

# 启动
/usr/local/tomcat/bin/startup.sh
```

### 配置为系统服务

```bash
# catalina.sh 移动到init.d目录，并重命名tomcat
cp /usr/local/tomcat/bin/catalina.sh /etc/init.d
mv /etc/init.d/catalina.sh /etc/init.d/tomcat

# 修改comcat文件，并将这段的代码加入其中
vi /etc/init.d/tomcat
#chkconfig:2345 10 90
#description:Tomcat service
# java和tomcat安装路径
CATALINA_HOME=/usr/local/apache-tomcat-8.5.9
JAVA_HOME=/usr/local/jdk1.8.0_111

# 修改权限
chmod +x /etc/init.d/tomcat

# 加入服务列表
chkconfig --add tomcat

# 检查是否加入列表
chkconfig --list tomcat

# 测试tomcat服务
service tomcat

# 常用操作
service tomcat start
service tomcat stop

# 将tomcat加入到环境变量中
vi /etc/profile
# 末尾加入
export CATALINA_HOME=/usr/local/tomcat
# 刷新
source /etc/profile
```

## 五、安装 Guacamole 服务端

```bash
# 安装依赖
yum install -y cairo cairo-devel libpng libpng-devel freerdp freerdp-devel pango pango-devel libssh2 libssh2-devel libtelnet libtelnet-devel libvncserver libvncserver-devel pulseaudio pulseaudio-libs pulseaudio-libs-devel openssl cd openssl-devel libvorbis libvorbis-devel uuid uuid-devel gcc libjpeg-turbo-devel

# 编译安装
wget http://ncu.dl.sourceforge.net/project/guacamole/current/source/guacamole-server-0.9.9.tar.gz
tar -xvzf guacamole-server-0.9.9.tar.gz
cd guacamole-server-0.9.9/
./configure --with-init-dir=/etc/init.d
make
make install
ldconfig

# 配置guacamole服务
chown root.root /etc/init.d/guacd
chmod 755 /etc/init.d/guacd
chkconfig --add guacd
chkconfig guacd on

# 常用命令
service guacd start
service guacd stop
service guacd restart
service guacd status
```

## 六、安装 Guacamole 客户端

### 配置文件

- 创建配置文件

```bash
mkdir /etc/guacamole
mkdir /root/.guacamole
touch /etc/guacamole/guacamole.properties
touch /etc/guacamole/user-mapping.xml
ln -s /etc/guacamole/guacamole.properties /root/.guacamole/
```

- guacamole.properties

```bash
# 配置基本用户映射
basic-user-mapping: /etc/guacamole/user-mapping.xml
```

- user-mapping.xml

```xml
<user-mapping>
    <authorize username="juzisang" password="juzisang">
        <connection name="Windows10">
            <protocol>rdp</protocol>
            <!-- 加密方式 -->
            <param name="security">nla</param>
            <param name="hostname">10.1.48.141</param>
            <param name="port">3389</param>
            <!-- 屏幕渲染色值 -->
            <param name="color-depth">24</param>
            <!-- 用户名密码在nla加密下必须输入 -->
            <param name="username">username</param>
            <param name="password">password</param>
            <!-- 忽略证书 -->
            <param name="ignore-cert">true</param>
        </connection>
        <connection name="Windows10 JuZi">
            <protocol>rdp</protocol>
            <param name="security">nla</param>
            <param name="hostname">192.168.0.125</param>
            <param name="port">3389</param>
            <param name="color-depth">24</param>
            <param name="username">username</param>
            <param name="password">password</param>
            <param name="ignore-cert">true</param>
        </connection>
    </authorize>
</user-mapping>
```

- 添加进环境变量

```bash
vi /etc/profile
# 末尾添加
export GUACAMOLE_HOME=/etc/guacamole
# 刷新
source /etc/profile
```

- 客户端网页下载和安装

```bash
wget http://downloads.sourceforge.net/project/guacamole/current/binary/guacamole-0.9.9.war

# 移动至 Tomact webapps 目录
mv guacamole-0.9.9.war /usr/local/tomcat/webapps/guacamole.war

# 重启 Tomcat
service tomcat stop
service tomcat start
```

## 七、遇到问题

### 编译失败

缺少`gcc` `libjpeg-turbo-devel`这两个库，安装即可

```bash
yum install -y gcc libjpeg-turbo-devel
```

### RDP 连接 win10 老是断开

guacamole 默认的 RDP 为标准协议，更改为 nla 加密协议解决问题，
注意：nla 加密下，username，password 必须填写

```xml
<param name="security">nla</param>
```

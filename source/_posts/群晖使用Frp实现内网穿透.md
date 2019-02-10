---
title: 群晖使用Frp实现内网穿透
date: 2017-08-20
categories:
  - Linux
tags:
  - Linux
  - Frp
  - 群晖
---

## 准备

- 群晖
- 拥有外网 ip 的 vps
- [frp](https://github.com/fatedier/frp)
- 域名（不是必须，你也可以用 ip 来访问）
  <!--more-->

## 准备

- 群晖
- 拥有外网 ip 的 vps
- [frp](https://github.com/fatedier/frp)
- 域名（不是必须，你也可以用 ip 来访问）

## VPS 设置

### 下载 Frp

我这是使用的是 64 位的 ubuntu 系统的，如果需要其他的，请自行在 [Github](https://github.com/fatedier/frp/releases) 上下载对应的版本

```bash
// 下载
wget https://github.com/fatedier/frp/releases/download/v0.13.0/frp_0.13.0_linux_amd64.tar.gz
// 解压
tar -zxvf frp_0.13.0_linux_amd64.tar.gz
// 进去frp目录
cd frp_0.13.0_linux_amd64
// 删除不需要的文件
rm -rf frpc frpc.ini frpc_full.ini
```

### Frps 配置

```
[common]
bind_port = 7000
max_pool_count = 5
authentication_timeout = 900

# 管理端 进入frp自带的web管理界面的帐号密码
dashboard_port = 7500
dashboard_user = user
dashboard_pwd = pwd

# ssh端口 auth_token就是密钥，随便填个
[ssh]
listen_port = 6000
auth_token = token

# 群晖网页
[dms]
listen_port = 5000
auth_token = token

# 群晖
[cloud_station]
listen_port = 6690
auth_token = token

[ds_photo]
listen_port = 8080
auth_token = token

# 域名
# vhost_http_port = 80
# subdomain_host = juzisang.com
```

### 启动

```
./frps -c ./frps.ini
```

## 群晖配置

### 开启 ssh

![images](/images/群晖使用Frp实现内网穿透/1535020185186.png)

### ssh 进入群晖

```bash
// ssh进入群晖
ssh juzisang@群晖局域网ip
// 切换root用户
sudo -s
```

### 下载 Frp

```bash
// 下载
wget https://github.com/fatedier/frp/releases/download/v0.13.0/frp_0.13.0_linux_amd64.tar.gz
// 解压
tar -zxvf frp_0.13.0_linux_amd64.tar.gz
// 进去frp目录
cd frp_0.13.0_linux_amd64
// 删除不需要的文件
rm -rf frps frps.ini frps_full.ini
```

### Frpc 配置

```
[common]
server_addr = xx.xx.xx.xx
server_port = 7000
// frps中填的
auth_token = token
pool_count = 1

# ssh
[ssh]
type = tcp
// 群晖局域网ip
local_ip = 192.168.31.50
// 本地端口
local_port = 22
// 穿透到vps中的端口
remote_port = 6000

# 群晖网页
[dms]
type = tcp
local_ip = 192.168.31.50
local_port = 5000
remote_port = 5000
# subdomain = nas

# 群晖同步
[cloud_station]
type = tcp
local_ip = 192.168.31.50
local_port = 6690
remote_port = 6690

# 群晖照片
[ds_photo]
type = tcp
local_ip = 192.168.31.50
local_port = 80
remote_port = 8080
```

### 启动

```
./frps -c ./frps.ini
```

## 后台运行

- 服务端

```
nohup ./frps -c ./frps.ini &
```

- 客户端

```
nohup ./frpc -c ./frpc.ini &
```

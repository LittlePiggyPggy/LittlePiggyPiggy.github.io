---
layout: wiki
title: ssh免密登录和github多账号处理
categories: [linux, git]
description: ssh免密登录和github多账号处理
keywords: ssh, git， linux
---


## 两台linux机器互相认证
1. 登录 server A
```shell
ssh-kengen -t rsa -C 'comments'
```
2. 将公钥.pub文件上传到server B ~/.ssh 目录下
3. 登录server B，在 ~/.ssh 目录下
```shell
cat id_dsa.pub >> ~/.ssh/authorized_keys
```
4. 之后可以在server A 上无密码登录 server B
5. 注意事项
``` shell
# 设置authorized_keys权限
$ chmod 600 authorized_keys 
# 设置.ssh目录权限
$ chmod 700 -R .ssh
```

## github 多帐号
1. 创建两个帐号的公钥私钥
2. 在~/.ssh 目录下配置config文件
```shell
#第一个账号，默认使用的账号
Host github.com
 HostName github.com
 User user_1
 IdentityFile ~/.ssh/id_rsa
# 第二个账号 kentsun
Host user_2.github.com # kentsun为前缀名，可以任意设置
 HostName github.com
 User user_2
 IdentityFile ~/.ssh/kentsun_rsa
```
3. 登录github在设置中添加公钥
4. 在.ssh根目录下, 清空本地的 SSH 缓存，添加新的 SSH 密钥 到 SSH agent中
```shell
cd ~/.ssh
ssh-add -D
ssh-add id_rsa
ssh-add id_rsa_second
# q确认是否成功
ssh-add -l
```
5. 输入指令, 验证配置是否成功
```shell
# @后面是config中配置的host
$ ssh -T git@github.com
Hi username! You've successfully authenticated, but GitHub does not provide shell access.
```
6. 取消全局配置

```shell
# 取消全局 用户名/邮箱 配置
git config –global –unset user.name
git config –global –unset user.email

# 单独设置每个repo 用户名/邮箱
git config user.name "用户名"  //在config后加上--global即全局
git config user.email "邮箱"
```
7. 特别注意, 现在clone的时候github域名要使用~/.ssh/congfig 中配置的host

```shell
$ git clone git@github.com:user_1/learngit.git

$ git clone git@user_2.github.com:tinypiggy/learngit.git
```

---
title: Linux SSH 免密登陆
date: 2017-12-14 18:56:56
toc: true
categories:
- CS
- OS
- Linux
tags:
- Linux
---



## 简述

传统的网络传输协议，如HTTP、FTP、Telnet、RSH等，设计实现并未考虑安全机制，很容易受到中间人攻击，传输的数据被截获篡改。SSH则提供一系列的机制来保证传输安全。本文讨论的Linux SSH的免密登陆，用到了SSH的一个重要功能——基于公开密钥加密算法的身份认证。

- 验证使用的Linux版本为：`CentOS Linux release 7.3.1611 (Core)`


- 本文额外介绍了一些基本概念，若仅希望了解免密登陆的配置方法，可以直接查看[生成拷贝密钥](/2017/12/14/CS/OS/Linux/linux_ssh_passwordless_login/#生成拷贝密钥)

<!-- more -->



## 相关文件

- 主机SSH的全局配置目录，默认路径为：`/etc/ssh`
  - rsa、ecdsa、ed25519后缀的文件：使用相应算法生成的公钥私钥，用于本主机的身份认证
  - config后缀的文件：ssh配置文件
- 用户SSH的配置目录，默认路径为：`~/.ssh`
  - rsa、ecdsa后缀的文件：使用相应算法生成的公钥私钥，用于本用户的身份认证
  - [known_hosts](https://www.ssh.com/ssh/host-key#sec-Known-Host-Keys)：保存本用户信任的主机公钥，用于本用户访问相应主机时（主机）的身份验证
  - [authorized_keys](https://www.ssh.com/ssh/authorized_keys/)：保存本用户信任的用户公钥，用于其他用户免密登陆本用户时（用户）的身份验证

<img src="/images/CS/201712/linux_ssh_passwordless_login_folder.jpg" width="700">



## 主机认证

### 基本过程

- 客户端使用ssh访问某服务端，服务端将自己的公钥提供给客户端。
- 客户端判断该服务端及其公钥是否在`~/.ssh/known_hosts`中，若在且公钥一致，则验证通过；若在但公钥不一致，则发出警告。
- 若该服务端不在客户端的known_hosts文件中，则客户端会根据该服务端的公钥，生成一个方便人工识别的指纹（fingerprint，如下图）提供给用户，用户根据指纹判断该服务端是否为目标服务器，若是，则进行相关确认后，该服务端的标志和公钥会被保存到known_hosts中。

### 图片说明

图：客户端提示用户验证指纹
<img src="/images/CS/201712/linux_ssh_passwordless_login_fingerprint_01.jpg">
图：在服务端使用命令根据公钥生成的指纹
<img src="/images/CS/201712/linux_ssh_passwordless_login_fingerprint_02.jpg">
图：确认后服务端的公钥被保存在客户端相应用户的known_hosts文件中
<img src="/images/CS/201712/linux_ssh_passwordless_login_hostkey_01.jpg">
图：服务端的公钥
<img src="/images/CS/201712/linux_ssh_passwordless_login_hostkey_02.jpg">



## 用户认证

### 基本步骤

- 某用户使用ssh登陆另一用户，将其公钥提供给目标用户
- 目标用户判断该用户及其公钥是否在自己的`~/.ssh/authorized_keys`文件中，若在且公钥一致，则验证通过，可以免密登陆；若在但公钥不一致或不在，则仍需提供密码才能登陆。


### 生成拷贝密钥

#### 命令

```shell
# 生成密钥命令，默认使用rsa算法，可用 -t 参数指定算法
ssh-keygen
ssh-keygen -t ecdsa
ssh-keygen -t ed25519

# 拷贝密钥命令，将命令拷贝至相应主机用户的authorized_keys文件中
ssh-copy-id [user@]hostname
```

####  步骤

若dev01主机上的spark用户，希望免密登陆dev02上的spak用户

- dev01的spark用户使用`ssh-keygen -t ecdsa`命令生成密钥
- dev01的spark用户使用`ssh-copy-id spark@dev02`命令，将自己的公钥拷贝至dev02 spark用户的authorized_keys文件中，这里也可以直接修改authorized_keys文件，但是注意不要修改其权限（600）

### 图片说明

图：生成rsa密钥

<img src="/images/CS/201712/linux_ssh_passwordless_login_keygen_01.jpg" width="700">

图：生成ecdsa密钥

<img src="/images/CS/201712/linux_ssh_passwordless_login_keygen_02.jpg" width="700">

图：拷贝密钥

<img src="/images/CS/201712/linux_ssh_passwordless_login_copyid.jpg">

图：目标用户已验证的公钥

<img src="/images/CS/201712/linux_ssh_passwordless_login_authorized_keys_01.jpg">

图：本用户的公钥
<img src="/images/CS/201712/linux_ssh_passwordless_login_authorized_keys_02.jpg">



## 遗留问题

- centos中混用rsa和ecdsa算法的密钥，何种情况下使用何种算法的密钥尚不清楚




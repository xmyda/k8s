---
layout: post
title: Linux FAQ 以及 命令
categories: linux
description: Linux FAQ 以及 命令
keywords: linux
---


# Linux 篇：


## CentOs 7 修改主机名

```
hostnamectl --static set-hostname <host-name>
```
 

## 删除0字节文件

```
find -type f -size 0c | xargs rm -f
```
 


## 截取 N - M 的日志

```
sed '/13:30:00/,/13:50:00/!d' catalina.out >> 22222.txt
```
 

## 添加主机路由


方法1：

```
cat /etc/sysconfig/network-scripts/route-em1               

# route-em1有严格的要求，em1必须与实际网卡名称对应，否则会失败
ADDRESS0=10.6.0.0                                                 
# 可以添加多条路由，必须从编号0开始
NETMASK0=255.255.0.0
GATEWAY0=172.16.1.1
```


方法2：

```
cat /etc/sysconfig/network-scripts/route-em1

10.6.0.0/16 via 172.16.1.1 dev em1
```
 

## centos 7 内核顺序变更

查看内核顺序:

```
awk -F\' '$1=="menuentry " {print $2}' /etc/grub2.cfg

# 选择内核0为默认

grub2-set-default 0
```
 

 

## 查询缺少的.so 文件

如:  ldd nginx

```
libcrypto.so.6 => not found

yum provides libcrypto.so.6

openssl098e-0.9.8e-29.el7.centos.i686 : A compatibility version of a general cryptography and TLS library

Repo        : base

Matched from:

Provides    : libcrypto.so.6
```

```
yum -y install openssl098e
```
 

 
## crontab FAQ


关于 Crontab 不能使用的问题..没安装等..

```
yum install vixie-cron

yum install crontabs

service crond start
```


## 修改时区

```
vi /etc/sysconfig/clock

ZONE="Aisa/Shanghai"

UTC=true

ARC=false

# 更新时间不生效，还是原来的时区...

cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```
 

 

## 创建大于2T的分区

Fdisk所使用的分区表为MBR，Master Boot Record，即主引导记录。

硬盘的0面、0道、1扇区就是主引导扇区，Fdisk将会写512个字节的记录在此，即MBR记录。

MBR分区表：（MBR含义：Master Boot Record，主引导记录）

所支持的最大卷：2T （T; terabytes,1TB=1024GB）

对分区的设限：最多4个主分区或3个主分区加一个扩展分区（扩展分区中支持无限制的逻辑驱动器）

GPT分区表：（GPT含义：GUID分区表）

支持最大卷：18EB，（E：exabytes,1EB=2(10) PB=1024PB，1PB=2(10) TB=1024TB）

每个磁盘最多支持128个分区

```

# parted /dev/sdb

(parted) mkpart primary 0% 10%

(parted) mkpart primary 10% 100%

(parted) p

Model: DELL MD3000 (scsi)

Disk /dev/sdb: 13.0TB

Sector size (logical/physical): 512B/512B

Partition Table: gpt

Number Start End Size File system Name Flags

1 17.4kB 1300GB 1300GB primary

2 1300GB 13.0TB 11.7TB primary

(parted)quit

```
 

## shell 下 sudu FAQ


Shell命令下使用sudo 提示 sudo: sorry, you must have a tty to run sudo 的错误

编辑 /etc/sudoers 文件

查找 “

```
Defaults  requiretty
```

修改为

```
Defaults:username  !requiretty
```


Shell 命令下使用 sudo echo > 这样的命令 依然提示 权限不够

这是因为重定向符号 “>” 也是 bash 的命令。sudo 只是让 echo 命令具有了 root 权限，

但是没有让 “>” 命令也具有root 权限，所以 bash 会认为这个命令没有写入信息的权限。

 
可以利用 “sh -c” 命令，它可以让 bash 将一个字串作为完整的命令来执行，这样就可以将 sudo 的影响范围扩展到整条命令。

 
```
sudo sh -c "echo 654321 > 1.txt"
```
 

 

 

## SVN FAQ

钩子日志输出

编辑 post-commit 文件

写入

```
svnlook changed /svn/yx > /svn/yx/changed.log && /shell/commit.sh
```

svnlook changed 命令 将 svn/yx 库操作记录到 changed.log 文件内... 然后用shell读取 changed.log 内的操作~执行脚本...

 

svn url 变更

```
svn switch --relocate svn://123.123.123.123/rl/api svn://192.168.0.74/rl/api


# svn switch --relocate 原url地址  新URL地址 
```
 



## Mysql FAQ


删除mysql 的binlog

一：查看备份的日志。

```
mysql> show binary logs;

+------------------+------------+

| Log_name     | File_size |

+------------------+------------+

| mysql-bin.000001 | 392914665 |

| mysql-bin.000002 |    2765 |

| mysql-bin.000003 | 1073742259 |

| mysql-bin.000004 | 1073741949 |

+------------------+------------+

11 rows in set (0.11 sec)
```
 

删除指定binglog , 如下语句，指删除3 之前的所有binlog,而非 一个binlog

```
mysql> purge binary logs to 'mysql-bin.00003';

mysql> show binary logs;

+------------------+------------+

| Log_name     | File_size |

+------------------+------------+

| mysql-bin.000004 | 1073741949 |

+------------------+------------+

11 rows in set (0.11 sec)
```
 

 

## Mongodb FAQ

查看当前性能

```
mongodb/bin/mongostat -h xx.xx.xx.xx:27017
```
 

查看读写

```
mongodb/bin/mongotop -h xx.xx.xx.xx:27017
```
 

查看当前执行语句

```
db.currentOp()
```
 

杀掉进程(先执行 db.currentOp()获取进程号,类似ps -ef)

```
db.killOP(2920488)
```
 

查看最近错误

```
db.getLastError()db.getLastError()
```
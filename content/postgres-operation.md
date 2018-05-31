---
title: "postgresql 安装|重置密码|备份数据|导入数据"
date: 2018-05-31T14:11:47+08:00
tags:
- blogs
categories:  "blogs" 
---

## 安装

本机在Centos 7环境下安装postgresql

使用如下图安装方式安装完成,[安装指南](https://www.postgresql.org/download/linux/redhat/)

![](install.png)

## 重置密码

完成安装后,并不知道postgresql的密码，在服务器终端通过ps命令可以查到postgres的进程，如下：

```shell
[root@ZABBIX-SERVER 9.5]# ps -ef|grep postgres
root     24737 24564  0 13:40 pts/1    00:00:00 su postgres
postgres 24738 24737  0 13:40 pts/1    00:00:00 bash
postgres 24902     1  0 13:43 pts/1    00:00:00 /usr/pgsql-9.5/bin/postgres -D /var/lib/pgsql/9.5/data
postgres 24903 24902  0 13:43 ?        00:00:00 postgres: logger process   
postgres 24905 24902  0 13:43 ?        00:00:00 postgres: checkpointer process   
postgres 24906 24902  0 13:43 ?        00:00:00 postgres: writer process   
postgres 24907 24902  0 13:43 ?        00:00:00 postgres: wal writer process   
postgres 24908 24902  0 13:43 ?        00:00:00 postgres: autovacuum launcher process   
postgres 24909 24902  0 13:43 ?        00:00:00 postgres: stats collector process   
root     26244 24816  0 13:50 pts/3    00:00:00 grep --color=auto postgres
[root@ZABBIX-SERVER 9.5]# 

```

通过ps命令,postgres默认安装路径在`/usr/pgsql-9.5/`目录下，配置文件在`/var/lib/pgsql/9.5/data`中，该目录也是数据库存储目录

编辑pg_hba.conf文件  

```shell
vim /var/lib/pgsql/9.5/data/pg_hda.conf
```

将原来所有方式修改为trust，如下：

```shell
# "local" is for Unix domain socket connections only
local   all             all                                     md5
# IPv4 local connections:
host    all             all             127.0.0.1/32            trust
host    all             all             0.0.0.0/0               trust
# IPv6 local connections:
host    all             all             ::1/128                 trust

```

修改完成后重启

```shell
su postgres
cd /usr/pgsql-9.5/bin
./pg_ctl restart -D /var/lib/pgsql/9.5/data
等待服务器进程关闭 .... 完成
服务器进程已经关闭
正在启动服务器进程
bash-4.2$ < 2018-05-31 13:43:30.580 CST >日志:  无法绑定 IPv6 套接字: 无法指定被请求的地址
< 2018-05-31 13:43:30.580 CST >提示:  是否有其它 postmaster 已经在端口 5432 上运行了? 如果没有, 请等待几秒钟后然后再重试.
< 2018-05-31 13:43:30.601 CST >日志:  日志输出重定向到日志收集进程
< 2018-05-31 13:43:30.601 CST >提示:  后续的日志输出将出现在目录 "pg_log"中.

```

重启完成后,使用postgres登录

```shell
[root@ZABBIX-SERVER 9.5]# psql --username=postgres
用户 postgres 的口令：
psql (9.5.13)
输入 "help" 来获取帮助信息.
postgres=# ^C
```

在该会话中执行修改密码命令：

```shell
ALTER USER postgres WITH PASSWORD '新密码'; 
```

操作完成的，执行：\q命令回车退出。   

最后,恢复pg_hba.conf设置为md5并重启服务 

## 创建数据库

创建用户数据库，如testdb： 

```sql
postgres=# CREATE DATABASE testdb OWNER dbuser;
```

将testdb数据库的所有权限都赋予dbuser： 

```sql
postgres=# GRANT ALL PRIVILEGES ON DATABASE testdb TO dbuser;
```

## 备份数据

使用如下命令：

```shell
pg_dump -h 127.0.0.1 -U postgres databasename > db_backup_date.sql
```

-h:目标主机

-U：用户名称

## 导入数据库

使用如下命令：

```shell
psql -U postgres -d databasename -f back_db_conf0529.sql 
```

-U：用户名称

-d：数据库名称

-f：导入数据库文件
---
layout: post
title: oracle数据库备份与还原完整版
category: oracle
tags: [oracle]
---

备份和还原数据库是我们开发人员的必备技能，在这里写篇文章记录一下，供大家参考。
# oracle创建表空间

备份与还原oracle数据库前，先查一下导出库的表空间，在导入库添加相应的表空间。

+ 查看当前用户的缺省表空间
```
select * from user_users;
```

+ 查询用户所有表空间（一个用户可能有多个表空间）
```
select tablespace_name,owner from dba_segments where owner ='old_db_name' group by tablespace_name,owner;
```

+ 修改用户表空间
```
alter user username default tablespace TBS01;
```

+ （超管）查看表空间
```
select * from dba_tablespaces;
```

+ （超管）查看表空间文件
`select * from dba_data_files;`

+ 在新库（就是一会要建新用户来还原数据库的机器上的oracle）创建表空间
```
create tablespace TBS01
datafile '/u01/app/oracle/oradata/TBS01.DBF'
size 128m
autoextend on
    next 50m
    maxsize unlimited;
```
+ 删除非空表空间，包含物理文件
drop tablespace TBS01 including contents and datafiles;

# oracle创建用户

新增一个用户用来还原数据库（对于oracle，一个数据库就是对应的一个oracle用户）。

> 说明：备份数据库时查一下用户有哪些权限，新增的用户也分配同样的权限。

+ 创建用户并授权
```
create user db_user_name identified by oracle
default tablespace TBS01 
temporary tablespace TEMP
profile DEFAULT;

grant connect to db_user_name;
grant resource to db_user_name;
grant create database link to db_user_name;
grant create job to db_user_name;
grant create procedure to db_user_name;
grant create public database link to db_user_name;
grant create synonym to db_user_name;
grant create table to db_user_name;
grant create view to db_user_name;
grant debug connect session to db_user_name;
grant select any dictionary to db_user_name;
grant unlimited tablespace to db_user_name;
```
+ 查询用户
```
select * from all_users;   
```
+ 删除用户
```
drop user db_user_name cascade;
```
# 导出（备份）
+ 执行dos命令，导出数据库备份文件：
```
exp 'sys/oracle@192.168.10.162/orcl as sysdba' owner=old_db file=d:\old_db.dmp buffer=102400000
```
 # 导入（还原）
+ 执行dos命令，把数据库备份文件还原到新库：
```
imp 'sys/oracle@192.168.10.174/orcl as sysdba' fromuser=old_db touser=new_db file=d:\old_db.dmp  log=d:\new_db_imp.log ignore=y;
```
> 备注：执行导出导入命令的机器要安装oracle客户端。

<!--
## 参考

* [Reactive Android UI Programming with RxBinding](https://realm.io/news/donn-felker-reactive-android-ui-programming-with-rxbinding/)
-->
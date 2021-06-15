---
title: MySQL 5.7安装（Linux）
tags: []
id: '1481'
categories:
  - - Linux
date: 2020-08-25 11:33:56
---

> 本次主要是为后续学习MySQL5.7中关于json格式数据格式处理做准备

所以可以先对安装mysql5.7做一下记录

### **_Linux下MySQL5.7的安装_**

#### **下载MySQL的yum Repository**

wget -i -c http://dev.mysql.com/get/mysql57-community-release-el7-10.noarch.rpm

#### **安装**

yum -y install mysql57-community-release-el7-10.noarch.rpm

yum -y install mysql-community-server

#### **启动**

systemctl start mysqld.service

#### **查看运行状态**

systemctl status mysqld.service

#### 查找MySQL首次登陆密码（ **首次安装后默认的root密码会在mysql.log中** ）

grep "password" /var/log/mysqld.log

#### 登录数据库

mysql -uroot -p

#### 此时不能做任何操作，MySQL默认修改密码后才可以使用

在之前的版本中，密码字段的字段名是 password，5.7版本改为了 authentication\_string

mysql> use mysql;

mysql> update user set authentication\_string = password('root'), password\_expired = 'N', password\_last\_changed = now() where user = 'root';

#### 更新数据（user表和privilige表的用户信息/权限信息到内存中，不用重启mysql）

mysql> flush privileges

#### **退出，使用新的密码登录**

* * *

## 可能遇到的问题

1.  **_错误操作导致的登录失败（忘记密码，密码修改失败等）_**

*   **修改MySQL的配置文件my.cnf (windows系统为my.ini)**

在\[mysqld\]下增加一行skip-grant-tables=1，这行配置会使mysql启动时不对密码进行校验

*   **重启MySQL**

systemctl restart mysqld.service

*   **登录到MySQL（无密码）**

mysql -uroot -p

*   **修改密码（和上面一样）**

*   **更新user和privilige表（和上面一样）**

* * *

最简单的方法还是直接安装**宝塔**，提供了多种工具的一键安装和管理
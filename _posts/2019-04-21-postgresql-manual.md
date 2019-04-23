---
layout: post
title: "PostgreSQL 使用手册"
---
一直听别人说PostgreSQL很好用，最近起了一个新项目决定尝试一下。一开始使用的时候很不适应，遇到很多坑记录如下。

## 安装PostgreSQL
在Mac上可以通过homebrew安装postgresql，可在命令行执行如下命令：
```
brew install postgresql
```
## 启动PostgreSQL Server
在命令行执行以下命令：
```
pg_ctl -D /usr/local/var/postgres start
```
## 常用命令
* createdb [DB NAME] 创建数据库
* dropdb [DB NAME] 删除数据库
* createuser [USER NAME] 创建用户
* dropuser [USER NAME] 删除用户
* psql \du 列出所有用户
* psql \l 列出所有数据库
* psql \c [DBNAME] 连接到指定数据库
* psql \dt 列出所有数据库表，举例：\dt public.*
* psql \q 离开psql

## 引用

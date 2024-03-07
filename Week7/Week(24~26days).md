
## 1. PostgreSQL 和 MySQL 的优劣势

| MySQL                        | PostgreSQL                                    |
| ---------------------------- | --------------------------------------------- |
| MySQL 社区版使用 GPL 许可证          | 使用 PostgreSQL 许可证，类似于 MIT 许可证                 |
| MySQL 的查询优化器更适合处理简单和中等复杂度的查询 | PostgreSQL 能够更好地处理复杂查询                        |
| InnoDB 表的数据行和索引行按照主键的顺序存储在一起 | 数据行和索引行是分开存储的                                 |
| 表添加新列，需要重建表和索引               | 表添加新列，PostgreSQL 在数据字典中增加表的定义                 |
| 仅支持 SQL 存储过程语言               | 支持 PL/PgSQL、PL/Tcl、PL/Perl 和 PL/Python 存储过程语言 |
| 不支持 Sequence 对象              | 支持 Sequence 对象                                |
| 不支持物化视图                      | 支持物化视图                                        |
| 执行计划是会话级别的                   | 执行计划是全局共享的                                    |
| 不支持用户自定义类型和域限制               | 支持用户自定义类型和域限制                                 |
| 仅支持内部的身份验证机制（插件）             | 支持操作系统级别的身份验证方式                               |
| DDL 操作是非事务性的                 | 几乎所有 DDL 操作都是事务性的                             |

## 2. 安装 PostgreSQL

### 2.1 二进制安装

创建仓库配置

```bash
root@pgsql:~# echo "deb https://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list
```

导入仓库签名密钥

```bash
root@pgsql:~# wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
```

更新软件包列表

```bash
root@pgsql:~# apt update
```

安装 PostgreSQL

```bash
root@pgsql:~# apt install postgresql-16
```

查看服务状态

```bash
root@pgsql:~# systemctl is-active postgresql@16-main.service
active
```

编辑 `/etc/postgresql/16/main/postgresql.conf` 文件

```bash
#listen_addresses = 'localhost'
listen_addresses = '*'
```

重启服务

```bash
root@pgsql:~# systemctl restart postgresql@16-main.service
```

连接数据库

```bash
root@pgsql:~# su - postgres
postgres@pgsql:~$ psql
psql (16.2 (Ubuntu 16.2-1.pgdg22.04+1))
Type "help" for help.

# 查看连接信息
postgres=# \conninfo
You are connected to database "postgres" as user "postgres" via socket in "/var/run/postgresql" at port "5432".
```
### 2.2 编译安装

安装依赖包

```bash
root@pgsql:~# apt update 
root@pgsql:~# apt install gcc make libreadline-dev zlib1g-dev libicu-dev pkg-config
```

下载并解压源码包

```bash
root@pgsql:~# wget https://ftp.postgresql.org/pub/source/v16.0/postgresql-16.0.tar.gz
root@pgsql:~# tar xf postgresql-16.0.tar.gz
```

编译安装

```bash
root@pgsql:~# cd postgresql-16.0
root@pgsql:~/postgresql-16.0# ./configure
root@pgsql:~/postgresql-16.0# make && make install
```

创建 postgres 用户

```bash
root@pgsql:~# useradd -u 2000 -m -d /home/postgres -s /bin/bash postgres
```

创建数据目录并修改所有者和所属组

```bash
root@pgsql:~# mkdir -p /pgsql/data
root@pgsql:~# chown -R postgres: /pgsql/data
```

创建 `/etc/profile.d/postgres.sh` 文件，设置环境变量

```bash
export PGDATA=/pgsql/data
export PATH=$PATH:/usr/local/pgsql/bin
```

```bash
root@pgsql:~# source /etc/profile.d/postgres.sh
```

初始化数据库

```bash
root@pgsql:~# su - postgres
postgres@pgsql:~$ initdb
```

编辑 `/pgsql/data/postgresql.conf` 文件

```bash
#listen_addresses = 'localhost'
listen_addresses = '*'
```

启动服务

```bash
postgres@pgsql:~$ pg_ctl -l logfile start
waiting for server to start.... done
server started
```

查看端口监听状态

```bash
postgres@pgsql:~$ ss -tnpl
State       Recv-Q      Send-Q           Local Address:Port           Peer Address:Port     Process
LISTEN      0           200                    0.0.0.0:5432                0.0.0.0:*         users:(("postgres",pid=25984,fd=6))
LISTEN      0           4096             127.0.0.53%lo:53                  0.0.0.0:*
LISTEN      0           128                    0.0.0.0:22                  0.0.0.0:*
LISTEN      0           200                       [::]:5432                   [::]:*         users:(("postgres",pid=25984,fd=7))
LISTEN      0           128                       [::]:22                     [::]:*
```

连接数据库

```bash
postgres@pgsql:~$ psql
psql (16.0)
Type "help" for help.

postgres=# \conninfo
You are connected to database "postgres" as user "postgres" via socket in "/tmp" at port "5432".
```

创建 `/lib/systemd/system/postgresql16.service` 文件

```bash
[Unit]
Description=PostgreSQL database server
After=network.target

[Service]
User=postgres
ExecStart=/usr/local/pgsql/bin/postgres -D /pgsql/data
ExecStop=/usr/local/pgsql/bin/pg_ctl stop -D /pgsql/data -m fast
ExecReload=/usr/local/pgsql/bin/pg_ctl reload -D /pgsql/data

[Install]
WantedBy=multi-user.target
```

重启服务

```bash
root@pgsql:~# killall postgres
root@pgsql:~# systemctl daemon-reload
root@pgsql:~# systemctl enable --now postgresql16.ser
root@pgsql:~# systemctl is-active postgresql16.service
active
```

## 3. 总结 PostgreSQL 服务管理相关命令 pg_ctl 和 pgsql 命令选项及示例，以及不同系统的初始化操作

### 3.1 pg_ctl

常用选项
- `-D`：数据目录
- `-l`：写入的服务器日志文件
- `-m`：停止服务的模式。smart 表示等待所有客户端断开连接后退出，fast 表示直接退出，immediate 表示立即退出，不进行完整的关机操作，将导致数据库在重启时进行恢复操作

启动服务

```bash
root@pgsql:~# su - postgres
postgres@pgsql:~$ pg_ctl -l logfile start
waiting for server to start.... done
server started
```

查看服务状态

```bash
postgres@pgsql:~$ pg_ctl -l logfile status
pg_ctl: server is running (PID: 14861)
/usr/local/pgsql/bin/postgres
```

停止服务

```bash
postgres@pgsql:~$ pg_ctl -m smart stop
waiting for server to shut down.... done
server stopped

postgres@pgsql:~$ pg_ctl -l logfile status
pg_ctl: no server running
```

### 3.2 初始化数据

Rocky

```bash
[root@rocky ~]# /usr/pgsql-16/bin/postgresql-16-setup initdb
```

Ubuntu

```bash
root@pgsql:~# /usr/lib/postgresql/16/bin/initdb
```

## 4. PostgreSQL 对象层次结构

![](https://github.com/zqhgithubuser/Homework/blob/main/Week7/images/Pasted_image_20240306150923.png)

## 5. 实现 PostgreSQL 远程连接。免密登录和密码登录

### 5.1 免密登录

编辑 `/pgsql/data/pg_hba.conf` 文件，修改以下配置

```bash
# IPv4 local connections:
#host    all             all             127.0.0.1/32            trust
host    all             all             0.0.0.0/0               trust
```

重启服务

```bash
root@pgsql:~# systemctl restart postgresql16.service
```

远程登录，无需输入密码

```bash
root@pgsql:~# su - postgres
postgres@pgsql:~$ psql -h 10.0.0.150
psql (16.0)
Type "help" for help.

postgres=# \conninfo
You are connected to database "postgres" as user "postgres" on host "10.0.0.150" at port "5432".
```

### 5.2 密码登录

设置用户密码

```postgresql
postgres=# ALTER USER postgres ENCRYPTED PASSWORD '123456';
ALTER ROLE
```

编辑 `/pgsql/data/pg_hba.conf` 文件，修改以下配置

```bash
host    all             all             0.0.0.0/0               scram-sha-256
```

重启服务

```bash
root@pgsql:~# systemctl restart postgresql16.service
```

远程登录，需要输入密码

```bash
postgres@pgsql:~$ psql -h 10.0.0.150
Password for user postgres:
psql (16.0)
Type "help" for help.

postgres=# \conninfo
You are connected to database "postgres" as user "postgres" on host "10.0.0.150" at port "5432".
```

## 6. 总结库，模式，表的创建和删除操作。表数据的 CRUD。以及相关信息查看语句

### 6.1 库

创建库

```postgresql
postgres=# CREATE DATABASE testdb;
CREATE DATABASE
```

切换库

```postgresql
postgres=# \c testdb
You are now connected to database "testdb" as user "postgres".

testdb=# \c
You are now connected to database "testdb" as user "postgres".
```

查看库信息

```postgresql
postgres=# \l testdb
List of databases
-[ RECORD 1 ]-----+------------
Name              | testdb
Owner             | postgres
Encoding          | UTF8
Locale Provider   | libc
Collate           | en_US.UTF-8
Ctype             | en_US.UTF-8
ICU Locale        |
ICU Rules         |
Access privileges |
```

查看库对应的目录名

```postgresql
postgres=# SELECT oid, datname FROM pg_database;
  oid  |  datname
-------+-----------
     5 | postgres
 16388 | testdb
     1 | template1
     4 | template0
(4 rows)
```

```bash
postgres@pgsql:~$ ls /var/lib/postgresql/16/main/base/
1  16388  4  5
```

删除库

```postgresql
postgres=# DROP DATABASE testdb;
DROP DATABASE
```

### 6.2 模式

>一个库包含一个或多个模式，模式是数据库对象的命名集合，包含表、视图、数据类型、函数、存储过程和操作符。在不同的模式中对象可以重名

列出模式

```postgresql
postgres=# \dn
      List of schemas
  Name  |       Owner
--------+-------------------
 public | pg_database_owner
(1 row)
```

创建模式

```postgresql
postgres=# CREATE SCHEMA test_schema;
CREATE SCHEMA
```

删除模式

```postgresql
postgres=# DROP SCHEMA test_schema;
DROP SCHEMA
```

### 6.3 表

查看支持的数据类型

```postgresql
postgres=# SELECT typname FROM pg_type;
```

创建表

```postgresql
CREATE TABLE links (
  id SERIAL PRIMARY KEY,
  url VARCHAR(255) NOT NULL,
  name VARCHAR(255) NOT NULL,
  description VARCHAR (255),
  last_update DATE
);
```

列出表

```postgresql
postgres=# \dt
         List of relations
 Schema | Name  | Type  |  Owner
--------+-------+-------+----------
 public | links | table | postgres
(1 row)
```

```postgresql
postgres=# SELECT * FROM pg_catalog.pg_tables
           WHERE tablename = 'links';
 schemaname | tablename | tableowner | tablespace | hasindexes | hasrules | hastriggers | rowsecurity
------------+-----------+------------+------------+------------+----------+-------------+-------------
 public     | links     | postgres   |            | t          | f        | f           | f
(1 row)
```

查看表的状态信息

```postgresql
postgres=# SELECT * FROM pg_stat_all_tables WHERE relname = 'links';
-[ RECORD 1 ]-------+------------------------------
relid               | 16405
schemaname          | public
relname             | links
seq_scan            | 1
last_seq_scan       | 2024-03-06 05:06:59.407286+00
seq_tup_read        | 0
idx_scan            | 0
last_idx_scan       |
idx_tup_fetch       | 0
n_tup_ins           | 0
n_tup_upd           | 0
n_tup_del           | 0
n_tup_hot_upd       | 0
n_tup_newpage_upd   | 0
n_live_tup          | 0
n_dead_tup          | 0
n_mod_since_analyze | 0
n_ins_since_vacuum  | 0
last_vacuum         |
last_autovacuum     |
last_analyze        |
last_autoanalyze    |
vacuum_count        | 0
autovacuum_count    | 0
analyze_count       | 0
autoanalyze_count   | 0
```

查看表结构

```postgresql
postgres=# \d links
```

插入行

```postgresql
postgres=# INSERT INTO links (url, name, last_update)
postgres-# VALUES('https://www.google.com','Google','2013-06-01');
INSERT 0 1
```

查看表数据

```postgresql
postgres=# SELECT * FROM links;
-[ RECORD 1 ]-----------------------
id          | 1
url         | https://www.google.com
name        | Google
description |
last_update | 2013-06-01
```

更新行

```postgresql
postgres=# UPDATE links
postgres-# SET last_update = CURRENT_DATE
postgres-# WHERE id = 1;
UPDATE 1

postgres=# SELECT * FROM links;
-[ RECORD 1 ]-----------------------
id          | 2
url         | https://www.google.com
name        | Google
description |
last_update | 2024-03-06
```

删除行

```postgresql
postgres=# DELETE FROM links
postgres-# WHERE id = 2;
DELETE 1

postgres=# SELECT * FROM links;
(0 rows)
```

删除表

```postgresql
postgres=# DROP TABLE links;
DROP TABLE
```

## 7. PostgreSQL 的用户和角色管理

### 7.1 创建用户和角色

>USER 和 ROLE 的区别是 `CREATE USER` 创建的用户默认可以登录

常用选项
- `SUPERUSER|NOSUPERUSER`：定义角色是否是超级用户，可以覆盖数据库内的所有访问限制
- `CREATEDB|NOCREATEDB`：定义角色是否具有创建数据库的权限
- `CREATEROLE|NOCREATEROLE`：定义角色是否允许创建、修改、删除其他非超级用户
- `INHERIT|NOINHERIT`：该角色作为另一个角色的成员时，是否继承其权限
- `LOGIN|NOLOGIN`：定义角色是否被允许登录
- `REPLICATION|NOREPLICATION`：定义角色是否为复制角色
- `CONNECTION LIMIT <connlimit`：
- `[ENCRYPTED] PASSWORD <password>`：设置角色密码
- `VALID UNTIL <timestamp>`：设置角色密码有效期

创建用户

```postgresql
postgres=# CREATE USER zhangsan with ENCRYPTED PASSWORD '123456';
CREATE ROLE

postgres=# \du zhangsan
     List of roles
 Role name | Attributes
-----------+------------
 zhangsan  |
```

禁止用户登录

```postgresql
postgres=# ALTER USER zhangsan WITH NOLOGIN;
ALTER ROLE
```

```bash
postgres@pgsql:~$ psql -U zhangsan
psql: error: connection to server on socket "/tmp/.s.PGSQL.5432" failed: FATAL:  role "zhangsan" is not permitted to log in
```

删除用户

```postgresql
postgres=# DROP USER zhangsan;
DROP ROLE
```

### 7.2 权限管理

权限
- `ALL`：所有权限
- `SELECT`：允许选择表、视图、物化视图等类似表的对象的任意列
- `INSERT`：允许向表、视图等插入新行。可以对指定列授权
- `UPDATE`：允许更新表、视图等的任意列（任何复杂的 `UPDATE` 命令都需要 `SELECT` 权限）
- `DELETE`：允许删除表、视图等中的行（任何复杂的 `DELETE` 命令都需要 `SELECT` 权限）
- `TRUNCATE`：允许清空表
- `REFERENCES`：允许创建引用表或表指定列的外键
- `TRIGGER`：允许在表、视图等上创建触发器
- `CREATE`：对于数据库，允许在数据库中创建新的模式；对于模式，允许在模式中创建新的对象；对于表空间，允许在表空间中创建表、索引和临时文件
- `CONNECT`：允许被授权者连接到数据库
- `TEMPORARY`：允许创建临时表
- `EXECUTE`：允许调用函数和存储过程
- `SET`：允许在当前会话中设置服务器配置参数
- `ALTER SYSTEM`：允许使用 `ALTER SYSTEM` 设置服务器配置参数

创建表

```bash
postgres@pgsql:~$ psql -U postgres
```

```postgresql
create table temp (
  id int generated always as identity,
  first_name varchar(100) not null,
  last_name varchar(100) not null,
  primary key(id)
);
```

使用 `zhangsan` 登录

```bash
postgres@client:~$ psql -U zhangsan postgres
```

查看表数据，权限拒绝

```postgresql
postgres=> SELECT * FROM temp;
ERROR:  permission denied for table temp
```

授予`zhangsan` 用户 `SELECT` 权限

```bash
postgres@pgsql:~$ psql -U postgres
```

```postgresql
postgres=# GRANT SELECT ON temp TO zhangsan;
GRANT
```

用户 `zhangsan` 执行 `SELECT` 语句

```postgresql
postgres=> SELECT * FROM temp;
 id | first_name | last_name
----+------------+-----------
(0 rows)
```

## 8. 创建用户 mage，模式 magedu，库 zabbix，配置用户 mage 的默认模式为 magedu，用户 mage 拥有 zabbix 库的所有权限

创建用户 mage

```postgresql
postgres=# CREATE USER mage WITH ENCRYPTED PASSWORD '123456';
CREATE ROLE

postgres=# \du
                             List of roles
 Role name |                         Attributes
-----------+------------------------------------------------------------
 mage      |
 postgres  | Superuser, Create role, Create DB, Replication, Bypass RLS
```

创建模式 magedu

```postgresql
postgres=# CREATE SCHEMA magedu;
CREATE SCHEMA

postgres=# \dn
      List of schemas
  Name  |       Owner
--------+-------------------
 magedu | postgres
 public | pg_database_owner
(2 rows)
```

配置用户 mage 的默认模式为 magedu

```postgresql
postgres=# ALTER ROLE mage SET search_path TO magedu;
ALTER ROLE
```

```postgresql
postgres=> \c
You are now connected to database "postgres" as user "mage".
postgres=> SHOW search_path;
 search_path
-------------
 magedu
(1 row)
```

创建库 zabbix

```postgresql
postgres=# CREATE DATABASE zabbix;
CREATE DATABASE

postgres=# \l zabbix
List of databases
-[ RECORD 1 ]-----+------------
Name              | zabbix
Owner             | postgres
Encoding          | UTF8
Locale Provider   | libc
Collate           | en_US.UTF-8
Ctype             | en_US.UTF-8
ICU Locale        |
ICU Rules         |
Access privileges |
```

配置用户 mage 拥有 zabbix 库的所有权限

```postgresql
postgres=# GRANT ALL ON DATABASE zabbix to mage;
GRANT

postgres=# \l zabbix
List of databases
-[ RECORD 1 ]-----+----------------------
Name              | zabbix
Owner             | postgres
Encoding          | UTF8
Locale Provider   | libc
Collate           | en_US.UTF-8
Ctype             | en_US.UTF-8
ICU Locale        |
ICU Rules         |
Access privileges | =Tc/postgres         +
                  | postgres=CTc/postgres+
                  | mage=CTc/postgres
```

## 9. PostgreSQL 的进程结构，进程间是如何协同工作的

### 9.1 进程

```bash
postgres@pgsql:~$ ps axfo pid,cmd | grep postgres
   3006 /usr/lib/postgresql/16/bin/postgres -D /var/lib/postgresql/16/main -c config_file=/etc/postgresql/16/main/postgresql.conf
   3007  \_ postgres: 16/main: checkpointer
   3008  \_ postgres: 16/main: background writer
   3010  \_ postgres: 16/main: walwriter
   3011  \_ postgres: 16/main: autovacuum launcher
   3012  \_ postgres: 16/main: logical replication launcher
   3033  \_ postgres: 16/main: postgres postgres 10.0.0.150(52368) idle
```

- Postgres Server 进程
	- 数据库集群管理相关的所有进程的父进程，之前被称为 "postmaster"
	- 执行 `pg_ctl start` 命令可以启动该进程
	- 然后，它申请一块共享内存区域，启动大量辅助进程，接着等待客户端的连接请求
	- 接收到客户端的连接请求后，它会启动一个 backed 进程，负责处理客户端的所有查询
- Backend 进程
	- 它通过 TCP 连接与客户端通信，当客户端断开连接时被终止
	- 客户端发送连接请求时需要指定操作的库
- 辅助进程
	- **checkpointer**：在特定的时间间隔内将所有脏缓冲写入磁盘，并创建检查点用于数据恢复
	- **background writer**：定期将部分脏缓冲写入磁盘，确保有足够的干净数据页，减少 checkpointer 的 I/O 操作
	- **wal writer**：定期将 WAL 缓冲刷新到磁盘
	- **autovacuum launcher**：定期向主进程请求创建自动清理工作进程，清理数据库中的旧版本数据
	- **archiver**：将 WAL 文件复制到指定目录，避免被新的 WAL 文件覆盖
	- **stats collector**：收集数据库运行过程中的统计信息，例如表的增删改查次数、数据块的数量、索引的变化等
	- **wal sender** 和 **wal receiver**：wal sender 将 WAL 发送到从服务器，wal 从主服务器获取 WAL

### 9.2 内存

**本地内存区域**：由每个 backend 进程分配，用于处理查询，分为具有固定或变化大小的子区域

work_mem：写入临时磁盘文件前执行内部排序和哈希操作使用的内存

```postgresql
postgres=# SHOW work_mem;
 work_mem
----------
 4MB
(1 row)
```

maintenacne_work_mem：执行维护操作（例如 VACUUM、CREATE INDEX、ALTER TABLE）使用的内存

```postgresql
postgres=# SHOW maintenance_work_mem;
 maintenance_work_mem
----------------------
 64MB
(1 row)
```

temp_buffers：用于访问临时表的临时缓冲区

```postgresql
postgres=# SHOW temp_buffers;
 temp_buffers
--------------
 8MB
(1 row)
```

**共享内存区域**

PostgreSQL 服务启动时申请的一块内存区域，以提高读写性能。共享内存中还存在 WAL 缓冲区和 CLog 缓冲区，以及一些全局信息，例如进程信息、锁信息、全局统计信息等

共享缓冲区：存放经常访问的数据页

```postgresql
postgres=# SHOW shared_buffers;
 shared_buffers
----------------
 128MB
(1 row)
```

WAL 缓冲区：存放未写入磁盘的 WAL 数据

```postgresql
postgres=# SHOW wal_buffers;
 wal_buffers
-------------
 4MB
(1 row)
```

Clog 缓冲区：记录事务提交状态，由数据库内部自动管理

### 9.3 数据更新过程

![](https://github.com/zqhgithubuser/Homework/blob/main/Week7/images/Pasted_image_20240306235114.png)

1. 从磁盘读取要修改的数据到共享缓冲区
2. 在共享缓冲区中更新数据
3. 将更新操作写入 WAL 缓冲区
4. 将 WAL 缓冲区的更新操作写入 WAL 文件，确保 WAL 的持久化
5. 将更新后的数据页刷新到磁盘，确保数据的持久化
## 10. PostgreSQL 的数据目录结构，说明每个文件的作用

|PGDATA|描述|
|---|---|
|global|包含集群范围的数据库对象|
|base|包含每个库对应的目录|
|pg_commit_ts|包含事务提交时间戳|
|pg_dynshmem|包含动态共享内存子系统使用的文件|
|pg_logical|包含逻辑复制相关的文件|
|pg_multixact|包含多事务状态数据|
|pg_notify|包含 LISTEN/NOTIFY 状态数据|
|pg_replslot|包含复制槽数据|
|pg_serial|包含与已提交的可串行化事务相关的信息|
|pg_snapshots|包含被导出的快照|
|pg_stat|包含用于统计子系统的永久文件|
|pg_stat_tmp|包含用于统计子系统的临时文件|
|pg_subtrans|包含子事务状态数据|
|pg_tblspc|包含表空间物理位置的软连接|
|pg_twophase|包含双阶段事务的状态文件|
|pg_wal|包含 WAL 文件|
|pg_xact|包含事务提交状态|
|postmaster.opts|启动服务的命令行选项|
|postmaster.pid|记录 postmaster 的 PID、集群数据目录，postmaster 进程启动的时间，端口号，Unix 套接字文件所在目录，监听的 IP 地址，共享内存段 ID|


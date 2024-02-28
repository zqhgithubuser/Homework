
## 1. 关系型数据库相关概念

- 关系（Relation）：关系是数据库中的表，用于存储数据
- 行（Row）：行是关系中的一条记录，每一行表示一个实体或对象，包含多列数据
- 列（Column）：列是关系中的一个字段，也称为属性
- 主键（Primary Key）：主键是关系中的一列或多列的组合，用于唯一标识每一行。主键的值在关系中是唯一且非空的
- 唯一键（Unique Key）：唯一键是关系中的一列或多列的组合，用于唯一标识每一行。唯一键的值可以为空
- 域（Domain）：域是字段值的取值范围

## 2. 联系类型

- 一对一：一个人只有一张身份证，每张身份证只属于一个人
- 一对多：一个人可以拥有多张银行卡，但每张银行卡只属于一个人
- 多对多：一个客户可以购买任意数量的产品，并且每个产品可以被任意数量的客户购买

## 3. 数据库范式

### 3.1 第一范式（1NF）

1 NF 要求表中的每个单元格都包含原子值，并且每一行都是被唯一标识的

示例：`Phome Numbers` 列中有包含多个值的单元格

|Customer ID|Name|Phone Numbers|
|---|---|---|
|1|John|555-1234, 555-5678|
|2|Jane|555-9876|
|3|Michael|555-5555|

为了使该表满足 1NF，可以将 `Phone Numbers` 列的值拆分到单独的行

|Customer ID|Name|Phone Number|
|---|---|---|
|1|John|555-1234|
|1|John|555-5678|
|2|Jane|555-9876|
|3|Michael|555-5555|

### 3.2 第二范式（2NF）

2NF 建立在 1NF 的基础上，要求表中的每个非主键列在功能上完全依赖于主键，不能仅依赖主键的一部分

示例：`Customer Name` 仅依赖于 `Customer ID`，`Item Name` 仅依赖于 `Item ID`

|Order ID|Customer ID|Customer Name|Item ID|Item Name|Quantity|
|---|---|---|---|---|---|
|1|1|John|1|Shirt|2|
|1|1|John|2|Pants|1|
|2|2|Jane|1|Shirt|1|
|2|2|Jane|3|Hat|3|

为了使该表满足 2NF，可以将它拆分为四张表

|Order ID|Item ID|Quantity|
|---|---|---|
|1|1|2|
|1|2|1|
|2|1|1|
|2|3|3|

|Customer ID|Customer Name|
|---|---|
|1|John|
|2|Jane|

|Item ID|Item Name|
|---|---|
|1|Shirt|
|2|Pants|
|3|Hat|

|Customer ID|
|---|
|1|
|2|

### 3.3 第三范式（3NF）

3NF 建立在 2NF 的基础上，要求表中的每个非主键列不依赖于其它非主键列

示例：非主键列 `CUstomer Name`、`Customer City` 依赖非主键列 `Customer ID`

|Order ID|Customer ID|Customer Name|Customer City|Order Date|Order Total|
|---|---|---|---|---|---|
|1|100|John Smith|New York|2022-01-01|100|
|2|101|Jane Doe|Los Angeles|2022-01-02|200|
|3|102|Bob Johnson|San Francisco|2022-01-03|300|
为了使该表满足 3NF，可以将它拆分为两张表

|Customer ID|Customer Name|Customer City|
|---|---|---|
|100|John Smith|New York|
|101|Jane Doe|Los Angeles|
|102|Bob Johnson|San Francisco|

|Order ID|Customer ID|Order Date|Order Total|
|---|---|---|---|
|1|100|2022-01-01|100|
|2|101|2022-01-02|200|
|3|102|2022-01-03|300|
## 4. 安装 MySQL，执行安全加固脚本，总结 MySQL 配置文件。将 server 和 client 的默认字符集配置为 utf8mb4

### 4.1 包管理器

**MySQL8.0（Rocky8）**

安装 MySQL

```bash
[root@rocky ~]# wget https://repo.mysql.com//mysql80-community-release-el8-9.noarch.rpm
[root@rocky ~]# rpm -ivh mysql80-community-release-el8-9.noarch.rpm
[root@rocky ~]# yum module -y disable mysql
[root@rocky ~]# yum install mysql-community-server
```

启动服务

```bash
[root@rocky ~]# systemctl enable --now mysqld
```

查看临时密码

```bash
[root@rocky ~]# grep 'temporary password' /var/log/mysqld.log
2024-02-20T04:08:27.441174Z 6 [Note] [MY-010454] [Server] A temporary password is generated for root@localhost: gj7YjAewnT?e
```

登录 MySQL

```bash
[root@rocky ~]# mysql -uroot -p'gj7YjAewnT?e'
```

修改密码

```mysql
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'P@ssw0rd!';
Query OK, 0 rows affected (0.00 sec)

mysql> exit
Bye
```

**MySQL8.0（Ubuntu22.04）**

安装 MySQL

```bash
$ wget https://repo.mysql.com//mysql-apt-config_0.8.29-1_all.deb
$ dpkg -i mysql-apt-config_0.8.29-1_all.deb
$ apt update
$ apt install mysql-community-server
```

查看服务状态

```bash
$ systemctl is-active mysql
active
```

### 4.2 通用二进制包

**Rocky8**

安装脚本

```bash
#!/bin/bash

# https://mirrors.aliyun.com/mysql/MySQL-5.7/mysql-5.7.38-linux-glibc2.12-x86_64.tar.gz
# https://mirrors.aliyun.com/mysql/MySQL-8.0/mysql-8.0.28-linux-glibc2.12-x86_64.tar.xz

MYSQL_BINARY57="mysql-5.7.38-linux-glibc2.12-x86_64.tar.gz"
MYSQL_BINARY80="mysql-8.0.28-linux-glibc2.12-x86_64.tar.xz"
MYSQL_ROOT_PASSWORD="P@ssw0rd!"

check() {
    if (( $UID != 0 )); then
        echo "非root用户！"
        exit 1
    fi

    if [ ! -e $1 ]; then
        echo "请将$1下载到当前目录"
        exit
    elif [ -d /usr/local/mysql ]; then
        echo "/usr/local/mysql不是空目录！"
        exit
    else
        return
    fi
}

install_mysql() {
    echo "开始安装MySQL..."
    groupadd mysql && useradd -r -g mysql -s /bin/false mysql; echo "创建mysql用户！";
    yum -y install libaio numactl-libs ncurses-compat-libs
    tar xf $1 -C /usr/local
    MYSQL_DIR=$(echo $1 | sed -rn 's/^(.*[0-9]).*/\1/p')
    ln -s /usr/local/${MYSQL_DIR} /usr/local/mysql
    chown -R mysql: /usr/local/mysql

    echo 'PATH=$PATH:/usr/local/mysql/bin/' > /etc/profile.d/mysql.sh
    . /etc/profile.d/mysql.sh
    cat > /etc/my.cnf <<-EOF
    [mysqld]
    server-id=1
    log-bin=mysql-bin
    datadir=/data/mysql
    socket=/data/mysql/mysql.sock
    log-error=/data/mysql/mysql.log
    pid-file=/data/mysql/mysql.pid

    [client]
    socket=/data/mysql/mysql.sock
    EOF

    [ -d /data ] || mkdir /data
    mysqld --initialize --user=mysql --datadir=/data/mysql
    cp /usr/local/mysql/support-files/mysql.server /etc/init.d/mysqld
    chkconfig --add mysqld && chkconfig mysqld on
    service mysqld start && sleep 2
    (( $? != 0 )) && { echo "MySQL启动失败！"; exit; }
    MYSQL_OLD_PASSWORD=$(awk '/temporary password/{ print $NF }' /data/mysql/mysql.log)
    mysqladmin -uroot -p$MYSQL_OLD_PASSWORD password $MYSQL_ROOT_PASSWORD &> /dev/null
    echo "MySQL安装完成！"
}

select_version() {
    read -p "请选择MySQL版本(5.7|8.0): " version
    if [[ $version == "5.7" ]]; then
        check $MYSQL_BINARY57
        install_mysql $MYSQL_BINARY57
    elif [[ $version == "8.0" ]]; then
        check $MYSQL_BINARY80
        install_mysql $MYSQL_BINARY80
    else
        echo "请输入正确的版本！"
        exit
    fi
}

select_version
```

脚本执行结果

```bash
[root@rocky ~]# ./install_mysql.sh
请选择MySQL版本(5.7|8.0): 8.0
开始安装MySQL...
创建mysql用户！
Starting MySQL.. SUCCESS!
MySQL安装完成！
```

查看服务状态

```bash
[root@rocky ~]# service mysqld status
 SUCCESS! MySQL running (2235)
```

### 4.3 安全加固

是否修改密码

```bash
$ mysql_secure_installation
VALIDATE PASSWORD COMPONENT can be used to test passwords
and improve security. It checks the strength of password
and allows the users to set only those passwords which are
secure enough. Would you like to setup VALIDATE PASSWORD component?

Press y|Y for Yes, any other key for No: y

There are three levels of password validation policy:

LOW    Length >= 8
MEDIUM Length >= 8, numeric, mixed case, and special characters
STRONG Length >= 8, numeric, mixed case, special characters and dictionary                  file

Please enter 0 = LOW, 1 = MEDIUM and 2 = STRONG: 2
Please set the password for root here.

New password:

Re-enter new password:
```

是否删除匿名用户

```bash
By default, a MySQL installation has an anonymous user,
allowing anyone to log into MySQL without having to have
a user account created for them. This is intended only for
testing, and to make the installation go a bit smoother.
You should remove them before moving into a production
environment.

Remove anonymous users? (Press y|Y for Yes, any other key for No) : y
Success.
```

是否允许远程登录

```bash
Normally, root should only be allowed to connect from
'localhost'. This ensures that someone cannot guess at
the root password from the network.

Disallow root login remotely? (Press y|Y for Yes, any other key for No) : y
Success.
```

是否删除 `test` 库

```bash
Remove test database and access to it? (Press y|Y for Yes, any other key for No) : y
 - Dropping test database...
Success.

 - Removing privileges on test database...
Success.
```

是否重新加载权限

```bash
Reloading the privilege tables will ensure that all changes
made so far will take effect immediately.

Reload privilege tables now? (Press y|Y for Yes, any other key for No) : y
Success.
```

### 4.4 配置文件

| 文件名                       | 目的     |
| ------------------------- | ------ |
| /etc/my.cnf               | 全局选项   |
| /etc/mysql/my.cnf（Ubuntu） | 全局选项   |
| /etc/my.cnf.d/\*.cnf      | 全局选项   |
| ~/.my.cnf                 | 用户特定选项 |
>[!note]
>MySQL 服务器选项：
>- https://dev.mysql.com/doc/refman/5.7/en/server-option-variable-reference.html
>- https://dev.mysql.com/doc/refman/8.0/en/server-option-variable-reference.html

### 4.5 配置默认字符集

编辑 `/etc/my.cnf` 文件，添加以下配置

```bash
[mysqld]
character-set-server=utf8mb4
```

重启服务

```bash
[root@rocky ~]# systemctl restart mysqld
```

查看字符集设置

```mysql
mysql> SHOW VARIABLES LIKE 'character_set_results';
+-----------------------+---------+
| Variable_name         | Value   |
+-----------------------+---------+
| character_set_results | utf8mb4 |
+-----------------------+---------+
1 row in set (0.00 sec)
```

## 5. 基于 SQL 命令的帮助创建 testdb 库（字符集：utf8，排序集合：utf8_bin）。创建 host 表（字段：id，host，ip，cname）

### 5.1 创建 testdb 库

```mysql
mysql> \h CREATE DATABASE
Name: 'CREATE DATABASE'
Description:
Syntax:
CREATE {DATABASE | SCHEMA} [IF NOT EXISTS] db_name
    [create_option] ...

create_option: [DEFAULT] {
    CHARACTER SET [=] charset_name
  | COLLATE [=] collation_name
  | ENCRYPTION [=] {'Y' | 'N'}
}

mysql> CREATE DATABASE IF NOT EXISTS testdb
       CHARACTER SET utf8
       COLLATE utf8_bin;
Query OK, 1 row affected, 2 warnings (1.58 sec)
```

### 5.2 创建 host 表

```mysql
mysql> USE testdb;
Database changed

mysql> \h CREATE TABLE
Name: 'CREATE TABLE'
Description:
Syntax:
CREATE [TEMPORARY] TABLE [IF NOT EXISTS] tbl_name
    (create_definition,...)
    [table_options]
    [partition_options]
......

mysql> CREATE TABLE host (
       id INT UNSIGNED PRIMARY KEY AUTO_INCREMENT,
       host VARCHAR(25),
       ip VARCHAR(15) NOT NULL,
       cname VARCHAR(25)
       );
Query OK, 0 rows affected (0.06 sec)
```

## 6. 演示 DDL，DML 语句的用法

### 6.1 DDL 语句

查看表结构

```mysql
mysql> DESC host;
+-------+--------------+------+-----+---------+----------------+
| Field | Type         | Null | Key | Default | Extra          |
+-------+--------------+------+-----+---------+----------------+
| id    | int unsigned | NO   | PRI | NULL    | auto_increment |
| host  | varchar(25)  | YES  |     | NULL    |                |
| ip    | varchar(15)  | NO   |     | NULL    |                |
| cname | varchar(25)  | YES  |     | NULL    |                |
+-------+--------------+------+-----+---------+----------------+
4 rows in set (0.00 sec)
```

查看创建表的语句

```mysql
mysql> SHOW CREATE TABLE host\G
*************************** 1. row ***************************
       Table: host
Create Table: CREATE TABLE `host` (
  `id` int unsigned NOT NULL AUTO_INCREMENT,
  `host` varchar(25) COLLATE utf8mb3_bin DEFAULT NULL,
  `ip` varchar(15) COLLATE utf8mb3_bin NOT NULL,
  `cname` varchar(25) COLLATE utf8mb3_bin DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb3 COLLATE=utf8mb3_bin
1 row in set (0.00 sec)
```

查看表状态

```mysql
mysql> SHOW TABLE STATUS LIKE 'host'\G
*************************** 1. row ***************************
           Name: host
         Engine: InnoDB
        Version: 10
     Row_format: Dynamic
           Rows: 0
 Avg_row_length: 0
    Data_length: 16384
Max_data_length: 0
   Index_length: 0
      Data_free: 0
 Auto_increment: 1
    Create_time: 2024-02-20 06:43:03
    Update_time: NULL
     Check_time: NULL
      Collation: utf8mb3_bin
       Checksum: NULL
 Create_options:
        Comment:
1 row in set (0.00 sec)
```

### 6.2 DML 语句

插入记录

```mysql
/* 所有列 */
mysql> INSERT INTO host VALUES (1, 'www.example.com', '10.0.0.150', 'web.example.com');
Query OK, 1 row affected (0.00 sec)

/* 部分列 */
mysql> INSERT INTO host (ip) VALUES ('10.0.0.160');
Query OK, 1 row affected (0.00 sec)

mysql> SELECT * FROM host;
+----+-----------------+------------+-----------------+
| id | host            | ip         | cname           |
+----+-----------------+------------+-----------------+
|  1 | www.example.com | 10.0.0.150 | web.example.com |
|  2 | NULL            | 10.0.0.160 | NULL            |
+----+-----------------+------------+-----------------+
2 rows in set (0.00 sec)
```

修改记录

```mysql
mysql> UPDATE host SET host = 'db.example.com' WHERE id = 2;
Query OK, 1 row affected (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> SELECT * FROM host;
+----+-----------------+------------+-----------------+
| id | host            | ip         | cname           |
+----+-----------------+------------+-----------------+
|  1 | www.example.com | 10.0.0.150 | web.example.com |
|  2 | db.example.com  | 10.0.0.160 | NULL            |
+----+-----------------+------------+-----------------+
2 rows in set (0.00 sec)
```

删除记录

```mysql
mysql> DELETE FROM host WHERE id = 1;
Query OK, 1 row affected (0.00 sec)

mysql> SELECT * FROM host;
+----+----------------+------------+-------+
| id | host           | ip         | cname |
+----+----------------+------------+-------+
|  2 | db.example.com | 10.0.0.160 | NULL  |
+----+----------------+------------+-------+
1 row in set (0.00 sec)
```

## 7. MySQL 架构原理

![](https://github.com/zqhgithubuser/Homework/blob/main/Week6/images/Pasted_image_20240227215925.png)

客户端层
- 连接处理：客户端向服务端发送连接请求，尝试与服务端建立连接
- 身份验证：连接建立成功后，服务端会验证用户的身份
- 安全性：用户通过身份验证后，服务端会查询用户的权限并进行授权。至此，服务端会分配一个单独的线程。该线程负责执行客户端的查询

服务端层
- SQL 接口：连接建立成功后，负责处理客户端的查询
- 解析器：解析器执行词法分析、语法分析和语义分析，随后生成一个内部结构（解析树）
- 优化器：解析完成后，优化器部分会应用不同的优化技术。例如重写查询、重新排列表的顺序、选择正确的索引等。将解析转换为完整的查询执行计划。线程根据执行计划，调用存储引擎提供的 API
- 读取缓存（MySQL 8.0 移除）：将一些经常执行的 SQL 语句查询结果保存在缓存中
- 写入缓冲：在数据库中读取某页数据操作时，会先将从磁盘读到的页存放在缓冲区中，后续操作相同页的时候，可以基于内存操作

存储引擎层
- MySQL 可以根据不同的情况使用不同类型的存储引擎。包括 InnoDB、MyISAM、Memory 等。它们作为插件实现

## 8. 总结 MyISAM 和 InnoDB 存储引擎的区别

| MyISAM             | InnoDB             |
| ------------------ | ------------------ |
| 不支持事务              | 支持事务               |
| 不支持 ACID 特性        | 支持 ACID 特性         |
| 表级锁                | 行级锁                |
| 不支持外键约束            | 支持外键约束             |
| 读取性能更好，适合读取密集型工作负载 | 写入性能更好，适合写入密集型工作负载 |
| 崩溃恢复性较好            | 无崩溃恢复机制            |
| 仅支持缓存索引，支持全文搜索     | 支持缓存数据和索引，不支持全文搜索  |
| 不支持多版本并发控制（MVCC）   | 支持多版本并发控制（MVCC）    |
| 非聚集索引              | 支持聚集索引、二级索引        |

## 9. MySQL 索引的作用，哪些查询不会使用索引

通常，MySQL 数据库执行查询时，必须逐行扫描表中的所有行。随着表大小的增加，查询可能会变得非常慢且占用资源。要解决大表查询的性能问题，可以使用索引。索引是一种数据结构，单独存储特定列数据的排序子集，包含指向对应行的指针，能挺高数据库引擎的检索速度

不使用索引的查询
- 索引的选择性（索引列的唯一值数量 / 总行数）很低
- 表中的数据量很小或查询涉及表中的大部分行
- 以通配符（% 或 \_）开头的模糊搜索
- 查询条件中不包含复合索引（多列索引）的最左列
- 查询条件中包含未建立索引的列的 `OR` 子句
- 查询条件中的数据类型与索引列不匹配
- 查询条件总的索引列被包装在函数中

## 10. 事务的 ACID 特性

- **原子性**（Atomicity）：原子性确保事务被视为单个不可分割的单元。事务中的所有操作要么全部执行成功，要么全部执行失败。如果事务在执行过程中的发生错误，所有已执行的操作都必须回滚到事务开始前的状态
- **一致性**（Consistency）：一致性确保事务执行前后数据库都处于一致的状态。事务中的操作必须满足数据库的约束条件以确保数据的一致性
- **隔离性**（Isolation）：隔离性确保多个事务可以并发执行而不会互相干扰。每个事务不能看到其它事务的中间结果，避免数据不一致等问题
- **持久性**（Durability）：持久性确保事务一旦提交，对数据的修改就是永久性的，即使在系统发生故障或重启后也能保存下来

## 11. 事务日志工作原理

InnoDB 撤销日志（Undo Log）
- **维护原始数据副本**：每当事务修改 InnoDB 中的数据时，撤销日志会记录原始数据的副本，以便在需要时回滚更改
- **事务隔离**：它确保在一个事务内，其它并发事务可以通过存储在撤销日志中的原始数据副本读取一致的数据
- **崩溃恢复**：如果系统崩溃或重启，撤销日志会根据记录的原始数据副本执行必要的撤销操作，从而使数据库恢复到一致的状态

InnoDB 重做日志（Redo Log）
- **记录更改**：每当事务修改 InnoDB 中的数据时，重做日志会记录执行的修改
- **预写日志**：在更新数据前先将更改写入重做日志
- **崩溃恢复**：通过应用重做日志中的更改，InnoDB 可以恢复已提交但未写入磁盘的事务

## 12. MySQL 日志类型，说明如何启动日志

### 12.1 错误日志

**错误日志**（Error Log）：记录 MySQL 服务启动、关闭和运行过程中的诊断和错误消息

创建错误日志文件

```bash
[root@rocky ~]# touch /var/log/mysql.err
[root@rocky ~]# chown mysql: /var/log/mysql.err
```

编辑 `/etc/my.cnf` 文件，添加以下配置

```mysql
[mysqld]
log_error=/var/log/mysql.err
```

重启服务

```bash
[root@rocky ~]# systemctl restart mysqld
```

验证

```mysql
mysql> SHOW VARIABLES LIKE 'log_error';
+---------------+--------------------+
| Variable_name | Value              |
+---------------+--------------------+
| log_error     | /var/log/mysql.err |
+---------------+--------------------+
1 row in set (0.04 sec)
```

### 12.2 通用查询日志

**通用查询日志**（General Query Log）：记录客户端发送的每条 SQL 语句（执行 SQL 语句前）

默认关闭

```mysql
mysql> SHOW VARIABLES LIKE 'general_log%';
+------------------+------------------------------+
	| Variable_name    | Value                        |
+------------------+------------------------------+
| general_log      | OFF                          |
| general_log_file | /var/lib/mysql/rocky.log     |
+------------------+------------------------------+
2 rows in set (0.00 sec)
```

创建通用查询日志文件

```bash
[root@rocky ~]# touch /var/log/mysql/rocky.log
[root@rocky ~]# chown mysql: /var/log/mysql/general.log
```

编辑 `/etc/my.cnf` 文件，添加以下配置

```mysql
[mysqld]
general_log = ON
general_log_file = /var/log/mysql/general.log
```

重启服务

```bash
[root@rocky ~]# systemctl restart mysqld
```

### 12.3 二进制日志

**二进制日志**（Binary Log）：记录修改表数据或表结构的事件（事务被提交后）

默认配置

```mysql
mysql> SHOW VARIABLES LIKE 'log_bin%';
+---------------------------------+-----------------------------+
| Variable_name                   | Value                       |
+---------------------------------+-----------------------------+
| log_bin                         | ON                          |
| log_bin_basename                | /var/lib/mysql/binlog       |
| log_bin_index                   | /var/lib/mysql/binlog.index |
| log_bin_trust_function_creators | OFF                         |
| log_bin_use_v1_row_events       | OFF                         |
+---------------------------------+-----------------------------+
5 rows in set (0.00 sec)
```

### 13.4 中继日志

**中继日志**（Relay Log）：副本服务器（replica）从源服务器（source）的二进制日志中读取事件并将其写入中继日志

```mysql
mysql> SHOW VARIABLES LIKE 'relay_log';
+---------------+---------------------+
| Variable_name | Value               |
+---------------+---------------------+
| relay_log     | rocky-relay-bin     |
+---------------+---------------------+
1 row in set (0.00 sec)
```

### 13.5 慢查询日志

**慢查询日志**（Slow Query Log）：记录执行时间超过设定阈值的 SQL 查询语句

默认配置

```mysql
mysql> SHOW VARIABLES LIKE 'slow_query_log%';
+---------------------+-----------------------------------+
| Variable_name       | Value                             |
+---------------------+-----------------------------------+
| slow_query_log      | OFF                               |
| slow_query_log_file | /var/lib/mysql/rocky-slow.log     |
+---------------------+-----------------------------------+
2 rows in set (0.01 sec)
```

编辑 `/etc/my.cnf` 文件，添加以下配置

```bash
[mysqld]
slow_query_log = ON
slow_query_log_file = /var/lib/mysql/rocky-slow.log 
```

## 13. 二进制日志的不同格式的使用场景

- STATEMENT 格式：记录 SQL 语句。适用于简单的修改操作，某些情况下无法确保数据的一致性，写入效率相对较高
- ROW 格式：记录事件。适用于复杂的修改操作，占用存储空间大，使用安全，写入效率相对较低
- MIXED 格式：默认情况下记录 SQL 语句，特定情况下记录事件。结合了 STATEMENT 和 ROW 格式的优点

## 14. 总结 MySQL 备份类型，基于 mysqldump 和 XtraBackup 完成数据库备份与恢复

### 14.1 备份类型

按备份范围分类
- 完全备份：备份整个数据集
- 增量备份：备份自上次备份以来发生变化的数据
- 差异备份：备份自上次完全备份以来发生变化的数据

按备份过程中数据库的状态分类
- 冷备份：数据库处于停止状态下进行的备份
- 温备份：数据库处于只读状态下进行的备份，不允许写入操作
- 热备份：数据库处于运行状态下进行的备份，允许写入操作

按备份方式分类
- 逻辑备份：将数据库中的数据和对象以逻辑方式导出为可读的文本格式，例如 SQL 语句或 CSV 文件。适用于跨数据库实例和跨数据库系统的数据迁移
- 物理备份：复制数据库文件，包括数据文件、日志文件、索引文件等。相对更为快速和高效

### 14.2 mysqldump

**备份单个库**

备份

```bash
root@mysql1:~# mysqldump --triggers --routines --events --flush-privileges --hex-blob --flush-logs --single-transaction --databases testdb > dump.sql

root@mysql1:~# scp dump.sql root@10.0.0.155:/root
```

- `--triggers`：默认选项，备份触发器
- `--routines`：备份函数和存储过程
- `--events`：备份事件
- `--flush-privileges`： `mysql` 库备份完成后执行 `FLUSH PRIVILEGES` 语句
- `--hex-blob`：使用十六进制格式备份二进制字符串
- `--flush-logs`：开启备份前刷新二进制日志文件
- `--single-transaction`：在单个事务中备份所有表。确保数据的一致性

恢复

```bash
root@mysql2:~# mysql < dump.sql
root@mysql2:~# mysql testdb -e "SHOW TABLES"
+------------------+
| Tables_in_testdb |
+------------------+
| host             |
+------------------+
```

**备份整个数据库实例**

备份

```bash
root@mysql1:~# mysqldump --all-databases --routines --events --flush-privileges --hex-blob --flush-logs --source-data=2 --single-transaction | gzip > dump.sql.gz

root@mysql1:~# scp dump.sql.gz root@10.0.0.155:/root
```

- `--source-data`：值为 1 时在备份文件中添加一条 `CHANGE MASTER TO` 语句，包含二进制日志文件名和位置。值为 2 注释该语句。不同时指定 `--single-transaction` 会开启 `--lock-all-tables` 选项，`--master-data` 在未来将被弃用

恢复

```bash
root@mysql2:~# mysql -e "DROP DATABASE testdb"
root@mysql2:~# gzip -d dump.sql.gz
root@mysql2:~# mysql < dump.sql
root@mysql2:~# mysql testdb -e "SHOW TABLES"
+------------------+
| Tables_in_testdb |
+------------------+
| host             |
+------------------+
```

### 14.3 XtraBackup

**基础备份**

备份

```bash
root@mysql1:~# xtrabackup --backup --target-dir=/tmp/backup 
root@mysql1:~# scp -r /tmp/backup root@10.0.0.155:/tmp/
```

恢复

```bash
# --prepare: 回滚未完成的事务
root@mysql2:~# xtrabackup --prepare --target-dir=/tmp/backup 
root@mysql2:~# systemctl stop mysql.service
root@mysql2:~# mv /var/lib/mysql /var/lib/mysql_old
root@mysql2:~# xtrabackup --copy-back --target-dir=/tmp/backup --datadir=/var/lib/mysql
root@mysql2:~# chown -R mysql: /var/lib/mysql
root@mysql2:~# chmod o+rx /var/lib/mysql
root@mysql2:~# systemctl start mysql.service
root@mysql2:~# mysql testdb -e "SHOW TABLES"
+------------------+
| Tables_in_testdb |
+------------------+
| host             |
+------------------+
```

**增量备份**

插入新的记录

```mysql
mysql> USE testdb;
Database changed

mysql> INSERT INTO host (host, ip) VALUES ('test.example.com', '10.0.0.200');
Query OK, 1 row affected (1.64 sec)

mysql> SELECT * FROM host;
+----+------------------+------------+-------+
| id | host             | ip         | cname |
+----+------------------+------------+-------+
|  2 | db.example.com   | 10.0.0.160 | NULL  |
|  3 | test.example.com | 10.0.0.200 | NULL  |
+----+------------------+------------+-------+
2 rows in set (0.00 sec)
```

备份

```bash
root@mysql1:~# xtrabackup --backup \
                 --incremental-basedir=/tmp/backup \
                 --target-dir=/tmp/inc_backup1 
root@mysql1:~# scp -r /tmp/backup root@10.0.0.155:/tmp/
root@mysql1:~# scp -r /tmp/inc_backup1 root@10.0.0.155:/tmp/
```

恢复

```bash
# --apply-log-only: 将增量备份日志应用到基础备份中，但不会将更改写入磁盘
root@mysql2:~# xtrabackup --prepare --apply-log-only \
                 --target-dir=/tmp/backup

root@mysql2:~# xtrabackup --prepare \
                 --target-dir=/tmp/backup \
                 --incremental-dir=/tmp/inc_backup1

root@mysql2:~# systemctl stop mysql.service
root@mysql2:~# mv /var/lib/mysql /var/lib/mysql_old_1
root@mysql2:~# xtrabackup --target-dir=/tmp/backup --copy-back --datadir=/var/lib/mysql
root@mysql2:~# chown -R mysql: /var/lib/mysql
root@mysql2:~# chmod o+rx /var/lib/mysql
root@mysql2:~# systemctl start mysql.service
root@mysql2:~# mysql testdb -e "SELECT * FROM host"
+----+------------------+------------+-------+
| id | host             | ip         | cname |
+----+------------------+------------+-------+
|  2 | db.example.com   | 10.0.0.160 | NULL  |
|  3 | test.example.com | 10.0.0.200 | NULL  |
+----+------------------+------------+-------+
```

## 15. 编写计划任务，每天按表备份所有 MySQL 数据。将备份数据放在以天为时间的目录下。基于 XtraBackup，每周一、周五进行完全备份，周二到周四进行增量备份

第一次完全备份（周一）

```bash
root@mysql1:~# xtrabackup --backup --target-dir=/backup/mysql_$(date -d '-1 days' +%F)
# 2024-02-19 -> 周一
root@mysql1:~# ls /backup
mysql_2024-02-19
```

创建计划任务

```bash
root@mysql1:~# crontab -l
0 2 * * 1,5        xtrabackup --backup --target-dir=/backup/mysql_$(date +\%F)
0 2 * * 2-4        xtrabackup --backup --incremental-basedir=/backup/mysql_$(date -d '-1 days' +\%F) --target-dir=/backup/mysql_inc_$(date +\%F)
```

插入新的记录

```mysql
mysql> USE testdb;
Database changed

mysql> INSERT INTO host VALUES (NULL, 'server1.example.com', '10.0.0.100', NULL);
Query OK, 1 row affected (0.04 sec)

mysql> SELECT * FROM host;
+----+---------------------+------------+-------+
| id | host                | ip         | cname |
+----+---------------------+------------+-------+
|  2 | db.example.com      | 10.0.0.160 | NULL  |
|  3 | test.example.com    | 10.0.0.200 | NULL  |
|  4 | server1.example.com | 10.0.0.100 | NULL  |
+----+---------------------+------------+-------+
3 rows in set (0.00 sec)
```

查看目录下是否有增量备份

```bash
root@mysql1:~# du -sh mysql_2024-02-19
71M     mysql_2024-02-19
root@mysql1:~# du -sh mysql_inc_2024-02-20
2.1M    mysql_inc_2024-02-20
```

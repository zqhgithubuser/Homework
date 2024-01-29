
## 1. SSH 服务安全加固，基于密钥登录的原理及实现

### 1.1 SSH 安全加固

编辑 `/etc/ssh/sshd_config` 文件，修改以下配置

```bash
ListenAddress 10.0.0.150              # 指定监听的地址
PermitRootLogin no                    # 不允许 root 使用 SSH 登录
MaxSessions 10                        # 每个用户允许的最大会话数量
MaxAuthTries 3                        # 每个连接允许的最大身份验证尝试次数
PermitEmptyPasswords no               # 不允许使用空密码登录
PasswordAuthentication no             # 不允许使用密码进行身份验证
ClientAliveInterval 100               # 服务端每 100s 向客户端发送一次保持连接消息
ClientAliveCountMax 3                 # 服务端发送 3 次保持连接消息后终止会话
```

重启 SSH 服务

```bash
[root@rocky ~]# systemctl restart sshd
```

### 1.2 基于密钥登录的原理

1. 客户端生成密钥对，将公钥保存到服务端的 `$HOME/.ssh/authorized_keys` 文件中
2. 客户端向服务端发送一个 SSH 登录请求
3. 服务端检查 `$HOME/.ssh/authorized_keys` 文件中是否存在与客户端发送的公钥匹配的公钥。如果找到匹配的公钥，使用客户端的公钥加密一个随机数，并发送给客户端
4. 客户端收到后，使用私钥进行解密，并使用该随机数以及会话 ID 生成一个 MD5 哈希值
5. 客户端将生成的哈希值进行加密，将其发送给服务端
6. 服务器也生成一个 MD5 哈希值，将两个哈希值进行比较。如果结果一致，证明客户端拥有对应的私钥，允许客户端登录

### 1.3 基于密钥登录的实现

客户端生成密钥对

```bash
zqh@ubuntu:~$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/zqh/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/zqh/.ssh/id_rsa
Your public key has been saved in /home/zqh/.ssh/id_rsa.pub
The key fingerprint is:
SHA256:0NuAe0bsB5KjWrXDPNr+OhvP5K9WFqe50hoR2rFCJTc zqh@ubuntu
The key's randomart image is:
+---[RSA 3072]----+
|      . E        |
|       O .       |
|      O O        |
|     * @ O. .    |
|    o X S o=     |
|   o o * o=      |
|  . . o o+ .     |
|     ..*o.o      |
|      +*B=.      |
+----[SHA256]-----+
```

将公钥复制到服务器

```bash
zqh@ubuntu:~$ ssh-copy-id root@10.0.0.150
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/zqh/.ssh/id_rsa.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
root@10.0.0.150's password:

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'root@10.0.0.150'"
and check to make sure that only the key(s) you wanted were added.
```

尝试登录

```bash
zqh@ubuntu:~$ ssh root@10.0.0.150
Activate the web console with: systemctl enable --now cockpit.socket

Last login: Wed Jan 24 17:42:30 2024 from 10.0.0.1
[root@rocky ~]#
```

## 2. sudo 配置文件格式

配置文件：`/etc/sudoers` 和 `/etc/sudoers.d/*`

基本的授权规则格式：

```bash
User     Host=(Runas)   Cmnd
```

定义别名

```bash
# User_Alias
User_Alias      WEBMASTERS = will, wendy, wim

# Host_Alias
Host_Alias      CSNETS = 128.138.243.0, 128.138.204.0/24, 128.138.242.0
Host_Alias      SERVERS = master, mail, www, ns

# Runas_Alias
Runas_Alias     OP = root, operator

# Cmnd_Alias
Cmnd_Alias      REBOOT = /usr/sbin/reboot
Cmnd_Alias      PAGERS = /usr/bin/more, /usr/bin/pg, /usr/bin/less
```

配置示例

```bash
# 允许任意主机上的 wheel 组中的用户以任意用户的身份执行任意命令
%wheel          ALL = (ALL) ALL

# 允许主机 HPPA 上的 pete 用户修改除 root 外的任意用户的密码
Host_Alias      HPPA = boa, nag, python
pete            HPPA = /usr/bin/passwd [A-Za-z]*, !/usr/bin/passwd *root*

# 允许 opers 组中的用户以 adm 或 oper 组成员的身份执行 /usr/sbin/ 目录下的任意命令
Runas_Alias     ADMINGRP = adm, oper
%opers          ALL = (: ADMINGRP) /usr/sbin/

# 允许主机 www 上的 WEBMASTER 用户以 www 用户的身份执行任意命令，以 root 用户的身份执行 su www 命令
User_Alias      WEBMASTERS = will, wendy, wim
WEBMASTERS      www = (www) ALL, (root) /usr/bin/su www
```

## 3. PAM 架构及工作原理

### 3.1 架构

![](https://github.com/zqhgithubuser/Homework/blob/main/Week5/images/Pasted_image_20240129171004.png)

PAM API 与 PAM 库进行通信，PAM 模块通过接口与 PAM 库进行通信。因此 PAM 库使得应用程序和模块之间能够间接通信

### 3.2  工作原理

1. 应用程序调用 PAM API，例如 `passwd` 命令
2. PAM 库加载`/etc/pam.d` 下对应的PAM 配置文件
3. 调用 `/etc/security` 目录下的 PAM 模块提供的接口进行验证
## 4. PAM 配置文件格式

配置文件：`/etc/pam.d/*`

格式：

```bash
type    control    module-path module-arguments
```

type（模块接口类型）
- `account`：验证是否允许用户访问。例如检查用户账户是否过期，或者是否被允许在特定时间登录
- `auth`：验证用户身份，例如验证密码的有效性
- `password`：更改身份验证令牌。通常是密码
- `session`：管理和配置用户会话。记录用户登录尝试并配置用户的特定环境（邮件、家目录、系统限制等）
- `-<type>`：系统中缺少该模块接口时，PAM 库将不会记录到系统日志中
control
- `required`：模块的结果必须成功，才能继续进行身份验证。如果在此阶段测试失败，用户将在所有引用该接口的模块测试完成之后收到通知
- `requisite`：模块的结果必须成功，才能继续进行身份验证。如果在此阶段测试失败，用户将立即收到一条 `requisite` 接口测试失败的消息
- `sufficient`：如果模块结果失败，则被忽略。如果被标记为 `sufficient` 的接口成功，并且之前没有被标记为 `required` 的接口失败，则不需要其它结果即可认证成功
- `optional`：模块结果通常被忽略。只有当没有其它模块引用该接口时，才对成功的身份验证起到必要作用
- `include`：提取其它配置文件的所有行
module-path：模块文件路径
module-arguments：模块参数

**pam_nologin.so 模块**

 功能：当 `/var/run/nologin` 或 `/etc/nologin` 文件存在时，普通用户将无法登录系统

```
root@ubuntu:~# echo 'Login denied.' > /etc/nologin
```

尝试登录

```bash
[root@rocky ~]# ssh zqh@10.0.0.151
zqh@10.0.0.151's password:
Login denied.

Connection closed by 10.0.0.151 port 22
[root@rocky ~]#
```

**pam_limits.so 模块**

功能：限制用户能够获得的系统资源

配置文件：`/etc/security/limits.conf` 和 `/etc/security/limits.d/*`

格式：

```bash
<domain> <type> <item> <value>
```

domain
- 用户名
- 组名（`@group`）
- 通配符 `*`，不包括 root
- 通配符 `%`：仅用于最大登录限制
type
- `hard`：执行硬限制
- `soft`：执行软限制。用户可以暂时超过软限制
- `-`：同时执行软限制和硬限制
item
- `core`：核心转储文件大小
- `data`：进程数据段的最大大小
- `fsize`：文件的最大大小
- `memlock`：进程可以锁定在内存中的地址空间大小
- `nofile`：进程可以打开的文件描述符的最大数量
- `stack`：进程栈的最大大小
- `cpu`：进程可以使用的最大 CPU 时间
- `nproc`：用户可以创建的最大进程数
- `as`：进程的地址空间大小
- `maxlogins`：一个用户可以同时登录的最大数量（对 root 无效）
- `maxsyslogins`：系统上所有用户的最大登录数量（对 root 无效）
- `priority`：用户进程运行的优先级
- `locks`：进程可以锁定文件的最大数量
- `sigpending`：进程可以挂起的待处理信号的最大数量
- `msgqueue`：POSIX 消息队列可以使用的最大内存大小
- `nice`：非特权进程可以提高的最大 nice 优先级值
- `rtprio`：非特权进程可以使用的最大实时优先级

编辑 `/etc/security/limits.conf` 文件，添加以下内容

```bash
*                soft    core            0
*                hard    core            unlimited
*                -       nproc           655350
*                -       nofile          655350
*                -       memlock         32000
*                hard    msgqueue        8192000
```

重新登录，查看当前的限制

```bash
[root@rocky ~]# ulimit -a
core file size          (blocks, -c) 0
data seg size           (kbytes, -d) unlimited
scheduling priority             (-e) 0
file size               (blocks, -f) unlimited
pending signals                 (-i) 6752
max locked memory       (kbytes, -l) 32000
max memory size         (kbytes, -m) unlimited
open files                      (-n) 655350
pipe size            (512 bytes, -p) 8
POSIX message queues     (bytes, -q) 819200
real-time priority              (-r) 0
stack size              (kbytes, -s) 8192
cpu time               (seconds, -t) unlimited
max user processes              (-u) 655350
virtual memory          (kbytes, -v) unlimited
file locks                      (-x) unlimited
```

## 5. 实现私有时钟同步服务器

### 5.1 环境准备

|角色|主机名|IP 地址|
|---|---|---|
|私有时钟同步服务器 |rocky|10.0.0.150|
|NTP 客户端 |ubuntu|10.0.0.151
### 5.2 服务端配置

安装软件包

```bash
[root@rocky ~]# yum install -y chrony
```

编辑 `/etc/chrony.conf` 文件，找到以下行

```bash
pool 2.rocky.pool.ntp.org iburst

# Allow NTP client access from local network.
#allow 192.168.0.0/16

# Serve time even if not synchronized to a time source.
#local stratum 10
```

修改为

```bash
# pool 2.rocky.pool.ntp.org iburst
server ntp.aliyun.com iburst        # iburst: 加快时钟同步
server ntp.tencent.com iburst

# Allow NTP client access from local network.
allow 10.0.0.0/24

# Serve time even if not synchronized to a time source.
local stratum 10          # 相对于实时参考时钟（GPS、原子钟等）的层级
```

重启服务

```bash
[root@rocky ~]# systemctl restart chronyd
```

显示当前的时间源信息

```bash
[root@rocky ~]# chronyc sources -v

  .-- Source mode  '^' = server, '=' = peer, '#' = local clock.
 / .- Source state '*' = current best, '+' = combined, '-' = not combined,
| /             'x' = may be in error, '~' = too variable, '?' = unusable.
||                                                 .- xxxx [ yyyy ] +/- zzzz
||      Reachability register (octal) -.           |  xxxx = adjusted offset,
||      Log2(Polling interval) --.      |          |  yyyy = measured offset,
||                                \     |          |  zzzz = estimated error.
||                                 |    |           \
MS Name/IP address         Stratum Poll Reach LastRx Last sample
===============================================================================
^+ 203.107.6.88                  2   8   377   174  -3970us[-3540us] +/-   42ms
^* 106.55.184.199                2   7   257   169  +2714us[+3144us] +/-   36ms
```

### 5.3 客户端配置

安装软件包

```bash
zqh@ubuntu:~$ sudo apt install -y chrony
```

编辑 `/etc/chrony/chrony.conf` 文件，找到以下行

```bash
pool ntp.ubuntu.com        iburst maxsources 4
pool 0.ubuntu.pool.ntp.org iburst maxsources 1
pool 1.ubuntu.pool.ntp.org iburst maxsources 1
pool 2.ubuntu.pool.ntp.org iburst maxsources 2
```

修改为

```bash
# pool ntp.ubuntu.com        iburst maxsources 4
# pool 0.ubuntu.pool.ntp.org iburst maxsources 1
# pool 1.ubuntu.pool.ntp.org iburst maxsources 1
# pool 2.ubuntu.pool.ntp.org iburst maxsources 2
server 10.0.0.150 iburst
```

重启服务

```bash
zqh@ubuntu:~$ sudo systemctl restart chrony
```

显示当前的时间源信息

```bash
zqh@ubuntu:~$ chronyc sources -v

  .-- Source mode  '^' = server, '=' = peer, '#' = local clock.
 / .- Source state '*' = current best, '+' = combined, '-' = not combined,
| /             'x' = may be in error, '~' = too variable, '?' = unusable.
||                                                 .- xxxx [ yyyy ] +/- zzzz
||      Reachability register (octal) -.           |  xxxx = adjusted offset,
||      Log2(Polling interval) --.      |          |  yyyy = measured offset,
||                                \     |          |  zzzz = estimated error.
||                                 |    |           \
MS Name/IP address         Stratum Poll Reach LastRx Last sample
===============================================================================
^* 10.0.0.150                    3   6   377     4   -181us[-1384us] +/-   36ms
```

## 6. DNS 域名分层结构

- 根域
- 顶级域（TLD）
	- 通用顶级域（gTLD）：`com`、`net`、`org`、`edu`、`gov`、`mil` 等
	- 国家/地区代码顶级域（ccTLD）：`cn`、`us`、`hk` 等
- 二级域（SLD）：www.baidu.com 中 `baidu` 是二级域
- 低级别域：www.tsinghua.edu.cn 中 `tsinghua` 是三级域

## 7. DNS 工作原理

![](https://github.com/zqhgithubuser/Homework/blob/main/Week5/images/Pasted_image_20240128140819.png)

DNS 查询使用 UDP 协议 53 端口

- PC 向 DNS 解析器发送一个**递归查询**消息（①），请求解析器返回一个准确的答案
- DNS 解析器接收到用户的递归查询后，如果答案在其缓存中，则立刻返回结果；否则，它会访问 DNS 层次结构以获取答案。解析器从根服务器开始，发送**迭代查询**请求（②、③ 和 ④）
- DNS 解析器最终将查询结果返回给用户

## 8. 实现私有 DNS 服务器

|角色|主机名|IP 地址|
|---|---|---|
|主 DNS 服务器|dns-master|10.0.0.150|
|Web 服务器|nginx|10.0.0.151|
|客户端|client|10.0.0.152|
### 8.1 主 DNS 服务器配置

安装软件包

```bash
[root@dns-master ~]# yum install -y bind bind-utils
```

编辑 `/etc/named.conf` 文件，找到以下行

```bash
options {
    listen-on port 53 { 127.0.0.1; };
    ......
    allow-query     { localhost; };
    recursion yes;
    
    dnssec-enable yes;
    dnssec-validation yes;
};
```

修改为

```bash
options {
    // listen-on port 53 { 127.0.0.1; };
    // listen-on-v6 port 53 { ::1; };
    ......
    allow-query     { any; };
    recursion yes;
    
    dnssec-enable no;
    dnssec-validation no;
};
```

启动服务

```bash
[root@dns-master ~]# systemctl enable --now named
Created symlink /etc/systemd/system/multi-user.target.wants/named.service → /usr/lib/systemd/system/named.service.
```

查看端口监听状态

```bash
[root@dns-master ~]# ss -nupl | grep named
UNCONN 0      0          10.0.0.150:53         0.0.0.0:*    users:(("named",pid=3831,fd=513))
UNCONN 0      0           127.0.0.1:53         0.0.0.0:*    users:(("named",pid=3831,fd=512))
......
```

编辑 `/etc/named.rfc1912.zones` 文件，添加以下内容

```bash
zone "zqh.com" {
    type master;
    file "zqh.com.zone";
};
```

创建 `/var/named/zqh.com.zone` 文件，添加以下内容

```bash
$TTL    1D
@       IN      SOA     zqh.com. root.zqh.com. (
                              0         ; Serial
                             1D         ; Refresh
                             1H         ; Retry
                             1W         ; Expire
                             3H )       ; Negative Cache TTL

@       IN      NS      ns.zqh.com.
ns      IN      A       10.0.0.150
www     IN      A       10.0.0.151
```

修改文件权限和所属组

```bash
[root@dns-master named]# chmod 640 zqh.com.zone
[root@dns-master named]# chgrp named zqh.com.zone
[root@dns-master named]# ll zqh.com.zone
-rw-r----- 1 root named 424 Jan 27 15:22 zqh.com.zone
```

检查区域文件语法和完整性

```bash
[root@dns-master named]# named-checkzone zqh.com /var/named/zqh.com.zone
zone zqh.com/IN: loaded serial 0
OK
```

重新加载配置

```bash
[root@dns-master named]# rndc reload
server reload successful
```

### 8.2 Web 服务器配置

安装软件包

```bash
zqh@nginx:~$ sudo apt install -y nginx

zqh@nginx:~$ systemctl is-active nginx
active
```

修改首页内容

```bash
zqh@nginx:~$ sudo sh -c 'echo "I am 10.0.0.151" > /var/www/html/index.nginx-debian.html'

zqh@nginx:~$ curl 127.0.0.1
I am 10.0.0.151
```

### 8.3 客户端配置

编辑 `/etc/netplan/00-installer-config.yaml` 文件，修改 DNS 服务器地址

```bash
network:
  ethernets:
    ens33:
      addresses:
      - 10.0.0.152/24
      nameservers:
        addresses:
        - 10.0.0.150
        search: []
      routes:
      - to: default
        via: 10.0.0.2
  version: 2
```

应用配置

```bash
zqh@client:~$ sudo netplan apply
```

测试

```bash
zqh@client:~$ nslookup www.zqh.com 10.0.0.150
Server:         10.0.0.150
Address:        10.0.0.150#53

Name:   www.zqh.com
Address: 10.0.0.151

zqh@nginx:~$  curl www.zqh.com
I am 10.0.0.151
```

## 9. DNS 服务器类型，正反向解析，解析答案，资源记录定义

### 9.1 DNS 服务器类型

- 主 DNS 服务器
- 从 DNS 服务器
- 解析器（缓存 DNS 服务器）

### 9.2 解析答案

- 权威答案：负责直接管理用户查询主机所在域的 DNS 记录的服务器，即权威 DNS 服务器返回的答案
- 非权威答案：非权威 DNS 服务器返回的答案。可能来自缓存 DNS 服务器
- 否定答案：查询相应的结果

### 9.3 正反向解析

- 正向解析：完全限定域名（FQDN）-> IP 地址
- 反向解析：IP 地址 -> FQDN

### 9.4 资源记录定义

`A`：地址记录

```bash
server1  IN  A  10.0.1.3
```

`CNAME`：别名记录

```bash
server1  IN  A      10.0.1.5
www      IN  CNAME  server1
```

`MX`：邮件交换记录。指定邮件服务器，需要指定优先级

```bash
example.com.  IN  MX  10  mail.example.com.
              IN  MX  20  mail2.example.com.
```

`NS`：域名服务器记录。声明权威 DNS 服务器

```bash
IN  NS  dns1.example.com.
IN  NS  dns2.example.com.
```
``
`PTR`：指针记录。用于反向域名解析

`SOA`：权威记录。声明重要的权威信息

```bash
@  IN  SOA  dns1.example.com.  hostmaster.example.com. (
       2001062501  ; Serial
       21600       ; Refresh
       3600        ; Retry 
       604800      ; Expire
       86400 )     ; Negative Cache TTL
```

- `@`：区域名
- `dns1.example.com`：当前区域主 DNS 服务器的主机名，起注释作用
- `hostmaster.example.com`：当前区域的联系人的邮箱地址
- `Serial`：修改区域文件时手动递增，表示 `named` 服务重新加载区域的时间
- `Refresh`：从 DNS 服务器向主 DNS 服务器查询是否对区域进行了任何更改之前等待的时间
- `Retry`：主 DNS 服务器不响应时再次发出刷新请求前等待的时间
- `Expire`：主 DNS 服务器在指定的时间内，没有回复刷新请求，从 DNS 服务器将停止响应用户请求
- `Negative Cache TTL`：否定答案的缓存时间。最长 3 小时

## 10 实现主从 DNS 服务器同步

|角色|主机名|IP 地址|
|---|---|---|
|主 DNS 服务器|dns-master|10.0.0.150|
|从 DNS 服务器|dns-slave|10.0.0.160|
|Web 服务器|nginx|10.0.0.151|
|客户端|client|10.0.0.152|
### 10.1 主 DNS 服务器配置

编辑 `/var/namd/zqh.com.zone` 文件，修改如下

```bash
$TTL    1D
@       IN      SOA     zqh.com. root.zqh.com. (
                              0         ; Serial
                             1D         ; Refresh
                             1H         ; Retry
                             1W         ; Expire
                             3H )       ; Negative Cache TTL

@       IN      NS      ns1.zqh.com.
@       IN      NS      ns2.zqh.com.
ns1     IN      A       10.0.0.150
ns2     IN      A       10.0.0.160
www     IN      A       10.0.0.151
```

重新加载配置

```bash
[root@dns-master ~]# rndc reload
server reload successful
```

### 10.2 从 DNS 服务器配置

安装软件包

```bash
[root@dns-slave ~]# yum install -y bind bind-utils
```

复制主 DNS 服务器的 `/etc/named.conf`

编辑 `/etc/named.rfc1912.zones` 文件，添加以下内容

```bash
zone "zqh.com" {
    type slave;
    masters { 10.0.0.150; };
    file "slaves/zqh.com.zone";
};
```

启动服务

```bash
[root@dns-slave ~]# systemctl enable --now named

[root@dns-slave ~]# ll /var/named/slaves/
total 4
-rw-r--r--. 1 named named 218 Jan 27 15:59 zqh.com.zone
```

### 10.3 客户端配置

编辑 `/etc/netplan/00-installer-config.yaml` 文件，修改 DNS 服务器地址

```bash
network:
  ethernets:
    ens33:
      addresses:
      - 10.0.0.152/24
      nameservers:
        addresses:
        - 10.0.0.150
        - 10.0.0.160
        search: []
      routes:
      - to: default
        via: 10.0.0.2
  version: 2
```

应用配置

```bash
zqh@client:~$ sudo netplan apply
```

测试

```bash
zqh@ubuntu-1:~$ nslookup www.zqh.com 10.0.0.160
Server:         10.0.0.160
Address:        10.0.0.160#53

Name:   www.zqh.com
Address: 10.0.0.151
```

## 11. 实现 DNS 子域授权

|角色|主机名|IP 地址|
|---|---|---|
|主 DNS 服务器|dns-master|10.0.0.150|
|从 DNS 服务器|dns-slave|10.0.0.160|
|子 DNS 服务器|dns-child|10.0.0.170|
|Web 服务器|nginx|10.0.0.151|
|客户端|client|10.0.0.152|
### 11.1 主 DNS 服务器配置

编辑 `/var/named/zqh.com.zone` 文件，修改如下

```bash
......
@       IN      NS      ns1.zqh.com.
@       IN      NS      ns1.zqh.com.
gd      IN      NS      gdns.zqh.com.      ; 子域: gd.zqh.com
ns1     IN      A       10.0.0.150
ns2     IN      A       10.0.0.160
gdns    IN      A       10.0.0.170
......
```

重新加载配置

```bash
[root@dns-master ~]# rndc reload
server reload successful
```

### 11.2 子 DNS 服务器配置

省略部分步骤

编辑 `/etc/named.rfc1912.zones` 文件，添加以下内容

```bash
zone "gd.zqh.com" {
    type master;
    file "gd.zqh.com.zone";
};
```

创建 `/var/named/gd.zqh.com.zone` 文件，添加以下内容

```bash
$TTL    1D
@       IN      SOA     gd.zqh.com. root.gd.zqh.com. (
                              0         ; Serial
                             1D         ; Refresh
                             1H         ; Retry
                             1W         ; Expire
                             3H )       ; Negative Cache TTL

@       IN      NS      ns.gd.zqh.com.
ns      IN      A       10.0.0.170
www     IN      A       10.0.0.153      ; www.gd.zqh.com
```

重新加载配置

```bash
[root@dns-child ~]# rndc reload
server reload successful
```

### 11.3 客户端配置

测试

```bash
zqh@client:~$ nslookup www.gd.zqh.com 10.0.0.150
Server:         10.0.0.150
Address:        10.0.0.150#53

Non-authoritative answer:
Name:   www.gd.zqh.com
Address: 10.0.0.153
```

## 12. 基于 acl 实现智能 DNS

|角色|主机名|IP 地址|
|---|---|---|
|DNS 服务器|dns|192.168.1.111, 192.168.100.111|
|Web 服务器|nginx1|192.168.1.222|
|Web 服务器|nginx2|192.168.100.222|
|客户端|client1|192.168.1.10|
|客户端|client2|192.168.100.10
### 12.1 DNS 服务器配置

安装软件包

```bash
[root@dns ~]# yum install -y bind bind-utils
```

编辑 `/etc/named.conf` 文件，内容如下

```bash
acl client_beijing {
    192.168.1.0/24;
};

acl client_shanghai {
    192.168.100.0/24;
};

acl client_other {
    any;
};

options {
        // listen-on port 53 { 127.0.0.1; };
        // listen-on-v6 port 53 { ::1; };
		......
        allow-query     { any; };

        recursion yes;

        dnssec-enable no;
        dnssec-validation no;
        ......
};

......

view beijing {
    match-clients { client_beijing; };
    include "/etc/named.rfc1912.zones.bj";
};

view shanghai {
    match-clients { client_shanghai; };
    include "/etc/named.rfc1912.zones.sh";
};

view other {
    match-clients { client_other; };
    include "/etc/named.rfc1912.zones.other";
};

include "/etc/named.root.key";
```

创建 `/etc/named.rfc1912.zones.bj` 文件，内容如下

```bash
zone "." IN {
    type hint;
    file "named.ca";
};

zone "zqh.com" {
    type master;
    file "zqh.com.zone.bj";
};
```

创建 `/etc/named.rfc1912.zones.sh` 文件，内容如下

```bash
zone "." IN {
    type hint;
    file "named.ca";
};

zone "zqh.com" {
    type master;
    file "zqh.com.zone.sh";
};
```

创建 `/etc/named.rfc1912.zones.other` 文件，内容如下

```bash
zone "." IN {
    type hint;
    file "named.ca";
};

zone "zqh.com" {
    type master;
    file "zqh.com.zone.other";
};
```

修改文件权限和所属组

```bash
[root@dns ~]# chmod 640 /etc/named.rfc1912.zones.*
[root@dns ~]# chgrp named /etc/named.rfc1912.zones.*
[root@dns ~]# ll /etc/named.rfc1912.zones.*
-rw-r----- 1 root named 119 Jan 27 22:13 /etc/named.rfc1912.zones.bj
-rw-r----- 1 root named 122 Jan 27 22:16 /etc/named.rfc1912.zones.other
-rw-r----- 1 root named 119 Jan 27 22:14 /etc/named.rfc1912.zones.sh
```

创建 `/var/named/zqh.com.zone.bj` 文件，内容如下

```bash
$TTL    1D
@       IN      SOA     zqh.com. root.zqh.com. (
                              0         ; Serial
                             1D         ; Refresh
                             1H         ; Retry
                             1W         ; Expire
                             3H )       ; Negative Cache TTL

@       IN      NS      ns.zqh.com.
ns      IN      A       192.168.1.111
www     IN      A       192.168.1.222
```

创建 `/var/named/zqh.com.zone.sh` 文件，内容如下

```bash
$TTL    1D
@       IN      SOA     zqh.com. root.zqh.com. (
                              0         ; Serial
                             1D         ; Refresh
                             1H         ; Retry
                             1W         ; Expire
                             3H )       ; Negative Cache TTL

@       IN      NS      ns.zqh.com.
ns      IN      A       192.168.100.111
www     IN      A       192.168.100.222
```

创建 `/var/named/zqh.com.zone.other` 文件，内容如下

```bash
$TTL    1D
@       IN      SOA     zqh.com. root.zqh.com. (
                              0         ; Serial
                             1D         ; Refresh
                             1H         ; Retry
                             1W         ; Expire
                             3H )       ; Negative Cache TTL

@       IN      NS      ns.zqh.com.
ns      IN      A       192.168.1.111
www     IN      A       127.0.0.1
```

启动服务

```bash
[root@dns ~]# systemctl enable --now named
Created symlink /etc/systemd/system/multi-user.target.wants/named.service → /usr/lib/systemd/system/named.service.
```

### 12.2 客户端配置

client1

```bash
root@client1:~# nslookup www.zqh.com 192.168.1.111
Server:         192.168.1.111
Address:        192.168.1.111#53

Name:   www.zqh.com
Address: 192.168.1.222
```

client2

```bash
root@client2:~# nslookup www.zqh.com 192.168.100.111
Server:         192.168.100.111
Address:        192.168.100.111#53

Name:   www.zqh.com
Address: 192.168.100.222
```

## 13. 防火墙分类

按保护范围划分
- 主机防火墙：保护范围为当前主机
- 网络防火墙：保护范围为整个局域网

按实现方式划分
- 硬件防火墙
- 软件防火墙

按网络协议划分
- 网络层防火墙：OSI 模型 1~4 层，又称为包过滤防火墙
- 应用层防火墙

## 14. iptables 5 表 5 链，基本使用，扩展模块

### 14.1 5 表 5 链

- PREROUTING：raw、mangle、nat
- INPUT：security、mangle、nat、filter
- FORWARD：security、mangle、filter
- OUTPUT：security、raw、mangle、nat、filter
- POSTROUTING：nat、mangle

>[!note]
>表优先级：security > raw > mangle > nat > filter

### 14.2 基本使用

常用选项
- `-t table`：指定操作的表，默认是 filter 表
- `-A chain`：追加规则
- `-D chain`：删除规则
- `-I chain [rulenum]`：插入规则
- `-R chain [rulenum]`：替换规则
- `-j`：指定 target。常用的 target 有 ACCEPT、DROP、REJECT 等
- `-L`：列出规则
- `-n`：输出显示 IP 地址和端口
- `-v`：详细模式
- `--line-numbers`：打印规则行号
- `-f`：删除所有规则

允许指定 IP 地址和回环网卡的入站流量，拒绝其它入站流量

```bash
[root@rocky ~]# iptables -A INPUT -s 10.0.0.1 -j ACCEPT
[root@rocky ~]# iptables -A INPUT -i lo -j ACCEPT
[root@rocky ~]# iptables -R INPUT 3 -s 0.0.0.0/0 -j DROP

[root@rocky ~]# iptables -nvL --line-number
Chain INPUT (policy ACCEPT 1085 packets, 64944 bytes)
num   pkts bytes target     prot opt in     out     source               destination
1      493 30076 ACCEPT     all  --  *      *       10.0.0.1             0.0.0.0/0
2        0     0 ACCEPT     all  --  lo     *       0.0.0.0/0            0.0.0.0/0
3        0     0 DROP       all  --  *      *       0.0.0.0/0            0.0.0.0/0

Chain FORWARD (policy ACCEPT 0 packets, 0 bytes)
num   pkts bytes target     prot opt in     out     source               destination

Chain OUTPUT (policy ACCEPT 1278 packets, 148K bytes)
num   pkts bytes target     prot opt in     out     source               destination
```

### 14.3 扩展模块

> man iptables-extensions

**tcp**

- `[!] --sport port[:port]`：源端口
- `[!] --dport port[:port]`：目的端口
- `tcp-flags mask comp`：标志位

```bash
[root@rocky ~]# iptables -A INPUT -s 10.0.0.150 -p tcp --dport 80 -j REJECT
```

**udp**
- `[!] --sport port[:port]`：源端口
- `[!] --dport port[:port]`：目的端口

**icmp**
- `[!] --icmp-type {type[/code]|typename}`：ICMP 类型

**multiport**

```bash
[root@rocky ~]# iptables -A INPUT -s 10.0.0.0/24 -p tcp -m multiport --dports 20:22, 80 -j ACCEPT
```

**iprange**

```bash
[root@rocky ~]# iptables -A INPUT -p tcp --dport 80 -m iprange --src-range 10.0.0.100-10.0.0.200 -j DROP
```

**connlimit**
- `--connlimit-upto n`：连接数小于等于 n
- `--connlimit-above n`：连接数大于 n

```bash
[root@rocky ~]# iptables -A INPUT -p tcp --dport 80 -m connlimit --connlimit-above 5 -j REJECT
```

**limit**
- `--limit-burst number`：最大初始数据包数量
- `--limit rate[/second|/minute|/hour|/day]`：最大平均速率

```bash
[root@rocky ~]# iptables -I INPUT -p icmp --icmp-type 8 -m limit --limit 10/minute --limit-burst 5 -j ACCEPT
```

**state**

连接状态
- INVALID：未知的连接
- NEW：新发起的连接
- ESTABLISHED：已建立的连接
- RELATED：请发起的连接，但与已存在的连接关联
- UNTRACKED：未进行追踪的连接

```bash
[root@rocky ~]# iptables -A INPUT -m state --state ESTABLISHED -j ACCEPT
# 拒绝请发起的连接
[root@rocky ~]# iptables -A INPUT -m state --state NEW -j REJECT
```

## 15. iptables 规则优化实践、规则保存和恢复

### 15.1 iptables 规则优化

1. 放行 ESTABLISHED 连接的规则建议放在第一条
2. 拒绝指定用户的规则，放在放行规则前面
3. 针对统一应用的规则，匹配范围小的放在前面
4. 针对不同应用的规则，匹配范围大的放在前面
5. 尽可能合并多条规则，减少规则数量，提高效率
6. 设置默认策略，例如默认拒绝所有放在最后一条

### 15.2 规则保存和恢复

安装软件包

```bash
[root@rocky ~]# yum -y install iptables-services
```

启动服务

```bash
[root@rocky ~]# systemctl disable --now firewalld.service
[root@rocky ~]# systemctl enable --now iptables.service
```

保存

```bash
[root@rocky ~]# iptables-save > /etc/sysconfig/iptables
```

开机自动加载 `etc/sysconfig/iptables` 文件中的规则

## 16. SNAT 和 DNAT 原理及实现

### 16.1 原理

- SNAT：修改请求数据包的源 IP 地址，记录连接跟踪信息，用于响应数据包的返回
- DNAT：修改请求数据包的目的 IP 地址，记录连接跟踪信息，用于响应数据包的返回

### 16.2 实现

![](https://github.com/zqhgithubuser/Homework/blob/main/Week5/images/Pasted_image_20240128231624.png)

|角色|主机名|IP 地址|
|---|---|---|
|防火墙|firewall|10.0.0.254, 192.168.10.111|
|内网服务器|internal-server|10.0.0.150|
|外网客户端|external-client|192.168.10.100|
|外网 Web 服务器|external-nginx|192.168.10.200

**SNAT**

Nginx 服务器配置

```bash
[root@external-nginx ~]# echo "I am 192.168.10.200." > /usr/share/nginx/html/index.html

[root@external-nginx ~]# curl 127.0.0.1
I am 192.168.10.200.
```

防火墙配置

```bash
# 开启 IP 转发
[root@firewall ~]# echo 'net.ipv4.ip_forward = 1' >> /etc/sysctl.conf
[root@firewall ~]# sysctl -p

[root@firewall ~]# iptables -t nat -F
[root@firewall ~]# iptables -t nat -A POSTROUTING -s 10.0.0.0/24 -p tcp --dport 80 -j SNAT --to-source 192.168.10.111

root@firewall ~]# iptables -t nat -nL
Chain POSTROUTING (policy ACCEPT)
target     prot opt source               destination
SNAT       tcp  --  10.0.0.0/24          0.0.0.0/0            tcp dpt:80 to:192.168.10.111
```

访问 Nginx 服务器

```bash
root@internal-server ~]# curl 192.168.10.200
I am 192.168.10.200.
```

查看网络连接跟踪信息

```bash
root@firewall ~]# cat /proc/net/nf_conntrack
ipv4     2 tcp      6 57 TIME_WAIT src=10.0.0.150 dst=192.168.10.200 sport=35902 dport=80 src=192.168.10.200 dst=192.168.10.111 sport=80 dport=35902 [ASSURED] mark=0 secctx=system_u:object_r:unlabeled_t:s0 zone=0 use=2
```

**DNAT**

防火墙配置

```bash
[root@firewall ~]# iptables -t nat -A PREROUTING -d 192.168.10.111 -p tcp --dport 22 -j DNAT --to-destination 10.0.0.150  

[root@firewall ~]# iptables -t nat -nL
Chain PREROUTING (policy ACCEPT)
target     prot opt source               destination
DNAT       tcp  --  0.0.0.0/0            192.168.10.111       tcp dpt:22 to:10.0.0.150
```

尝试 SSH 登录内网服务器

```bash
[root@external-client ~]# ssh 192.168.10.111
root@192.168.10.111's password:

Last login: Sun Jan 28 17:10:32 2024 from 10.0.0.1
[root@internal-server ~]# 
```

查看网络连接跟踪信息

```bash
root@firewall ~]# cat /proc/net/nf_conntrack
ipv4     2 tcp      6 431997 ESTABLISHED src=192.168.10.100 dst=192.168.10.111 sport=54838 dport=22 src=10.0.0.150 dst=192.168.10.100 sport=22 dport=54838 [ASSURED] mark=0 secctx=system_u:object_r:unlabeled_t:s0 zone=0 use=2
```

## 17. 使用 REDIRECT 将 90 端口的流量重定向到 80 端口

防火墙配置，放行 80 和 90 端口的 TCP 流量

```bash
[root@firewall ~]# iptables -t nat -A POSTROUTING -s 10.0.0.0/24 -p tcp -m multiport --dport 80,90 -j SNAT --to-source 192.168.10.111

[root@firewall ~]# iptables -t nat -nL
Chain POSTROUTING (policy ACCEPT)
target     prot opt source               destination
SNAT       tcp  --  10.0.0.0/24          0.0.0.0/0            multiport dports 80,90 to:192.168.10.111
```

Nginx 服务器配置，将 90 端口的 TCP 流量重定向到 80 端口

```bash
[root@external-nginx ~]# iptables -t nat -A PREROUTING -p tcp --dport 90 -j REDIRECT --to-ports 80

[root@external-nginx ~]# iptables -t nat -nL
Chain PREROUTING (policy ACCEPT)
target     prot opt source               destination
REDIRECT   tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:90 redir ports 80
```

访问 Nginx 服务器

```bash
root@internal-client ~]# curl 192.168.10.200:90
I am 192.168.10.200.
```

查看网络连接跟踪信息

```bash
[root@firewall ~]# cat /proc/net/nf_conntrack
ipv4     2 tcp      6 117 TIME_WAIT src=192.168.10.111 dst=192.168.10.200 sport=43278 dport=90 src=192.168.10.200 dst=192.168.10.111 sport=80 dport=43278 [ASSURED] mark=0 secctx=system_u:object_r:unlabeled_t:s0 zone=0 use=2
```

## 18. firewalld 常见区域

|区域|描述|
|---|---|
|`block`|拒绝所有传入的网络连接，并回复 ICMP 禁止消息。仅接受系统内部发起的网络连接|
|`dmz`|仅接收指定的传入连接|
|`drop`|丢弃所有传入的数据包，不发送任何通知。仅接受传出的网络连接|
|`external`|所有传出流量的源地址被伪装成网卡的地址。仅接收指定的传入连接|
|`home`|家庭环境，基本上信任网络上的其它计算机。仅接收指定的传入连接|
|`internal`|内部网络，基本上信任网络上的其它计算机。仅接收指定的传入连接|
|`public`|公共区域，不信任网络上的其它计算机。仅接收指定的传入连接|
|`trusted`|接收所有网络连接|
|`work`|工作环境，基本上信任网络上的其它计算机。仅接收指定的传入连接|

## 19. nftables 放行本机 80、443 和 22 端口

启动 nftables 服务

```bash
zqh@ubuntu:~$ sudo systemctl enable --now nftables.service
Created symlink /etc/systemd/system/sysinit.target.wants/nftables.service → /lib/systemd/system/nftables.service.
```

编辑 `/etc/nftables.conf` 文件，替换文件内容

```bash
#!/usr/sbin/nft -f

flush ruleset

table inet filter {
    chain input {
        type filter hook input priority 0;

        # accept any localhost traffic
        iif lo accept

        # accept traffic originated from us
        ct state established,related accept

        tcp dport { 22, 80, 443 } ct state new accept

        # count and drop any other traffic
        counter drop
    }
}
```

重新加载服务

```bash
zqh@ubuntu:~$ sudo systemctl reload nftables.service
```

查看规则集

```bash
zqh@ubuntu:~$ sudo nft list ruleset
table inet filter {
        chain input {
                type filter hook input priority filter; policy accept;
                iif "lo" accept
                ct state established,related accept
                tcp dport { 22, 80, 443 } ct state new accept
                counter packets 0 bytes 0 drop
        }
}
```

访问 80 端口

```bash
[root@rocky ~]# curl 10.0.0.151
I am 10.0.0.151
```

发送 ICMP 请求

```bash
[root@rocky ~]# ping 10.0.0.151
PING 10.0.0.151 (10.0.0.151) 56(84) bytes of data.
^C
--- 10.0.0.151 ping statistics ---
5 packets transmitted, 0 received, 100% packet loss, time 4095ms
```

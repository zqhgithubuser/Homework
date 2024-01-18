## 1. RAID 0、1、5、6、10、01 的工作原理，总结它们的利用率，冗余性、性能、至少几个磁盘实现

**RAID0**：通常称为条带化。将数据均匀地分割到两个或多个磁盘上。任何一个磁盘故障，所有数据都会丢失

**RAID1**：数据镜像备份到 RAID 中的所有磁盘上。一个磁盘故障，不会影响数据的读写

**RAID5**：块级条带化，奇偶校验分布在不同的磁盘上。一个磁盘故障，可以根据其它数据块和校验数据重建损坏数据

**RAID6**：块级条带化，采用双重奇偶校验。可以容忍两个磁盘同时故障

**RAID10**：将两个 RAID1 组合成一个 RAID0

**RAID01**：将两个 RAID0 组合成一个 RAID1

假设每块磁盘的大小相同

|级别|最少磁盘数量|读性能|写性能|容错能力|磁盘利用率|
|---|---|---|---|---|---|
|RAID0|2|n|n|无|100%|
|RAID1|2|n|1|允许 n - 1块磁盘故障|1 / n|
|RAID5|3|n|n - 1|允许 1 块磁盘故障|(n - 1) / n|
|RAID6|4|n|n - 2|允许 2 块磁盘故障|(n - 2) / n|
|RAID10|4|||允许每组 RAID 1 的 1 块磁盘同时故障|50%|
|RAID 01|4|||允许一组 RAID 0 的 2 块磁盘同时故障|50%|

## 2. LVM 扩容和缩容示例

### 2.1 扩容

文件系统已挂载

```bash
[root@rocky ~]# df -Th
Filesystem                    Type      Size  Used Avail Use% Mounted on
......
/dev/mapper/vg--test-lv--test xfs        20G  175M   20G   1% /root/lv-test-dir
```

创建物理卷（PV）

```bash
[root@rocky ~]# pvs
  PV         VG      Fmt  Attr PSize    PFree
  /dev/sda2  rl      lvm2 a--  <199.00g    0
  /dev/sdb   vg-test lvm2 a--   <20.00g    0

[root@rocky ~]# pvcreate /dev/sdc
  Physical volume "/dev/sdc" successfully created.
  
[root@rocky ~]# pvs
  PV         VG      Fmt  Attr PSize    PFree
  /dev/sda2  rl      lvm2 a--  <199.00g     0
  /dev/sdb   vg-test lvm2 a--   <20.00g     0
  /dev/sdc           lvm2 ---    20.00g 20.00g
```

扩展卷组（VG）

```bash
[root@rocky ~]# vgextend vg-test /dev/sdc
  Volume group "vg-test" successfully extended

[root@rocky ~]# vgs
  VG      #PV #LV #SN Attr   VSize    VFree
  rl        1   3   0 wz--n- <199.00g      0
  vg-test   2   1   0 wz--n-   39.99g <20.00g
```

扩展逻辑卷（LV）

```bash
[root@rocky ~]# lvextend -L +10G /dev/vg-test/lv-test
  Size of logical volume vg-test/lv-test changed from <20.00 GiB (5119 extents) to <30.00 GiB (7679 extents).
  Logical volume vg-test/lv-test successfully resized.
```

调整文件系统的大小

```bash
[root@rocky ~]# xfs_growfs /dev/vg-test/lv-test
meta-data=/dev/mapper/vg--test-lv--test isize=512    agcount=4, agsize=1310464 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=1, sparse=1, rmapbt=0
         =                       reflink=1    bigtime=0 inobtcount=0
data     =                       bsize=4096   blocks=5241856, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0, ftype=1
log      =internal log           bsize=4096   blocks=2560, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
data blocks changed from 5241856 to 7863296

[root@rocky ~]# lvs
  LV      VG      Attr       LSize    Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  home    rl      -wi-ao---- <126.98g
  root    rl      -wi-ao----   70.00g
  swap    rl      -wi-ao----   <2.02g
  lv-test vg-test -wi-ao----  <30.00g
```

>[!note]
>xfs_growfs 命令用于调整 XFS 文件系统的大小，resize2fs 命令用于调整 EXT 文件系统的大小

### 2.2 缩容

卸载文件系统

```bash
[root@rocky ~]# umount lv-test-dir
```

缩减逻辑卷（LV）

```bash
[root@rocky ~]# resize2fs /dev/vg-test/lv-test 25G
resize2fs 1.45.6 (20-Mar-2020)
Resizing the filesystem on /dev/vg-test/lv-test to 6553600 (4k) blocks.
The filesystem on /dev/vg-test/lv-test is now 6553600 (4k) blocks long.

[root@rocky ~]# lvreduce -L 25G /dev/vg-test/lv-test
  WARNING: Reducing active logical volume to 25.00 GiB.
  THIS MAY DESTROY YOUR DATA (filesystem etc.)
Do you really want to reduce vg-test/lv-test? [y/n]: y
  Size of logical volume vg-test/lv-test changed from 30.00 GiB (7680 extents) to 25.00 GiB (6400 extents).
  Logical volume vg-test/lv-test successfully resized.
```

重新挂载

```bash
[root@rocky ~]# mount /dev/vg-test/lv-test lv-test-dir

[root@rocky ~]# df -Th
Filesystem                    Type      Size  Used Avail Use% Mounted on
......
/dev/mapper/vg--test-lv--test ext4       25G   24K   24G   1% /root/lv-test-dir
```

>[!warning]
>缩容可以会损坏数据，建议先进行备份。XFS 文件系统原生不支持缩容

## 3. 软件包管理程序有哪些，软件包中包含什么内容？yum/dnf/apt 软件包获取途径，以及命令选项示例？

>软件包获取途径
>https://developer.aliyun.com/mirror/

### 3.1 rpm

常用选项
- `-i`：安装软件包
- `-h`：显示安装进度
- `-v`：提供更详细的输出
- `-a`：所有包
- `-f`：查询文件属于哪个软件包
- `-q`：查询
- `-c`：列出所有配置文件
- `--info|-i`：列出软件包的描述信息
- `-l`：列出软件包内的文件
- `--scripts`：列出安装/卸载脚本
- `-e`：卸载软件包


安装软件包

```bash
[root@rocky ~]# rpm -ivh vsftpd-3.0.3-36.el8.x86_64.rpm
Verifying...                          ################################# [100%]
Preparing...                          ################################# [100%]
Updating / installing...
   1:vsftpd-3.0.3-36.el8              ################################# [100%]
```

查看软件包的详细信息

```bash
[root@rocky ~]# rpm -qi vsftpd-3.0.3-36.el8.x86_64.rpm
Name        : vsftpd
Version     : 3.0.3
Release     : 36.el8
Architecture: x86_64
Install Date: (not installed)
Group       : System Environment/Daemons
Size        : 355805
License     : GPLv2 with exceptions
Signature   : RSA/SHA256, Fri 07 Apr 2023 05:34:37 AM CST, Key ID 15af5dac6d745a60
Source RPM  : vsftpd-3.0.3-36.el8.src.rpm
Build Date  : Fri 07 Apr 2023 05:27:31 AM CST
Build Host  : ord1-prod-x86build003.svc.aws.rockylinux.org
Relocations : (not relocatable)
Packager    : infrastructure@rockylinux.org
Vendor      : Rocky
URL         : https://security.appspot.com/vsftpd.html
Summary     : Very Secure Ftp Daemon
Description :
vsftpd is a Very Secure FTP daemon. It was written completely from
scratch.
```

查询文件属于哪个软件包

```bash
[root@rocky ~]# rpm -qf /etc/vsftpd/vsftpd.conf
vsftpd-3.0.3-36.el8.x86_64
```

查询软件包的配置文件

```bash
[root@rocky ~]# rpm -qc vsftpd
/etc/logrotate.d/vsftpd
/etc/pam.d/vsftpd
/etc/vsftpd/ftpusers
/etc/vsftpd/user_list
/etc/vsftpd/vsftpd.conf
```

卸载软件包

```bash
[root@rocky ~]# rpm -evh vsftpd
Preparing...                          ################################# [100%]
Cleaning up / removing...
   1:vsftpd-3.0.3-36.el8              ################################# [100%]
```

### 3.2 yum/dnf

yum/dnf 是基于 rpm 的软件包管理程序，可以解决软件包之间的依赖关系

常用选项
- `-y`：自动回答 yes
- `--enablerepo`：临时启用仓库
- `--disablerepo`：临时禁用仓库
- `--repo`：指定仓库
- ` --showduplicates`：显示依赖

常用子命令
- `autoremove`：卸载不需要的软件包以及依赖包
- `clean`：清除缓存的数据
- `info`：显示软件包的详细信息
- `install`：安装软件包
- `list`：列出所有软件包
- `makecache`：生成元数据缓存
- `provides`：查看某个文件是由哪个软件包提供的
- `remove`：卸载软件包
- `repolist`：显示配置的软件仓库列表
- `search`：搜素软件包
- `upgrade`：更新软件包

显示可用软件包的所有版本

```bash
[root@rocky ~]# yum --showduplicates list 'docker-ce'
Last metadata expiration check: 1:48:58 ago on Sun 14 Jan 2024 04:20:30 PM CST.
Installed Packages
docker-ce.x86_64                                      3:24.0.7-1.el8                                        @docker-ce-stable
Available Packages
docker-ce.x86_64                                      3:19.03.13-3.el8                                      docker-ce-stable
docker-ce.x86_64                                      3:19.03.14-3.el8                                      docker-ce-stable
docker-ce.x86_64                                      3:19.03.15-3.el8                                      docker-ce-stable
docker-ce.x86_64                                      3:20.10.0-3.el8                                       docker-ce-stable
docker-ce.x86_64                                      3:20.10.1-3.el8                                       docker-ce-stable
docker-ce.x86_64                                      3:20.10.2-3.el8                                       docker-ce-stable
docker-ce.x86_64                                      3:20.10.3-3.el8                                       docker-ce-stable
......
```

安装软件包

```bash
[root@rocky ~]# yum install -y httpd
```

卸载软件包

```bash
[root@rocky ~]# yum remove -y httpd
```

查看文件是哪个软件包提供的

```bash
[root@rocky ~]# yum provides /usr/bin/yum
Last metadata expiration check: 1:56:52 ago on Sun 14 Jan 2024 04:20:30 PM CST.
yum-4.7.0-16.el8_8.noarch : Package manager
Repo        : @System
Matched from:
Filename    : /usr/bin/yum

yum-4.7.0-19.el8.noarch : Package manager
Repo        : baseos
Matched from:
Filename    : /usr/bin/yum
```

列出软件包的依赖

```bash
[root@rocky ~]# yum repoquery --deplist nginx
```

安装本地 rpm 软件包

```bash
[root@rocky ~]# yum localinstall -y vsftpd-3.0.3-36.el8.x86_64.rpm
```

### 3.3 dpkg

常用选项
- `-i`：安装软件包
- `-r`：卸载软件包
- `-L`：列出软件包内的文件
- `-l`：列出已安装的软件包
- `-s`：显示软件包详细信息
- `-S`：查询文件属于哪个软件包
- `--info|-I`：显示 deb 包的信息
- `-c`：列出 deb 包内的文件

显示软件包详细信息

```bash
zqh@ubuntu:~$ sudo dpkg -s vim
Package: vim
Status: install ok installed
Priority: optional
Section: editors
Installed-Size: 3931
Maintainer: Ubuntu Developers <ubuntu-devel-discuss@lists.ubuntu.com>
Architecture: amd64
Version: 2:8.2.3995-1ubuntu2.15
Provides: editor
Depends: vim-common (= 2:8.2.3995-1ubuntu2.15), vim-runtime (= 2:8.2.3995-1ubuntu2.15), libacl1 (>= 2.2.23), libc6 (>= 2.34), libgpm2 (>= 1.20.7), libpython3.10 (>= 3.10.0), libselinux1 (>= 3.1~), libsodium23 (>= 1.0.14), libtinfo6 (>= 6)
Suggests: ctags, vim-doc, vim-scripts
Description: Vi IMproved - enhanced vi editor
 Vim is an almost compatible version of the UNIX editor Vi.
 .
 Many new features have been added: multi level undo, syntax
 highlighting, command line history, on-line help, filename
 completion, block operations, folding, Unicode support, etc.
 .
 This package contains a version of vim compiled with a rather
 standard set of features.  This package does not provide a GUI
 version of Vim.  See the other vim-* packages if you need more
 (or less).
Homepage: https://www.vim.org/
Original-Maintainer: Debian Vim Maintainers <team+vim@tracker.debian.org>
```

列出软件包内的文件

```bash
zqh@ubuntu:~$ sudo dpkg -L vim
/.
/usr
/usr/bin
/usr/bin/vim.basic
/usr/share
/usr/share/bug
/usr/share/bug/vim
/usr/share/bug/vim/presubj
/usr/share/bug/vim/script
/usr/share/doc
/usr/share/doc/vim
/usr/share/doc/vim/NEWS.Debian.gz
/usr/share/doc/vim/changelog.Debian.gz
/usr/share/doc/vim/copyright
/usr/share/lintian
/usr/share/lintian/overrides
/usr/share/lintian/overrides/vim
```

查询文件属于哪个软件包

```bash
zqh@ubuntu:~$ sudo dpkg -S /usr/bin/ssh-keygen
openssh-client: /usr/bin/ssh-keygen
```

### 3.4 apt

apt 是基于 dpkg 的软件包管理程序，可以解决软件包之间的依赖关系

常用子命令
- `list`：列出软件包
- `search`：搜索软件包
- `show`：显示软件包详细信息
- `install`：安装软件包
- `remove`：卸载软件包
- `autoremove`：自动卸载不再使用的软件包
- `update`：更新可用软件包列表
- `upgrade`：更新软件包

显示可用的软件包版本

```bash
zqh@ubuntu:~$ apt-cache madison nginx
     nginx | 1.18.0-6ubuntu14.4 | https://mirrors.tuna.tsinghua.edu.cn/ubuntu jammy-updates/main amd64 Packages
     nginx | 1.18.0-6ubuntu14.3 | https://mirrors.tuna.tsinghua.edu.cn/ubuntu jammy-security/main amd64 Packages
     nginx | 1.18.0-6ubuntu14 | https://mirrors.tuna.tsinghua.edu.cn/ubuntu jammy/main amd64 Packages
```

显示软件包的依赖信息

```bash
zqh@ubuntu:~$ sudo apt-cache depends nginx
nginx
 |Depends: nginx-core
 |Depends: nginx-full
 |Depends: nginx-light
  Depends: nginx-extras
 |Depends: nginx-core
 |Depends: nginx-full
 |Depends: nginx-light
  Depends: nginx-extras
  Breaks: <libnginx-mod-http-lua>
```

## 4. 简要总结 yum/dnf 工作原理，并搭建私有 yum 仓库给另一个虚拟机使用

### 4.1 yum/dnf 工作原理

yum 基于 C/S 架构
- yum 服务器：通常情况下，所有 rpm 软件包存放在 Packages 目录中，元数据存放在 repodata 目录中，元数据包含软件包的的信息以及它们之间的依赖关系
- yum 客户端：用户想安装某个软件时，yum 客户端从 yum 服务器上下载元数据并缓存在本地，然后检查软件包的依赖关系，下载并安装软件

### 4.2 实验环境

| 角色                | 主机名 | IP 地址    |
| ------------------- | ------ | ---------- |
| 私有 yum 仓库服务器 | server | 10.0.0.180 |
| 客户端              | client | 10.0.0.200 |
### 4.3 服务端配置

安装并启动 httpd 服务

```bash
[root@server ~]# yum install httpd
[root@server ~]# systemctl enable --now httpd
Created symlink /etc/systemd/system/multi-user.target.wants/httpd.service → /usr/lib/systemd/system/httpd.service.
```

下载所需的软件包和元数据

```bash
[root@server ~]# mkdir -p /var/www/html/rockylinux/8
[root@server ~]# yum reposync --repoid=baseos --download-metadata -p /var/www/html/rockylinux/8/

[root@server ~]# yum install -y epel-release
[root@server ~]# yum reposync --repoid=epel --download-metadata -p /var/www/html/rockylinux/8/
```

yum 仓库文件

```bash
# 保留旧的 yum 仓库文件
[root@server ~]# ls /etc/yum.repos.d/* | xargs -I {}  mv {} {}.bak

[root@server yum.repos.d]# cat Rocky-BaseOS.repo
[baseos]
name=Rocky Linux $releasever - BaseOS
baseurl=file:///var/www/html/rockylinux/$releasever/baseos/
enabled=1
gpgcheck=0

[root@server yum.repos.d]# cat epel.repo
[epel]
name=Extra Packages for Enterprise Linux 8 - $basearch
baseurl=file:///var/www/html/rockylinux/$releasever/epel/
enabled=1
gpgcheck=0
```

列出启用的仓库

```bash
[root@rocky yum.repos.d]# yum repolist
repo id                                                  repo name
baseos                                                   Rocky Linux 8 - BaseOS
epel                                                     Extra Packages for Enterprise Linux 8 - x86_64
```

### 4.4 客户端配置

yum 仓库文件

```bash
[root@client yum.repos.d]# cat Rocky-BaseOS.repo
[baseos]
name=Rocky Linux $releasever - BaseOS
baseurl=http://10.0.0.180/rockylinux/$releasever/baseos/
enabled=1
gpgcheck=0

[root@client yum.repos.d]# cat epel.repo
[epel]
name=Extra Packages for Enterprise Linux 8 - $basearch
baseurl=http://10.0.0.180/rockylinux/$releasever/epel/
enabled=1
gpgcheck=0
```

尝试安装软件

```bash
[root@client yum.repos.d]# yum install zsh
```

## 5. 安装系统后的常用初始化操作

### 5.1 Rocky

**设置时区**

```bash
[root@rocky ~]# timedatectl set-timezone Asia/Shanghai
```

**配置国内镜像源**
`
```bash
[root@rocky ~]# sed -e 's|^mirrorlist=|#mirrorlist=|g' \
    -e 's|^#baseurl=http://dl.rockylinux.org/$contentdir|baseurl=https://mirrors.aliyun.com/rockylinux|g' \
    -i.bak \
    /etc/yum.repos.d/Rocky-*.repo
```

**安装常用的软件**

```bash
[root@rocky ~]# yum install -y vim
```

**关闭防火墙**

```bash
[root@rocky ~]# systemctl disable --now firewalld
```

**禁用 SELinux**

```bash
[root@rocky ~]# sed -ri 's/(^SELINUX=).*/\1disabled/' /etc/selinux/config
[root@rocky ~]# setenforce 0
```

### 5.2 Ubuntu

**设置时区**

```bash
zqh@ubuntu:~$ sudo timedatectl set-timezone Asia/Shanghai
```

**配置国内镜像源**

编辑 `/etc/apt/sources.list` 文件，使用以下行替换默认的内容

```bash
deb https://mirrors.aliyun.com/ubuntu/ jammy main restricted universe multiverse
deb-src https://mirrors.aliyun.com/ubuntu/ jammy main restricted universe multiverse

deb https://mirrors.aliyun.com/ubuntu/ jammy-security main restricted universe multiverse
deb-src https://mirrors.aliyun.com/ubuntu/ jammy-security main restricted universe multiverse

deb https://mirrors.aliyun.com/ubuntu/ jammy-updates main restricted universe multiverse
deb-src https://mirrors.aliyun.com/ubuntu/ jammy-updates main restricted universe multiverse

deb https://mirrors.aliyun.com/ubuntu/ jammy-backports main restricted universe multiverse
deb-src https://mirrors.aliyun.com/ubuntu/ jammy-backports main restricted universe multiverse
```

**安装常用的软件**

```bash
zqh@ubuntu:~$ sudo apt update
zqh@ubuntu:~$ sudo apt install -y vim
```

**关闭防火墙**

```bash
zqh@ubuntu:~$ sudo ufw disable
Firewall stopped and disabled on system startup
```

**允许 root 用户 SSH 登录**

设置 root 用户的密码

```bash
zqh@ubuntu:~$ sudo passwd root
```

编辑 SSH 服务配置文件 `/etc/ssh/sshd_config`，找到以下行

```bash
#PermitRootLogin prohibit-password
```

修改为

```bash
PermitRootLogin yes
```

重启 SSH 服务

```bash
zqh@ubuntu:~$ sudo systemctl reload ssh.service
```

## 6. Shell 编程：一键安装 Nginx

脚本内容如下

```bash
#!/usr/bin/bash

PROCESSOR=$(grep -c processor /proc/cpuinfo)

NGINX_VERSION=$1
SRC_PACKAGE=nginx-${NGINX_VERSION}.tar.gz
INSTALL_DIR=/usr/local/nginx
COLOR_RED="\e[31;1m"
COLOR_GREEN="\e[32;1m"
END="\e[0m"

source /etc/os-release

#######################################
# 关闭防火墙，安装依赖包
#######################################
init() {
    if [[ $ID =~ rhel|rocky|centos ]]; then
        systemctl disable --now firewalld
        yum makecache
        yum install -y gcc make openssl-devel pcre-devel
    elif [[ $ID == ubuntu ]]; then
        sudo ufw disable
        sudo apt update
        sudo apt install -y gcc make libssh-dev libpcre3-dev
    else
        echo -e ${COLOR_RED}"暂不支持$ID系统"$END
        exit 1
    fi
}

#######################################
# 下载并解压Nginx源码包
#######################################
download() {
    if [ ! -f ${SRC_PACKAGE} ]; then
        if [[ $ID =~ rhel|rocky|centos ]]; then
            yum install -y wget
        else
            sudo apt install -y wget
        fi
        wget https://nginx.org/download/nginx-${NGINX_VERSION}.tar.gz \
            || { echo -e ${COLOR_RED}"下载失败！"$END; exit 1; }
        tar -xf ${SRC_PACKAGE} -C /usr/local/src
    fi
}

#######################################
# 编译安装Nginx
#######################################
install() {
    cd /usr/local/src/nginx-${NGINX_VERSION}

    ./configure \
        --sbin-path=${INSTALL_DIR}/nginx \
        --conf-path=${INSTALL_DIR}/nginx.conf \
        --pid-path=${INSTALL_DIR}/nginx.pid \
        --with-http_ssl_module

    make -j $PROCESSOR && make install
    if (( $? != 0 )); then
        echo -e ${COLOR_RED}"安装失败！"$END
        exit 1
    fi
}

init
download
install
${INSTALL_DIR}/nginx && echo -e ${COLOR_GREEN}"成功启动Nginx服务！"$END
```

脚本执行结果

```bash
[root@rocky scripts]# ./install_nginx.sh 1.24.0
......
Configuration summary
  + using system PCRE2 library
  + using system OpenSSL library
  + using system zlib library

  nginx path prefix: "/usr/local/nginx"
  nginx binary file: "/usr/local/nginx/nginx"
  nginx modules path: "/usr/local/nginx/modules"
  nginx configuration prefix: "/usr/local/nginx"
  nginx configuration file: "/usr/local/nginx/nginx.conf"
  nginx pid file: "/usr/local/nginx/nginx.pid"
  nginx error log file: "/usr/local/nginx/logs/error.log"
  nginx http access log file: "/usr/local/nginx/logs/access.log"
  nginx http client request body temporary files: "client_body_temp"
  nginx http proxy temporary files: "proxy_temp"
  nginx http fastcgi temporary files: "fastcgi_temp"
  nginx http uwsgi temporary files: "uwsgi_temp"
  nginx http scgi temporary files: "scgi_temp"
......
make[1]: Leaving directory '/usr/local/src/nginx-1.24.0'
成功启动Nginx服务！
```

## 7. 总结 OSI 模型每层的作用及对应的协议 

**应用层**：各种应用程序协议。常见的协议包括 HTTP、SSH、FTP 等协议

**表示层**：网络服务和应用程序之间的数据转换，包括字符编码、数据压缩和加密解密

**会话层**：建立和管理两个节点之间的通信会话

**传输层**：网络上各点之间数据段的可靠传输，包括分段、确认和复用。常见的协议包括 TCP 和 UDP 协议

**网络层**：构建和管理多节点网络，包括寻址、路由和流量控制。常见的协议包括 IP、ICMP、OSPF 等协议

**数据链路层**：在物理层连接的两个节点之间传输数据帧。常见的协议包括 Ethernet 和 PPP、WLAN 等协议

**物理层**：通过物理介质传输和接收原始比特流

## 8. 调整动态端口范围为 20000~60000

```bash
[root@rocky ~]# cat /proc/sys/net/ipv4/ip_local_port_range
32768   60999

[root@rocky ~]# echo '20000   60000' > /proc/sys/net/ipv4/ip_local_port_range

[root@rocky ~]# cat /proc/sys/net/ipv4/ip_local_port_range
20000   60000
```

永久生效：编辑内核参数配置文件 `/etc/sysctl.conf` ，添加以下行

```bash
net.ipv4.ip_local_port_range = "20000   60000"
```

立即生效

```bash
[root@rocky ~]# sysctl -p
```

## 9. TCP 段结构，TCP 三次握手、四次挥手

### 9.1 TCP 段结构

![](https://github.com/zqhgithubuser/Homework/blob/main/Week3/images/Pasted_image_20240114130750.png)

**序列号（32 位）**：每发送一次数据，就累加一次数据字节数的大小

**确认号（32 位）**：ACK 发送方期望收到下一个段的第一个字节的编号。表示确认收到先前的所有字节

**控制位**
- ACK（1 位）：该位为 1，表示确认号字段生效
- RST（1 位）：该位为 1，重置连接
- SYN（1 位）：同步序列号。该位 为 1，表示希望建立连接，发送方在第一个数据包的序列号字段设置初始值
- FIN（1 位）：该位 为 1，发送方的最后一个数据包，表示希望断开连接

### 9.2 TCP 三次握手

![](https://github.com/zqhgithubuser/Homework/blob/main/Week3/images/Pasted_image_20240114141108.png)

1. 开始，客户端和服务端都处于 `CLOSED` 状态，服务端主动监听某个端口，进入 `LISTEN` 状态
2. SYN：客户端随机初始化序列号 `client_isn`，将 SYN 标志位置为 1，表示 SYN 报文。接着将报文发送给服务端，表示向服务端请求建立连接，该报文不包含应用层数据，客户端进入 `SYN-SENT` 状态
3. SYN-ACK：服务端收到客户端的 SYN 报文后，首先随机初始化自己的序列号 `server_isn`，然后将 `client_isn + 1` 填入TCP 头部的确认号字段，接着将 SYN 和 ACK 标志位都置为 1，最后将报文发送给客户端，该报文也不包含应用层数据，服务端进入 `SYN-RCVD` 状态
4. ACK：客户端收到服务端发送的 SYN-ACK 报文后，需要向服务端回应最后一个应答报文。将 ACK 标志位置为 1，确认号字段填入 `server_isn + 1`，最后把报文发送给服务端。该报文可以携带应用层数据，客户端进入 `ESTABLISHED` 状态
5. 服务端收到客户端的应答报文后，也进入 `ESTABLISHED` 状态

### 9.3 TCP 四次挥手

![](https://github.com/zqhgithubuser/Homework/blob/main/Week3/images/Pasted_image_20240114144030.png)

1. 客户端打算关闭连接，此时发送一个 TCP 头部 FIN 标志位为 1 的报文，即 FIN 报文，客户端进入 `FIN_WAIT_1` 状态
2. 服务端收到 FIN 报文后，向客户端发送 ACK 应答报文，表示对客户端的关闭请求进行确认。服务端进入 `CLOSED_WAIT` 状态
3. 客户端收到服务端的 ACK 应答报文后，进入 `FIN_WAIT_2` 状态
4. 服务端处理完数据后，也向客户端发送 FIN 报文，服务端进入 `LAST_ACK` 状态
5. 客户端收到服务端的 FIN 报文后，回复一个 ACK 应答报文，表示对服务端的关闭请求进行确认，进入 `TIME_WAIT` 状态
6. 服务端收到了 ACK 应答报文后进入 `CLOSED` 状态，完成连接的关闭
7. 客户端在经过一段时间后，自动进入 `CLOSED` 状态，完成连接的关闭。这段时间用于确保网络中已经没有该连接的任何数据

## 10. 主机到主机的包传递过程

![](https://github.com/zqhgithubuser/Homework/blob/main/Week3/images/Pasted_image_20240113220152.png)

主机 A 发送数据（自上向下封装）
1. 应用程序告诉传输层它有待发送的数据，并且需要可靠的连接
2. 传输层添加 TCP 头部，创建一个段。TCP 头部包含源端口和目的端口等信息。然后，段被传递到网络层
3. 网络层添加 IP 头部，创建一个数据包。IP 头部包含源 IP 地址和目的 IP 地址等信息。然后，数据包被传递到数据链路层
4. 数据链路层想要添加帧头部，需要知道主机 B 的 MAC 地址。数据链路层询问 ARP 是否有所需的映射。如果没有所需的映射，数据包等待 ARP 解析主机 B 的 MAC 地址。ARP 请求的目的 MAC 地址是广播地址
5. ARP 缓存更新主机 B 的 MAC 地址后，数据链路层添加帧头部，创建一个帧。帧头部包含源 MAC 地址和目的 MAC 地址等信息
6. 物理层将帧转化成比特流传输

主机 B 接收数据（自下而上解封装）
1. 物理层将比特流转化成，并传递给数据链路层
2. 数据链路层确定帧的目的 MAC 地址是主机 B 的 MAC 地址，移除帧头部并将数据包传递给网络层
3. 网络层确定数据包的目的 IP 地址是主机 B 的 IP 地址，移除 IP 头部并将段传递给传输层
4. 传输层确定目的端口是主机 B 支持的端口，移除 TCP 头部并将数据传递给应用程序
5. 应用程序接收并处理数据
	
## 11. 总结 A、B、C、D 类 IP 地址，并解析 IP 地址的组成

IP 地址组成（32 位二进制数，分成 4 组，每组 8 位）
- 网络部分：标识网络
- 主机部分：标识单个主机

子网掩码：用于划分 IP 地址中的网络部分和主机部分。子网掩码中连续的 1 表示网络部分，连续的 0 表示主机部分。例如 IP 地址 192.168.1.1 和子网掩码 255.255.255.0 进行与运算得到的结果是 192.168.1.0，这表示前 24 位是网络部分，后 8 位是主机部分。192.168.1.0 为网络地址，用于标识 IP 地址所处的网络

| IP 地址类别 | 第一个字节    | 子网掩码                     | IP 地址范围                          |
| ----------- | ------------- | ---------------------------- | --------------------------------- |
| A 类        | 最高位是 0    | 255.0.0.0                    | 10.0.0.0/8 ~ 127.255.255.255/8    |
| B 类        | 前两位是 10   | 255.255.0.0                  | 128.0.0.0/16 ~ 191.255.255.255/16 |
| C 类        | 前三位是 110  | 255.255.255.0                | 192.0.0.0/24 ~ 223.255.255.255/24 |
| D 类        | 前四位是 1110 | 用于多播通信，不使用子网掩码 | 224.0.0.0 ~ 239.255.255.255       |

## 12. 201.222.200.111/18 的主机数？子网掩码？说明计算方法

主机数：2^(32-网络前缀)-2 = 2^(32-18)-2 = 2^(14)-2 = 16382

子网掩码
①网络前缀的二进制表示：11111111 11111111 11000000 00000000

②对应的点分十进制表示即为子网掩码：255.255.192.0

>[!note]
>主机位都为 0 的地址称为网络地址，表示整个网络；主机位都为 1 的地址称为广播地址，用于向网络中所有主机发送广播消息，这两个地址不能分配给主机

## 13. A(10.0.1.1/16) 与 B(10.0.2.2/24) 通信，A 如何判断 B 是否和自己在同一个网段？A 和 B 能否通信？

当 A 向 B 发送数据包时，双方并不知道对方的子网掩码，它们会使用自己的子网掩码与目的 IP 地址进行与运算，将计算出的网络地址与自己的网络地址进行比较

| B 的 IP 地址       | 00001010 00000000 00000010 00000010 |
| ------------------ | ----------------------------------- |
| A 的子网掩码       | 11111111 11111111 00000000 00000000 |
| 与运算结果         | 00001010 00000000 00000000 00000000 |

- B 的网络地址为 10.0.0.0
- A 的网络地址为 10.0.0.0

在 A 看来，B 和自己在同一个网络，因此 A 会直接发送数据包给 B

| A 的 IP 地址       | 00001010 00000000 00000001 00000001 |
| ------------------ | ----------------------------------- |
| B 的子网掩码       | 11111111 11111111 11111111 00000000 |
| 与运算结果           | 00001010 00000000 00000001 00000000 |

- B 的网络地址为 10.0.2.0
- A 的网络地址为 10.0.1.0
在 B 看来，A 和自己不在同一个网络，因此 B 不会回复 A

## 14. 如何将 10.0.0.0/8 划分为 32 个子网

10.0.0.0/8 的网络前缀长度是 8 位，要将其划分为 32 个子网，则么每个子网的网络前缀长度是 8 + log2(32) = 13 位

| 网络地址的二进制表示                    | 网络地址的点分十进制表示    |
| ------------------------------------- | ------------- |
| 00001010 00000\|000 00000000 00000000 | 10.0.0.0/13   |
| 00001010 00001\|000 00000000 00000000 | 10.8.0.0/13   |
| 00001010 00010\|000 00000000 00000000 | 10.16.0.0/13  |
| 00001010 00011\|000 00000000 00000000 | 10.24.0.0/13  |
| 00001010 00100\|000 00000000 00000000 | 10.32.0.0/13  |
| 00001010 00101\|000 00000000 00000000 | 10.40.0.0/13  |
| 00001010 00110\|000 00000000 00000000 | 10.48.0.0/13  |
| ......                                | ......        |
| 00001010 11111\|000 00000000 00000000 | 10.248.0.0/13 |

## 15. 通过网络配置命令，使主机可以上网

### 15.1 ip

暂时关闭 NetworkManager 服务，防止网卡获取网络配置

```bash
[root@rocky ~]# systemctl stop NetworkManager.service
```

配置 IP 地址

```bash
[root@rocky ~]# ip address add 10.0.0.3/24 dev eth0

[root@rocky ~]# ip address show eth0
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 00:0c:29:4b:64:c8 brd ff:ff:ff:ff:ff:ff
    altname enp2s1
    altname ens33
    inet 10.0.0.3/24 scope global eth0
       valid_lft forever preferred_lft forever
```

配置默认路由（网关）

```bash
[root@rocky ~]# ip route                         
10.0.0.0/24 dev eth0 proto kernel scope link src 10.0.0.3 
192.168.122.0/24 dev virbr0 proto kernel scope link src 192.168.122.1 linkdown 

[root@rocky ~]# ip route add default via 10.0.0.2

[root@rocky ~]# ip route                         
default via 10.0.0.2 dev eth0 
10.0.0.0/24 dev eth0 proto kernel scope link src 10.0.0.3 
192.168.122.0/24 dev virbr0 proto kernel scope link src 192.168.122.1 linkdown 
```

配置 DNS 服务器，编辑 `/etc/resolv.conf` 文件，添加以下行

```bash
nameserver  223.5.5.5
```

验证网络连通性

```bash
[root@rocky ~]# ping -c5 qq.com
PING qq.com (111.33.167.71) 56(84) bytes of data.
64 bytes from 111.33.167.71 (111.33.167.71): icmp_seq=1 ttl=128 time=70.2 ms
64 bytes from 111.33.167.71 (111.33.167.71): icmp_seq=2 ttl=128 time=83.1 ms
64 bytes from 111.33.167.71 (111.33.167.71): icmp_seq=3 ttl=128 time=70.4 ms
64 bytes from 111.33.167.71 (111.33.167.71): icmp_seq=4 ttl=128 time=99.0 ms
64 bytes from 111.33.167.71 (111.33.167.71): icmp_seq=5 ttl=128 time=70.1 ms

--- qq.com ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4008ms
rtt min/avg/max/mdev = 70.122/78.557/99.010/11.367 ms
```

>[!note]
>`ip` 命令的配置在系统重启后不会保留

### 15.2 nmcli

**nmcli** 是 NetworkManager 的一个命令行工具，用户能够使用命令行控制 NetworkManager 管理网络连接

命令格式

```bash
nmcli [OPTIONS...] {help | general | networking | radio | connection | device | agent | monitor} [COMMAND] [ARGUMENTS...]
```

#### 15.2.1 连接

>连接即设备的网络配置

常用子命令
- `show`：显示连接信息
- `up`：激活连接
- `down`：断开连接
- `add`：创建新连接
- `modify`：修改连接配置
- `reload`：重新加载连接配置
- `delete`：删除连接``

常用设置
- `con-name`：连接名
- `ifname`：设备名
- `[+|-]ipv4.addresses`：IP 地址
- `ipv4.gateway`：网关
- `[+|-]ipv4.dns `：DNS 服务器地址
- `ipv4.method auto|manual`：DHCP 或静态网络配置
- `autoconnect yes|no`：是否自动激活连接

列出所有连接

```bash
[root@rocky ~]# nmcli connection show
NAME    UUID                                  TYPE      DEVICE
eth0    a014bcdc-4e4d-4ce1-accc-ec76cc0ec4db  ethernet  eth0
```

显示连接的详细设置

```bash
[root@rocky ~]# nmcli connection show eth0
......
connection.interface-name:     eth0
connection.autoconnect:        yes
ipv4.method:                   auto
ipv6.method:                   auto
......
```

创建新的连接

```bash
# DHCP
[root@rocky ~]# nmcli connection add con-name eth0-dhcp ifname eth0 ipv4.method auto autoconnect yes

# 设置静态 IPv4 地址、子网掩码、默认网关和 DNS 服务器地址
[root@rocky ~]# nmcli connection add con-name eth0-static ifname eth0 ipv4.addresses 10.0.0.150/24 ipv4.gateway 10.0.0.2 ipv4.dns 223.5.5.5 ipv4.method manual autoconnect yes
```

修改连接

```bash
# 添加 DNS 服务器地址
[root@rocky ~]# nmcli connection modify eth0-static +ipv4.dns 114.114.114.114

# 删除 DNS 服务器地址
[root@rocky ~]# nmcli connection modify eth0-static -ipv4.dns 114.114.114.114

# 静态网络配置 -> DHCP
[root@rocky ~]# nmcli connection modify eth0-static ipv4.method auto
```

应用配置

```bash
# 重新读取连接配置，并加载到内存
[root@rocky ~]# nmcli connection reload

# 重新激活连接
[root@rocky ~]# nmcli connection up eth0-static
```

验证网络连通性

```bash
[root@rocky ~]# ping -c5 qq.com
PING qq.com (111.33.167.71) 56(84) bytes of data.
64 bytes from 111.33.167.71 (111.33.167.71): icmp_seq=1 ttl=128 time=75.1 ms
64 bytes from 111.33.167.71 (111.33.167.71): icmp_seq=2 ttl=128 time=79.1 ms
64 bytes from 111.33.167.71 (111.33.167.71): icmp_seq=3 ttl=128 time=94.5 ms
64 bytes from 111.33.167.71 (111.33.167.71): icmp_seq=4 ttl=128 time=104 ms
64 bytes from 111.33.167.71 (111.33.167.71): icmp_seq=5 ttl=128 time=109 ms

--- qq.com ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4013ms
rtt min/avg/max/mdev = 75.095/92.427/109.285/13.467 ms
```

删除连接

```bash
# 激活原来的连接
[root@rocky ~]# nmcli connection up eth0

[root@rocky ~]# nmcli connection delete eth0-dhcp
[root@rocky ~]# nmcli connection delete eth0-static
```

#### 15.2.2 设备

常用子命令
- `status`：显示所有设备的状态
- `show [ifname]`：显示设备详细信息
- `connect <ifname>`：连接到设备。NetworkManager 连接到对应的设备，尝试找到合适的连接配置，并激活配置。如果不存在对应的连接配置，NetworkManager 将创建并激活一个默认连接
- `disconnect <ifname>`：断开设备连接，NetworkManager 断开设备的连接，防止设备自动激活

显示所有设备的状态

```bash
[root@rocky ~]# nmcli device status
DEVICE  TYPE      STATE                   CONNECTION
eth0    ethernet  connected               eth0
virbr0  bridge    connected (externally)  virbr0
lo      loopback  unmanaged               --
```

显示设备详细信息

```bash
[root@rocky ~]# nmcli device show eth0
GENERAL.DEVICE:                         eth0
GENERAL.TYPE:                           ethernet
GENERAL.HWADDR:                         00:0C:29:4B:64:C8
......
IP4.ADDRESS[1]:                         10.0.0.150/24
IP4.GATEWAY:                            10.0.0.2
IP4.ROUTE[1]:                           dst = 10.0.0.0/24, nh = 0.0.0.0, mt = 100
IP4.ROUTE[2]:                           dst = 0.0.0.0/0, nh = 10.0.0.2, mt = 100
IP4.DNS[1]:                             223.5.5.5
......
```

连接设备

```bash
[root@rocky ~]# nmcli device connect virbr0
Device 'virbr0' successfully activated with '479fd15f-c547-4287-89b0-119638dbceda'.

[root@rocky ~]# nmcli device status
DEVICE  TYPE      STATE      CONNECTION
eth0    ethernet  connected  eth0
virbr0  bridge    connected  virbr0
lo      loopback  unmanaged  --

[root@rocky ~]# nmcli connection
NAME    UUID                                  TYPE      DEVICE
eth0    a014bcdc-4e4d-4ce1-accc-ec76cc0ec4db  ethernet  eth0
virbr0  479fd15f-c547-4287-89b0-119638dbceda  bridge    virbr0
```

断开设备

```bash
[root@rocky ~]# nmcli device disconnect virbr0
Device 'virbr0' successfully disconnected.

[root@rocky ~]# nmcli device
DEVICE  TYPE      STATE         CONNECTION
eth0    ethernet  connected     eth0
virbr0  bridge    disconnected  --
lo      loopback  unmanaged     --
```

## 16. 网卡配置文件格式

### 16.1 Rocky

文件名：`/etc/sysconfig/network-scripts/ifcfg-<interface-name>`

常用选项

| 选项                 | 描述                                               |
| -------------------- | -------------------------------------------------- |
| TYPE                 | 设备类型，默认为 Ethernet                           |
| NAME                 | 名称                                               |
| DEVICE               | 设备名                                             |
| IPADDR               | IPv4 地址                                            |
| PREFIX               | 网络前缀                                           |
| GATEWAY              | 默认网关                                           |
| DNS{1,2}             | DNS 服务器地址                                      |
| HWADDR               | 网卡的硬件物理地址                                           |
| MACADDR              | 可以覆盖 HWADDR，但不能同时使用                                           |
| ONBOOT=yes\|no       | 是否在系统启动时自动激活                           |
| BOOTPROTO=none\|dhcp | `none` 表示手动配置 IP 地址，`dhcp` 表示使用 DHCP 自动获取配置 |

静态网络配置示例

```bash
TYPE=Ethernet
BOOTPROTO=none
NAME=eth0
DEVICE=eth0
ONBOOT=yes
IPADDR=10.0.0.150
PREFIX=24
GATEWAY=10.0.0.2
DNS1=223.5.5.5
```

动态网络配置示例

```bash
TYPE=Ethernet
BOOTPROTO=dhcp
NAME=eth0
DEVICE=eth0
ONBOOT=yes
```

应用配置

```bash
# 重新读取连接配置，并加载到内存
$ nmcli connection reload

# 重新激活连接
$ nmcli connection up eth0
```

### 16.2 Ubuntu

>自 Ubuntu 18.04 版本开始，系统使用 Netplan 工具配置网络

文件名：`/etc/netplan/*.yaml`

静态网络配置示例

```yaml
network:
  version: 2
  ethernets:
    eth0:
      addresses:
        - 10.0.0.151/24
      nameservers:
        addresses:
          - 223.5.5.5
      routes:
        - to: default
          via: 10.0.0.2
```

动态网络配置示例

```yaml
network:
  version: 2
  ethernets:
    eth0:
      dhcp4: true
```

应用配置

```bash
$ netplan apply
```

## 17. 配置 bond0

**网络绑定**（network bonding）是组合网卡的方法，以提供一个高吞吐量或冗余的逻辑接口

不同的网络绑定模式：

Balance-rr (Mode 0)

- `balance-rr` 使用轮询算法，按顺序地从第一个到最后一个可用端口传输数据包。该模式提供负载均衡和容错

Active-backup (Mode 1)

- `Active-backup` 确保 bond 中只有一个端口是活动的。当且仅当活动端口出现故障时，才会切换到另一个端口。bond 对外显示活动端口的 MAC 地址。该模式提供容错并且不要求任何交换机配置

Balance-xor (Mode 2)

- `Balance-xor` 使用所选择的传输哈希策略传输数据包。默认策略是 \[( 源 MAC 地址 XOR 目的 MAC 地址 XOR 包类型 ID) % 端口数量 ]。该模式提供负载均衡和容错

Broadcast (Mode 3)

- `Broadcast` 所有数据包被广播到所有端口。该模式提供容错

802.3ad (Mode 4)

- `802.3ad` 使用 IEEE 标准动态链路聚合策略。创建具有相同速度和双工设置的聚合组，利用聚合组中的所有端口。对于出站流量，根据传输哈希策略来选择端口。该模式提供负载均衡和容错

Balance-tlb (Mode 5)

- `balance-tlb` 使用自适应传输负载均衡策略，该个模式提供容错、负载平衡，且不需要任何特殊交换机支持不要求任何特殊交换机支持。活动端口接收入站流量，如果活动端口故障，另一个端口接管故障端口的 MAC 地址。`tlb_dynamic_lb=0` 使用哈希分发策略在不进行负载均衡的情况下分发出站流量。`tlb_dynamic_lb=1` 利用负载均衡将出站流量分发到每个端口

Balance-alb (Mode 6)

- `balance-alb` 使用自适应负载均衡策略。该模式提供容错、负载平衡，且不需要任何特殊交换机支持。包含 `balance-tlb` 和 IPv4 和 IPv6 流量接收负载均衡

### 17.1 配置文件

bond0 的配置文件

```bash
[root@rocky network-scripts]# cat ifcfg-bond0
NAME=bond0
TYPE=bond
DEVICE=bond0
BOOTPROTO=none
IPADDR=192.168.10.100
PREFIX=24
BONDING_OPTS="mode=1 miimon=100"
```

bon0 端口的配置文件

```bash
[root@rocky network-scripts]# cat ifcfg-bond0-port1
NAME=bond0-port1
DEVICE=eth1
BOOTPROTO=none
MASTER=bond0
SLAVE=yes
ONBOOT=yes

[root@rocky network-scripts]# cat ifcfg-bond0-port2
NAME=bond0-port2
DEVICE=eth2
BOOTPROTO=none
MASTER=bond0
SLAVE=yes
ONBOOT=yes
```

重启 NetworkManager 服务

```bash
[root@rocky network-scripts]# systemctl restart NetworkManager.service
```

查看 bond0 的状态

```bash
[root@rocky network-scripts]# cat /proc/net/bonding/bond0
Ethernet Channel Bonding Driver: v3.7.1 (April 27, 2011)

Bonding Mode: fault-tolerance (active-backup)
Primary Slave: None
Currently Active Slave: eth1
MII Status: up
MII Polling Interval (ms): 100
Up Delay (ms): 0
Down Delay (ms): 0
Peer Notification Delay (ms): 0

Slave Interface: eth1
MII Status: up
Speed: 1000 Mbps
Duplex: full
Link Failure Count: 0
Permanent HW addr: 00:0c:29:4b:64:d2
Slave queue ID: 0

Slave Interface: eth2
MII Status: up
Speed: 1000 Mbps
Duplex: full
Link Failure Count: 0
Permanent HW addr: 00:0c:29:4b:64:dc
Slave queue ID: 0
```

### 17.2 命令

创建 bond 接口

```bash
[root@rocky ~]# nmcli connection add type bond con-name bond0 ifname bond0 bond.options "mode=active-backup"
Connection 'bond0' (d592df44-0a1a-479e-90e7-f900ed95e5d7) successfully added.
```

为 bond0 的端口创建新的连接

```bash
[root@rocky ~]# nmcli connection add type bond-slave con-name bond0-port1 ifname eth1 master bond0
Connection 'bond0-port1' (f0d4eef5-6fcb-451c-9bb2-9f450a30376f) successfully added.

[root@rocky ~]# nmcli connection add type bond-slave con-name bond0-port2 ifname eth2 master bond0
Connection 'bond0-port2' (038984c1-f1bf-46ec-a37c-6dda9899adc8) successfully added.
```

bond0 设置静态 IP 地址

```bash
[root@rocky ~]# nmcli connection modify bond0 ipv4.addresses 192.168.10.100/24 ipv4.method manual
```

激活连接

```bash
[root@rocky ~]# nmcli connection up bond0
Connection successfully activated (master waiting for slaves) (D-Bus active path: /org/freedesktop/NetworkManager/ActiveConnection/11)
```

查看 bond0 的状态

```bash
# cat /proc/net/bonding/bond0
Ethernet Channel Bonding Driver: v3.7.1 (April 27, 2011)

Bonding Mode: fault-tolerance (active-backup)
Primary Slave: None
Currently Active Slave: eth2
MII Status: up
MII Polling Interval (ms): 100
Up Delay (ms): 0
Down Delay (ms): 0
Peer Notification Delay (ms): 0

Slave Interface: eth2
MII Status: up
Speed: 1000 Mbps
Duplex: full
Link Failure Count: 0
Permanent HW addr: 00:0c:29:4b:64:dc
Slave queue ID: 0

Slave Interface: eth1
MII Status: up
Speed: 1000 Mbps
Duplex: full
Link Failure Count: 0
Permanent HW addr: 00:0c:29:4b:64:d2
Slave queue ID: 0
```

## 18. 使用 ifconfig 命令获取网卡的 IP 地址

```bash
[root@rocky ~]# ifconfig eth0 | sed -n '2p' | awk '{print $2}'
10.0.0.150
```

## 19. Shell 编程：检测当前主机所在网络中活动的 IP 地址

脚本内容如下

```bash
#!/usr/bin/bash

network=`ifconfig eth0 | sed -n '2p' | awk '{print $2}' | cut -d '.' -f 1-3`

for host in {1..254}; do
    ip=$network.$host
    (
    if ping -c1 -w1 $ip &> /dev/null; then
        echo -e "$ip\t[\e[32;1m ACTIVE \e[0m]"
    fi
    ) &
done
```

脚本执行结果

```bash
[root@rocky scripts]# ./scan_host.sh
10.0.0.2        [ ACTIVE ]
10.0.0.150      [ ACTIVE ]
10.0.0.151      [ ACTIVE ]
```

>[!note]
>**子 shell**：由父 shell 管理的子进程
>
>( ) 中的命令在子 shell 中执行，& 将子 shell 放入后台执行，这些子 shell 可以并行执行，提高脚本执行速度


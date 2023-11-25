
## 1. 计算机基础



### 服务器硬件组成

**CPU**

中央处理器（Gentral Processing Unit，CPU）是计算机的核心组件之一，被认为是计算机的大脑。负责解释和执行计算机指令以及处理计算机软件中的数据

**主板**

主板（Mainboard）是构成计算机的主要电路板。主板的主要功能是提供各种接口和插槽，用于连接和集成计算机的各个组件和设备，例如处理器、内存、存储设备、显卡、声卡、网卡等

**内存**

随机访问存储器（Random Access Memory，RAM）是计算机中的一种内部存储器（内存），用于与 CPU 直接交换数据。它可以随时读写，而且速度很快，通常作为操作系统和其它正在运行中的程序的临时数据存储介质

**硬盘**



**网卡**






## 2. VMware Workstation 安装 Linux 系统

### 安装前准备

- 确保电脑开启虚拟化功能（默认已开启）
- 下载 ISO 文件
	- Rocky 8：[桌面版](https://dl.rockylinux.org/vault/rocky/8.8/isos/x86_64/Rocky-8.8-x86_64-dvd1.iso) | [服务器版](https://dl.rockylinux.org/vault/rocky/8.8/isos/x86_64/Rocky-8.8-x86_64-minimal.iso)
	- Ubuntu 22.04：[桌面版](https://releases.ubuntu.com/jammy/ubuntu-22.04.3-desktop-amd64.iso) | [服务器版](https://releases.ubuntu.com/jammy/ubuntu-22.04.3-live-server-amd64.iso)


### 创建虚拟机

1.选择 "文件 -> 新建虚拟机"

[//]:
![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231114230942.png){: width="650"}

2.选择"自定义安装"，点击 "下一步"

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231115141942.png)

3."硬件兼容性" 默认，点击 "下一步"

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231115142255.png)

4.选择 "稍后安装操作系统"，点击 "下一步"

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231115142427.png)

5.客户机操作系统选择 "Linux"，版本选择 "Ubuntu 64 位"（Rocky 选择 "CentOS 8 64 位"），点击 "下一步"

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231115142612.png)

6.填写 "虚拟机名称"，选择虚拟机目录存放的 "位置"，点击 "下一步"

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231115142914.png)

7.选择 "处理器数量"，点击 "下一步"

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231115143006.png)


8."此虚拟机的内存" 填写 2048 MB，点击 "下一步"

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231115143317.png)

9."网络类型" 选择 "使用网络地址转换(NAT)"，点击 "下一步"

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231115143741.png)

10."SCSI 控制器" 默认，点击 "下一步"

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231115144642.png)

11."虚拟磁盘类型" 默认，点击 "下一步"

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231115144719.png)

12.选择 "创建新虚拟磁盘"，点击 "下一步"

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231115144954.png)

12."最大磁盘大小" 填写 "100GB"，不要勾选 "立即分配所有磁盘空间"，点击 "下一步"

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231115145242.png)

13."磁盘文件" 名默认，点击 "下一步"

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231115145329.png)

14.点击 "完成"

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231115145403.png)

### 安装操作系统

#### Rocky 8

1.点击 "编辑虚拟机设置"

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231120214439.png)

2.点击 "CD/DVD"，选择下载的 ISO 文件，点击 "确定"

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231120214328.png)

3.点击 "开启此虚拟机"

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231120214539.png)

4.选择 "Install Rocky Linux 8.8"，回车

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231120215026.png)

5.语言选择 "English"

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231120215437.png)

6.安装目标选择 "sda"，点击 "Done"

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231120220350.png)

7.时区选择 "Asia/Shanghai"，点击 "Done"

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231120220507.png)

8.激活网卡连接，可以看到动态获取的 IP 地址，点击 "Done"

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231120220622.png)

9.设置 root 密码，点击 "Done"

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231120220832.png)

10.创建一个用户并设置密码

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231120220935.png)

11.开始安装，点击 "Begin Installation"

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231120221155.png)

12.安装完成，重启

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231120224141.png)

13.接受许可协议，点击 "Done"，最后点击 "FINISH CONFIGUARTION"

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231120224330.png)

14.登录

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231120224602.png)

#### Ubuntu 22.04

1.点击 "编辑虚拟机设置"

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231115153757.png)

2.点击 "CD/DVD"，选择下载的 ISO 文件，点击 "确定"

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231115154024.png)

3.点击 "开启此虚拟机"

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231115154148.png)

4.选择 "Try or install Ubuntu Server"，回车

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231115154254.png)

5.语言选择 "English"，回车

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231115154449.png)

6.选择 "Continue without updating"，回车

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231115154633.png)

7.选择 "Done"，回车

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231115154810.png)

8.选择 "Done"，回车

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231115154937.png)
![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231115155029.png)
![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231115155133.png)

9.镜像地址修改为国内的软件下载源（https://mirrors.aliyun.com/ubuntu）

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231115155335.png)

10.选择 "Done"，回车

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231115155539.png)
![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231115155643.png)
![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231115155829.png)

11.配置主机名、用户和密码

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231115160030.png)

12.选择 "Install OpenSSh server"

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231115160205.png)

13.开始安装

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231115160244.png)


14.安装完成，重启

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231115163003.png)

12.登录

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231115163255.png)

### 远程登录

查看虚拟机 的 IP 地址

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231120223750.png)

XShell 创建新的会话

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231120223510.png)
![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231120223922.png)

点击 "连接"

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231120224054.png)

>[!TIP]
>Ubuntu 系统默认情况下不允许以 root 用户的身份进行 SSH 远程登录

## 3. Linux 常用基本命令

### 获取帮助

`whatis`：显示命令的简短描述
```bash
# 更新 man 页面索引数据库缓存
[root@rocky ~]# man mandb

[root@rocky ~]# whatis ls
ls (1)               - list directory contents
ls (1p)              - list directory contents
```

`whereis`：查找命令或系统文件的路径，以及对应的 man 文档的路径
```bash
[root@rocky ~]# whereis ls
ls: /usr/bin/ls /usr/share/man/man1/ls.1.gz /usr/share/man/man1p/ls.1p.gz
```

`help`：内部命令的帮助
```bash
[root@rocky ~]# help echo
echo: echo [-neE] [arg ...]
    Write arguments to the standard output.
    
    Display the ARGs, separated by a single space character and followed by a
    newline, on the standard output.
......
```

外部命令的 `--help` 或 `-h` 选项
```bash
[root@rocky ~]# ls --help
Usage: ls [OPTION]... [FILE]...
List information about the FILEs (the current directory by default).
Sort entries alphabetically if none of -cftuvSUX nor --sort is specified.
......
```


`man`：查看帮助手册和文档

man 手册的章节号：
- 1 可执行程序或 shell 命令
- 2 系统调用（内核提供的函数）
- 3 库调用（程序库中的函数）
- 4 特殊文件（通常位于 /dev）
- 5 文件格式和规范，例如 /etc/passwd
- 6 游戏
- 7 杂项
- 8 系统管理命令（通常只针对 root 用户）
- 9 内核 API

```bash
$ man passwd      # 默认章节为 1
$ man 5 passwd    # 指定章节
$ man -f passwd   # 显示包含 “passwd” 相关的手册页
```

### 硬件信息

显示 CPU 相关信息
```bash
[root@rocky ~]# lscpu
Architecture:        x86_64
CPU op-mode(s):      32-bit, 64-bit
Byte Order:          Little Endian
CPU(s):              2        # CPU 数量
On-line CPU(s) list: 0,1
Thread(s) per core:  1
Core(s) per socket:  1
Socket(s):           2
NUMA node(s):        1
Vendor ID:           AuthenticAMD
BIOS Vendor ID:      AuthenticAMD
CPU family:          23
Model:               17
Model name:          AMD Ryzen 5 2500U with Radeon Vega Mobile Gfx
BIOS Model name:     AMD Ryzen 5 2500U with Radeon Vega Mobile Gfx  
Stepping:            0
CPU MHz:             1996.250
BogoMIPS:            3992.50
Hypervisor vendor:   VMware
Virtualization type: full
L1d cache:           32K
L1i cache:           64K
L2 cache:            512K
L3 cache:            4096K
NUMA node0 CPU(s):   0,1
......

[root@rocky ~]# cat /proc/cpuinfo
......
```

显示内存使用情况
```bash
[root@rocky ~]# free -h
              total        used        free      shared  buff/cache   available
Mem:          1.7Gi       558Mi       674Mi        10Mi       514Mi       1.0Gi
Swap:         2.0Gi          0B       2.0Gi

[root@rocky ~]# cat /proc/meminfo 
MemTotal:        1789220 kB
MemFree:          691100 kB
MemAvailable:    1052560 kB
......
```

列出块设备相关信息（磁盘和分区）
```bash
[root@rocky ~]# lsblk 
NAME        MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda           8:0    0  200G  0 disk 
├─sda1        8:1    0    1G  0 part /boot
└─sda2        8:2    0  199G  0 part 
  ├─rl-root 253:0    0   70G  0 lvm  /
  ├─rl-swap 253:1    0    2G  0 lvm  [SWAP]
  └─rl-home 253:2    0  127G  0 lvm  /home
sr0          11:0    1 11.8G  0 rom  

[root@rocky ~]# cat /proc/partitions 
major minor  #blocks  name

   8        0  209715200 sda
   8        1    1048576 sda1
   8        2  208665600 sda2
  11        0   12316672 sr0
 253        0   73400320 dm-0
 253        1    2117632 dm-1
 253        2  133144576 dm-2
```

显示文件系统和磁盘空间使用情况
```bash
[root@rocky ~]# df -h
Filesystem           Size  Used Avail Use% Mounted on
devtmpfs             845M     0  845M   0% /dev
tmpfs                874M     0  874M   0% /dev/shm
tmpfs                874M  9.3M  865M   2% /run
tmpfs                874M     0  874M   0% /sys/fs/cgroup
/dev/mapper/rl-root   70G  5.8G   65G   9% /
/dev/mapper/rl-home  127G  939M  126G   1% /home
/dev/sda1           1014M  265M  750M  27% /boot
tmpfs                175M   12K  175M   1% /run/user/42
tmpfs                175M     0  175M   0% /run/user/0
```

### 日期和时间

`date`：显示或设置系统时间

常用格式
- `%s`：时间戳
- `%F`：完整的日期，等同于 `%Y-%m-%d`
- `%T`：时间，等同于 `%H:%M:%S`
```bash
[root@rocky ~]# date
Mon Nov 20 16:58:35 CST 2023
[root@rocky ~]# date +%s
1700470732
[root@rocky ~]# date +'%F_%T'
2023-11-20_16:59:33
```

`clock`：显示硬件时钟

常用选项
- `-s`：根据硬件时钟同步系统时钟
- `-w`：根据系统时钟同步硬件时钟
```bash
[root@rocky ~]# clock
2023-11-20 17:11:30.241621+08:00
```

`timedatectl`：查询或改变时区

常用子命令
- `list-timezones`：显示已知的时区
- `set-timezone`：设置系统时区
- `status`：显示当前时间设置
```bash
[root@rocky ~]# timedatectl list-timezones
......

[root@rocky ~]# timedatectl status
               Local time: Mon 2023-11-20 17:15:18 CST
           Universal time: Mon 2023-11-20 09:15:18 UTC
                 RTC time: Mon 2023-11-20 09:15:19
                Time zone: Asia/Shanghai (CST, +0800)
System clock synchronized: no
              NTP service: inactive
          RTC in local TZ: no

[root@ubuntu2204 ~]# timedatectl set-timezone Asia/Shanghai

[root@rocky ~]# ll /etc/localtime 
lrwxrwxrwx. 1 root root 35 Nov  3 21:05 /etc/localtime -> ../usr/share/zoneinfo/Asia/Shanghai
```

### 操作系统版本信息

#### 查看内核版本

```bash
[root@rocky ~]# uname -r
4.18.0-477.10.1.el8_8.x86_64

root@ubuntu:~# uname -r
5.15.0-88-generic
```

#### 查看操作系统版本

Rocky
```bash
[root@rocky ~]# cat /etc/redhat-release 
Rocky Linux release 8.8 (Green Obsidian)

[root@rocky ~]# cat /etc/os-release 
NAME="Rocky Linux"
VERSION="8.8 (Green Obsidian)"
ID="rocky"
ID_LIKE="rhel centos fedora"
VERSION_ID="8.8"
PLATFORM_ID="platform:el8"
PRETTY_NAME="Rocky Linux 8.8 (Green Obsidian)"
ANSI_COLOR="0;32"
LOGO="fedora-logo-icon"
CPE_NAME="cpe:/o:rocky:rocky:8:GA"
HOME_URL="https://rockylinux.org/"
BUG_REPORT_URL="https://bugs.rockylinux.org/"
SUPPORT_END="2029-05-31"
ROCKY_SUPPORT_PRODUCT="Rocky-Linux-8"
ROCKY_SUPPORT_PRODUCT_VERSION="8.8"
REDHAT_SUPPORT_PRODUCT="Rocky Linux"
REDHAT_SUPPORT_PRODUCT_VERSION="8.8"

[root@rocky ~]# dnf install redhat-lsb-core
[root@rocky ~]# lsb_release -a
LSB Version:    :core-4.1-amd64:core-4.1-noarch
Distributor ID: Rocky
Description:    Rocky Linux release 8.8 (Green Obsidian)
Release:        8.8
Codename:       GreenObsidian
```

Ubuntu
```bash
root@ubuntu:~# cat /etc/os-release 
PRETTY_NAME="Ubuntu 22.04.2 LTS"
NAME="Ubuntu"
VERSION_ID="22.04"
VERSION="22.04.2 LTS (Jammy Jellyfish)"
VERSION_CODENAME=jammy
ID=ubuntu
ID_LIKE=debian
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
UBUNTU_CODENAME=jammy

root@ubuntu:~# lsb_release -a
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 22.04.2 LTS
Release:        22.04
Codename:       jammy
```

### 关机和重启

关机
- `shutdown`
- `poweroff`
- `init 0`
- `shutdown -h now`

重启
- `reboot`
- `init 6`
- `shutdown -r now`

### 用户登录信息

`whoami`：显示与当前有效用户 ID 关联的用户名
```bash
[root@rocky ~]# whoami 
root
```

`who`：显示所有登录到系统上的用户相关信息
```bash
[root@rocky ~]# who
root     pts/0        2023-11-20 16:39 (10.0.0.1)
zqh      pts/1        2023-11-20 17:55 (10.0.0.1)
```

`w`：显示所有登录到系统上的用户详细信息
```bash
[root@rocky ~]# w
 17:55:51 up  4:24,  3 users,  load average: 0.08, 0.02, 0.01
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
root     pts/0    10.0.0.1         16:39    0.00s  0.34s  0.00s w
zqh      pts/1    10.0.0.1         17:55    2.00s  0.02s  0.02s -bash
```
输出结果的字段解释
- `USER`：登录用户的用户名
- `TTY`：登录用户使用的终端
- `FROM`：登录用户的来源，即用户的主机 IP 地址或 TTY 设备（控制台登录）
- `LOGIN@`：用户登录到系统的时间
- `IDLE`：用户在终端上的空闲时间
- `JCPU`：用户在所有终端上运行的 CPU 时间
- `PCPU`：用户在当前终端上运行的 CPU 时间
- `WHAT`：用户当前正在运行的命令

### 输出信息

`echo`：显示文本

常用选项
- `-n`：不换行
- `-e`：解释反斜杠转义字符

常见转义字符
- `\n`：光标移动到下一行行首
- `\r`：光标移动到当前行行首
- `\t`：制表符
- `\\`："\\" 字符本身

```bash
[root@rocky88 ~]# echo hello
hello

# 不换行
[root@rocky88 ~]# echo -n hello
hello[root@rocky88 ~]# 

# 转义字符
[root@rocky88 ~]# echo -e "a\x0Ab"
a
b

# 输出变量的值
[root@rocky88 ~]# echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin
[root@rocky88 ~]# echo '$PATH'
$PATH
[root@rocky88 ~]# echo "$PATH"
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin
```

## 4. Linux 文件系统

### 文件系统目录结构

Linux 文件系统采用层级目录结构，从根目录（/）开始，使用斜杠（/）分隔，逐级向下组织

常见的文件系统目录及描述：
- `/bin`：包含所有用户都可以执行的命令
- `/boot`：包含引导加载程序的所需静态文件
- `/dev`：包含特殊文件和设备文件
- `/etc`：包含系统和应用程序的配置文件
- `/home`：用户家目录
- `/lib`：包含启动系统和运行命令需要的共享库文件和内核模块
- `/media`：移动设备的挂载点
- `/mnt`：临时的文件系统挂载点
- `/opt`：第三方应用程序的安装位置
- `/root`：系统管理员的家目录
- `/sbin`：仅限系统管理员使用的系统命令
- `/srv`：存放系统服务提供的数据
- `/tmp`：存放临时文件
- `/usr`：包含可共享、只读的数据
	- `/usr/bin`：系统上可执行命令的主要目录
	- `/usr/sbin`：仅限系统管理员使用的非必要二进制文件
	- `/usr/lib`：包含用于编程的库和内部二进制文件
	- `/usr/local`：系统管理员编译安装软件的位置
	- `/usr/share`：包含所有只读的独立数据文件，例如应用程序的文档帮助
- `/var`：包含变量数据文件
	- `/var/cache`：存放应用程序缓存数据
	- `/var/lib`：与应用程序或系统相关的状态数据
	- `/var/lock`：存放锁文件
	- `/var/log`：存放系统和应用程序的日志文件
	- `/var/run`：存放包含系统信息数据
	- `/var/spool`：包含等待某种后续处理的数据，例如邮件
	- `/var/tmp`：存放两次系统重启之间的临时数据
- `/proc`：包含内核和进程信息的虚拟文件系统
- `/sys`：包含与硬件相关信息的虚拟文件系统

### 文件类型

- `.`：普通文件，普通的文本文件或二进制文件
- `d`：目录文件，用于存储其它文件和目录
- `l`：符号链接（symbolic link）文件，也称为软链接。类似于 Windows 中的快捷方式
- `b`：块设备文件，例如硬盘或 USB 设备等。块设备以固定大小的块为单位进行数据读写
- `c`：字符设备文件，例如终端、打印机等。字符设备以字符为单位进行数据读写
- `p`：管道文件，也称为命名管道，用于进程间通信。可以将一个进程的输出连接到另一个进程的输入
- `s`：套接字文件，用于在网络上进行进程间通信，允许不同主机上的进程进行网络通信

### 文件相关操作

**显示当前工作目录**

`pwd`：打印工作目录（print working directory）
```bash
[root@rocky88 ~]# pwd
/root
[root@rocky88 ~]# cd /usr/bin
[root@rocky88 bin]# pwd
/usr/bin
```

**绝对路径和相对路径**

- 绝对路径：从根目录（/）开始的完整路径
- 相对路径：相对于当前工作目录的路径

`basename`：提取文件路径的文件名部分
```bash
[root@rocky88 ~]# basename /etc/sysconfig/network
network

[root@rocky88 ~]# basename http://nginx.org/download/nginx-1.18.0.tar.gz
nginx-1.18.0.tar.gz
```

`dirname`：提取文件路径的目录部分
```bash
[root@rocky88 ~]# dirname /etc/sysconfig/network
/etc/sysconfig
```

**更改目录**

`cd`：更改目录（change directory）
- `cd ..`：将当前工作目录更改为上一级目录
- `cd`：将当前工作目录更改为当前用户的家目录
- `cd -`：将当前工作目录更改为上一次所在的目录
```bash
[root@rocky88 ~]# cd /etc/sysconfig
[root@rocky88 sysconfig]# pwd
/etc/sysconfig
[root@rocky88 sysconfig]# cd ..
[root@rocky88 etc]# pwd
/etc
[root@rocky88 etc]# cd
[root@rocky88 ~]# pwd
/root
[root@rocky88 ~]# cd -
/etc
```

相关环境变量
- `$PWD`：当前工作目录
- `$OLDPWD`：上一次所在的工作目录
```bash
[root@rocky88 ~]# echo $OLDPWD
/etc/sysconfig
[root@rocky88 ~]# cd -
/etc/sysconfig
[root@rocky88 sysconfig]# pwd
/etc/sysconfig
```

**列出目录内容**

`ls`：列出关于文件的信息

常用选项
- `-a`：显示所有文件，包括隐藏文件
- `-d`：显示目录本身，而不是列出目录中的内容
- `-F`：在文件和目录名后面添加一个特殊字符（\*/=>@|），表示其类型
- `-l`：以长格式显示文件和目录的详细信息
- `-R`：递归地列出指定目录下的所有文件和目录
- `-S`：按文件大小排序，最大的在前面
- `-t`：按文件的修改时间（mtime）排序，最近修改的文件在前面
- `-u`：按文件的访问时间（atime）排序，最近访问的文件在前面

**查看文件状态**

`stat`：显示文件状态信息
```bash
[root@rocky88 ~]# stat /etc/passwd
  File: /etc/passwd
  Size: 2554            Blocks: 8          IO Block: 4096   regular file
Device: fd00h/64768d    Inode: 136165732   Links: 1
Access: (0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
Context: system_u:object_r:passwd_file_t:s0
Access: 2023-11-09 19:35:19.474775151 +0800
Modify: 2023-11-07 16:36:51.781020421 +0800
Change: 2023-11-07 16:36:51.783020421 +0800
 Birth: 2023-11-07 16:36:51.781020421 +0800
```
- `atime`：最后一次读取文件的时间
- `mtime`：最后一次修改文件内容的时间
- `ctime`：最后一次修改文件元数据的时间（例如权限、所有者等）

**确定文件类型**

`type`：确定文件类型

常用选项
- `-f`：从指定的文件读取要检查的文件名列表
- `-L`：显示软链接所指向的文件的类型

Windows 和 Linux 的文本格式
```bash
[root@rocky88 data]# cat linux.txt 
a
b
c
[root@rocky88 data]# cat win.txt 
a
b
c[root@rocky88 data]# file linux.txt win.txt 
linux.txt: ASCII text
win.txt:   ASCII text, with CRLF line terminators    # 回车换行符

# 格式转换
[root@rocky88 data]# dos2unix win.txt 
dos2unix: converting file win.txt to Unix format...
[root@rocky88 data]# file win.txt 
win.txt: ASCII text

[root@rocky88 data]# unix2dos win.txt 
unix2dos: converting file win.txt to DOS format...
[root@rocky88 data]# file win.txt 
win.txt: ASCII text, with CRLF line terminators
```

**文件通配符**

常用的通配符
- `*`：匹配 0 个或多个任意字符
- `?`：匹配 1 个任意字符
- `~`：当前用户的家目录
- `[0-9]`：匹配 1 个数字字符
- `[a-z]`：匹配 1 个小写字母字符
- `[A-Z]`：匹配 1 个大写字母字符
- `[abc]`：匹配 1 个指定的字符
- `[^abc]`：匹配除指定字符以外的任意字符
- `[^a-z]`：匹配除小写字母以外的任意字符

Linux 系统中预定义的字符类别（character class）
- `[:digit:]`：匹配任意数字字符
- `[:lower:]`：匹配任意小写字母字符
- `[:upper:]`：匹配任意大写字母字符
- `[:alpha:]`：匹配任意字母字符
- `[:blank:]`：匹配空格和制表符字符
- `[:space:]`：匹配任意空白字符，包括空格、制表符、换行符、回车符等

```bash
[root@rocky88 data]# touch file{a..c}.txt file{A..C}.txt file{0..9}.txt
[root@rocky88 data]# ls file[a-c].txt
filea.txt  fileA.txt  fileb.txt  fileB.txt  filec.txt
[root@rocky88 data]# ls file[A-C].txt
fileA.txt  fileb.txt  fileB.txt  filec.txt  fileC.txt
[root@rocky88 data]# ls file[[:lower:]].txt
filea.txt  fileb.txt  filec.txt
[root@rocky88 data]# ls file[[:upper:]].txt
fileA.txt  fileB.txt  fileC.txt
```

**创建空文件或更新文件时间**

`touch`：更新文件的访问和修改时间，文件不存在则创建文件
- `-a`：仅更新文件访问时间
- `-m`：仅更新文件修改时间
```bash
[root@rocky88 ~]# ll /etc/issue
-rw-r--r--. 1 root root 23 Nov  7 22:27 /etc/issue
[root@rocky88 ~]# touch /etc/issue
[root@rocky88 ~]# ll /etc/issue
-rw-r--r--. 1 root root 23 Nov 11 13:01 /etc/issue
```

**复制**

`cp`：复制文件或目录

常用选项
- `-a`：复制时保留文件的所有属性
- `-b`：如果目标文件已存在，先备份目标文件再复制源文件
- `-f`：强制复制，如果目标文件已存在，在不询问用户的情况下覆盖目标文件
- `-i`：交互式复制，如果目标文件已存在，询问用户是否覆盖目标文件
- `-p`：复制时保留文件的权限、所有权和时间戳属性
- `-r`：递归复制目录及目录下的所有文件和子目录``

**移动或重命名**

`mv`：移动和重命名文件或目录

常用选项
- `-b`：如果目标文件已存在，先备份目标再移动源文件
- `-f`：如果目标文件已存在，在不询问用户的情况下覆盖目标文件
- `-i`：如果目标文件已存在，询问用户是否覆盖目标文件

`rename` 批量重命名文件
```bash
# 将 txt 后缀的改为 bak 后缀
$ rename 's/txt/bak/g' *.txt
```

**删除**

`rm`：删除文件或目录

常用选项
- `-f`：强制删除，不询问用户
- `-r`：递归删除

```bash
[root@rocky88 ~]$ ls -l ./-f
-rw-r--r--. 1 root root 0 Nov 14 10:26 ./-f
[root@rocky88 ~]$ rm ./-f    # 或者 rm -- -f
rm: remove regular empty file './-f'? y
```

### 目录相关操作

**创建目录**

`mkidr`：创建目录

常用选项
- `-p`：递归创建目录
``

### 索引节点

#### 索引节点

**索引节点**（Index node）是一种数据结构，用于存储文件对象（文件或目录）的属性和指向磁盘块位置的指针。每个文件系统都有一个独立的索引节点表，其中的每个文件都分配一个索引节点，每个索引节点都有唯一的编号，用于标识和访问文件对象

目录级索引节点的主要作用是记录其子目录和文件的索引号，而不是直接存储子目录和文件的元数据

inode 中具有以下属性：
- 文件名
- 文件类型
- inode 号
- 权限
- 硬链接数量
- 所有者
- 所属组
- 块数量
- 文件大小
- 最后访问时间、最后修改和最后状态更改时间
- 创建时间

#### 硬链接

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231114193252.png)

**硬链接**（Hard link）是一个将文件名与索引节点关联起来的目录级索引节点表条目。因此，新创建的普通文件默认硬链接数为 1

硬链接的特点：
- 无论是原始文件还是硬链接，它们都指向同一个索引节点
- 删除原始文件或一个硬链接文件，硬链接数会减 1，当硬链接数为 0 时，且没有进程正在调用，文件会被删除
- 硬链接只能在相同的文件系统创建，因为每个文件系统维护独立的索引节点表
- 不支持对目录创建硬链接，避免链接环路和引用计数问题

```bash
[root@rocky88 ~]$ ln file file.link
[root@rocky88 ~]$ ll -i file*
202560085 -rw-r--r--. 2 root root 0 Nov 14 13:53 file
202560085 -rw-r--r--. 2 root root 0 Nov 14 13:53 file.link
[root@rocky88 ~]$ rm -f file
[root@rocky88 ~]$ ll -i file*
202560085 -rw-r--r--. 1 root root 0 Nov 14 13:53 file.link
```

#### 符号链接

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231114194034.png)

**符号链接**（Symbolic link）也称为软链接，是一种特殊的文件类型，它的数据块中存放的是路径名（字符串），指向目标文件或目录的位置

软链接的特点：
- 软链接可以指向不同文件系统中的文件或目录
- 软链接文件的大小由取决于目标路径的长度
- 创建软链接时，如果目标文件使用相对路径，是相对于软链接文件的路径，而不是相对于当前工作目录。如果软链接使用相对路径，则是相对于当前工作目录
- 删除软链接不会对目标文件造成影响，删除目标文件则会导致软链接不可用

```bash
[root@rocky88 ~]$ ln -s /root/file ./file.slink
[root@rocky88 ~]$ ll file*
-rw-r--r--. 1 root root  0 Nov 14 13:55 file
lrwxrwxrwx. 1 root root 10 Nov 14 14:00 file.slink -> /root/file
# 删除目标文件
[root@rocky88 ~]$ rm -f file
```
![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231114140155.png)

#### 硬链接 vs 软链接

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231122115401.png)


### IO 重定向和管道

#### 标准输入输出

每个进程都有一个打开的文件描述符表，用于管理与文件和 I/O 资源相关的操作。`0`、`1` 和 `2` 是三个特殊的文件描述符
- `0`：标准输入，进程从该文件描述符中读取数据
- `1`：标准输出，进程将常规输出写入该文件描述符
- `2`：标准错误：进程将错误输出写入该文件描述符
默认情况下，这三个文件描述符与当前终端关联，这意味着程序从终端接收用户的输入，并将输出显示在终端上
```bash
[root@rocky88 ~]$ ll /dev/std*
lrwxrwxrwx. 1 root root 15 Nov 14 17:03 /dev/stderr -> /proc/self/fd/2
lrwxrwxrwx. 1 root root 15 Nov 14 17:03 /dev/stdin -> /proc/self/fd/0
lrwxrwxrwx. 1 root root 15 Nov 14 17:03 /dev/stdout -> /proc/self/fd/1

[root@rocky88 ~]$ ll /proc/self/fd/{0..2}
lrwx------. 1 root root 64 Nov 14 17:05 /proc/self/fd/0 -> /dev/pts/0
lrwx------. 1 root root 64 Nov 14 17:05 /proc/self/fd/1 -> /dev/pts/0
lrwx------. 1 root root 64 Nov 14 17:05 /proc/self/fd/2 -> /dev/pts/0

# 当前终端
[root@rocky88 ~]$ tty
/dev/pts/0
```

#### 重定向

**标准输出和标准错误重定向**

通过使用重定向操作符，标准输出和标准错误信息可以被重定向到文件或其它终端，而不是当前终端
- `>`：将标准输出重定向到文件，覆盖文件中的内容
- `2>`：将标准错误重定向到文件，覆盖文件中的内容
- `&>` 或 `>&`：将标准输出和标准错误都重定向到文件，覆盖文件中的内容
- `>>`：将标准输出追加到文件
- `2>>`：将标准错误追加到文件
- `&>>` 或 `>>&`：将标准输出和标准错误都追加到文件

**标准输入重定向**

`tr`：转换、压缩或删除标准输入中的字符，并写入标准输出

常用选项
- `-d`：删除指定字符。例如 `tr -d 'abc'` 删除出现的所有 `a`、`b` 和 `c` 字符
- `-s`：替换连续重复字符。例如 `tr -s ' '` 将连续出现的空格压缩为单个空格
- `-t`：使用指定的字符转换表替换字符。例如 `tr -t 'abc' 'xyz'`：将字符 `a` 替换为 `x`，将 `b` 替换为 `y`，将字符 `c` 替换为 `z`
- `-c`：保留除指定字符之外的所有字符。例如 `tr -c '0-9'` 将删除所有非数字字符
```bash
[root@rocky88 ~]$ df > df.log
[root@rocky88 ~]$ tr -s ' ' ':' < df.log 
Filesystem:1K-blocks:Used:Available:Use%:Mounted:on
devtmpfs:864296:0:864296:0%:/dev
tmpfs:894608:0:894608:0%:/dev/shm
tmpfs:894608:9452:885156:2%:/run
tmpfs:894608:0:894608:0%:/sys/fs/cgroup
/dev/mapper/rl-root:73364480:6117512:67246968:9%:/
/dev/mapper/rl-home:133079564:975236:132104328:1%:/home
/dev/sda1:1038336:270548:767788:27%:/boot
tmpfs:178920:12:178908:1%:/run/user/42
tmpfs:178920:0:178920:0%:/run/user/0
```

多行标准输入重定向
```bash
[root@rocky88 ~]$ cat > helloworld.txt <<EOF
> Hello
> World
> !
> EOF

[root@rocky88 ~]$ cat helloworld.txt 
Hello
World
!
```

`tee`：将标准输入的内容输出到文件以及标准输出
- `-a`：追加，而不是覆盖
```bash
[root@rocky88 ~]$ cat <<EOF | tee helloworld.txt
> Hello, World
> !
> EOF
Hello, World
!

[root@rocky88 ~]$ cat helloworld.txt 
Hello, World
!
```


高级重定向写法
- `cmd <<< "string"`：Bash 的扩展功能，将字符串作为输入传递给命令
```bash
[root@rocky88 ~]$ tr 'a-z' 'A-Z' <<< 'hello'
HELLO
```

- `cmd1 < <(cmd2)`：Bash 的扩展功能，将命令 1 的输出写入一个临时文件（匿名管道），命令 2 从临时文件读取输入
```bash
[root@rocky88 ~]$ tr 'a-z' 'A-Z' < <(echo 'hello')
HELLO
[root@rocky88 ~]$ ll <(echo 'hello')
lr-x------. 1 root root 64 Nov 14 22:12 /dev/fd/63 -> 'pipe:[73840]'
```

#### 管道

管道（pipe）是一种在命令行中连接多个命令的机制，一个命令的标准输出会被连接到另一个命令的标准输入。管道使用 `|` 表示
```bash
[root@rocky88 ~]$ df -h | tr -s ' '
Filesystem Size Used Avail Use% Mounted on
devtmpfs 845M 0 845M 0% /dev
tmpfs 874M 0 874M 0% /dev/shm
tmpfs 874M 9.3M 865M 2% /run
tmpfs 874M 0 874M 0% /sys/fs/cgroup
/dev/mapper/rl-root 70G 5.9G 65G 9% /
/dev/mapper/rl-home 127G 953M 126G 1% /home
/dev/sda1 1014M 265M 750M 27% /boot
tmpfs 175M 12K 175M 1% /run/user/42
tmpfs 175M 0 175M 0% /run/user/0
```

计算 1 到 100 的数字之和
```bash
[root@rocky88 ~]$ seq -s + 1 100 | bc
5050
```

非交互式设置密码，并且不显示任何输出
```bash
[root@rocky88 ~]$ echo '1234' | passwd --stdin zqh &> /dev/null 
```

## 5. Linux 安全模型

- 认证（Authentication）：认证是验证用户身份的过程。Linux 系统常见的认证方式包括基于密码和基于密钥的认证等方式。只有合法的用户才能访问系统资源
- 授权（Authorization）：授权决定了用户在系统中能够执行哪些操作和访问哪些资源。Linux 系统通过用户和组、权限和安全上下文来限制用户对文件、目录或其它系统资源的访问
- 审计（Audit）：审计是对系统活动和安全事件进行监控和记录。Linux 系统可以将用户登录、文件访问、系统配置等事件记录到日志文件中，以便后续分析

## 6. Linux 文件权限

### 文件所有者和所属组属性

`chown`：修改文件的所有者和/或所属组

常用选项
- `--reference=RFILE`：将指定文件的所有者和所属组属性复制到目标文件或目录上
- `-R`：递归操作指定目录下的文件和子目录
	- `-RH`：只修改软链接本身的所有者和所属组
	- `-RL`：修改软链接和软连接指向的文件或目录的所有者和所属组

```bash
# 修改所有者
[root@rocky ~]# ll a.txt
-rw-r--r-- 1 root root 7 Nov 19 10:33 a.txt
[root@rocky ~]# chown zqh a.txt 
[root@rocky ~]# ll a.txt
-rw-r--r-- 1 zqh root 7 Nov 19 10:33 a.txt

# 使用用户 ID
[root@rocky ~]# id -u zqh
1000
[root@rocky ~]# ll b.txt
-rw-r--r-- 1 root root 4 Nov 19 10:09 b.txt
[root@rocky ~]# chown 1000 b.txt 
[root@rocky ~]# ll b.txt
-rw-r--r-- 1 zqh root 4 Nov 19 10:09 b.txt

# 修改所属组
[root@rocky ~]# ll c.txt 
-rw-r--r-- 1 root root 7 Nov 21 13:17 c.txt
[root@rocky ~]# chown :zqh c.txt 
[root@rocky ~]# ll c.txt 
-rw-r--r-- 1 root zqh 7 Nov 21 13:17 c.txt

# 同时修改所有者和所属组
[root@rocky ~]# ll d.txt 
-rw-r--r-- 1 root root 4 Nov 21 13:17 d.txt
[root@rocky ~]# chown zqh:zqh d.txt 
[root@rocky ~]# ll d.txt 
-rw-r--r-- 1 zqh zqh 4 Nov 21 13:17 d.txt
```

`chgrp`：修改文件的所属组

常用选项
- `--reference=RFILE`：将指定文件的所有者和所属组属性复制到目标文件或目录上
- `-R`：递归操作指定目录下的文件和子目录
	- `-RH`：只修改软链接本身的所有者和所属组
	- `-RL`：修改软链接和软连接指向的文件或目录的所有者和所属组
```bash
[root@rocky ~]# ll e.txt 
-rw-r--r-- 1 root root 7 Nov 21 13:37 e.txt
[root@rocky ~]# chgrp zqh e.txt 
[root@rocky ~]# ll e.txt 
-rw-r--r-- 1 root zqh 7 Nov 21 13:37 e.txt

# 递归操作
[root@rocky ~]# ll /data
total 0
-rw-r--r-- 1 root root 0 Nov 21 13:39 1.txt
-rw-r--r-- 1 root root 0 Nov 21 13:39 2.txt
-rw-r--r-- 1 root root 0 Nov 21 13:39 3.txt
[root@rocky ~]# chgrp -R zqh /data
[root@rocky ~]# ll /data
total 0
-rw-r--r-- 1 root zqh 0 Nov 21 13:39 1.txt
-rw-r--r-- 1 root zqh 0 Nov 21 13:39 2.txt
-rw-r--r-- 1 root zqh 0 Nov 21 13:39 3.txt
```

### 普通权限

权限表示

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231121140846.png)

权限作用

![|650](https://github.com/zqhgithubuser/Homework/blob/main/Week1/images/Pasted_image_20231121171458.png)


`chmod`：修改文件权限

常用选项
- `--reference=RFILE`：将指定文件的所有者和所属组属性复制到目标文件或目录上
- `-R`：递归操作指定目录下的文件和子目录

```bash
[root@rocky data]# chmod a= file1
[root@rocky data]# ll file1
---------- 1 root root 0 Nov 21 17:16 file1

[root@rocky data]# ll file2
-rw-r--r-- 1 root root 0 Nov 21 17:16 file2
[root@rocky data]# chmod u=r,g=w,o=x file2
[root@rocky data]# ll file2
-r---w---x 1 root root 0 Nov 21 17:16 file2

[root@rocky data]# ll file3
-rw-r--r-- 1 root root 0 Nov 21 17:16 file3
[root@rocky data]# chmod 777 file3
[root@rocky data]# ll file3
-rwxrwxrwx 1 root root 0 Nov 21 17:16 file3
```

目录权限
```bash
# r 权限
[zqh@rocky ~]$ ll -d dir
dr-------- 2 zqh zqh 40 Nov 21 17:35 dir

# 无法查看文件属性
[zqh@rocky ~]$ ls dir
ls: cannot access 'dir/file1.txt': Permission denied
ls: cannot access 'dir/file2.txt': Permission denied
file1.txt  file2.txt

# rw 权限
[zqh@rocky ~]$ chmod u+w dir
[zqh@rocky ~]$ ll -d dir
drw------- 2 zqh zqh 40 Nov 21 17:35 dir
[zqh@rocky ~]$ ls dir
ls: cannot access 'dir/file1.txt': Permission denied
ls: cannot access 'dir/file2.txt': Permission denied
file1.txt  file2.txt

# 无法删除文件
[zqh@rocky ~]$ rm dir/file1.txt 
rm: cannot remove 'dir/file1.txt': Permission denied
```

### 默认权限

- 新创建文件的默认权限：666 - umask
- 新创建目录的默认权限：777 - umask

`umask`：查看或设置文件和目录的默认权限掩码
```bash
# 管理员用户
[root@rocky ~]# umask 
0022

# 普通用户
[zqh@rocky ~]$ umask 
0002
```
	
### 特殊权限

特殊权限
- SUID：可执行文件在执行时以文件所有者的权限运行
- SGID：可执行文件在执行时以文件所属组的权限运行。对于目录，在该目录下新创建的文件将继承目录所属组权限
- Sticky Bit：目录中的文件只能由文件所有者或 root 删除或重命名

权限表示
- SUID：s（所有者没有可执行权限，显示为 S），对应八进制数字 4
- SGID：s（所属组没有可执行权限，显示为 S），对应八进制数字 2
- Sticky Bit：t（其他用户没有可执行权限，显示为 T），对应八进制数字 1


设置 SUID 权限

```bash
# 普通用户无法查看 /etc/shadow 文件
[zqh@rocky ~]$ cat /etc/shadow
cat: /etc/shadow: Permission denied

# cat 命令添加 SUID(s) 权限
[root@rocky ~]# chmod u+s /usr/bin/cat
[root@rocky ~]# ll /usr/bin/cat
-rwsr-xr-x. 1 root root 38408 Jan 18  2023 /usr/bin/cat

[zqh@rocky ~]$ cat /etc/shadow
root:$6$NiYJFKLlIlyFtWGr$bkA5G0rL0KN5dZfgSqK6ujJF/sfzXTZ2lcp4d67oemFDKwoixmHzhgoa7/PVJeF9CSicyt2g7XotXmXg1gVSd.::0:99999:7:::
bin:*:19326:0:99999:7:::
daemon:*:19326:0:99999:7:::
......
```

设置 SGID 权限

```bash
[zqh@rocky ~]$ ll -d dir
drwxrwxr-x 2 zqh zqh 6 Nov 21 18:38 dir
[root@rocky zqh]# touch dir/file1
[zqh@rocky ~]$ ll dir
total 0
-rw-r--r-- 1 root root 0 Nov 21 18:39 file1

# 添加 SGID(s) 权限
[zqh@rocky ~]$ chmod g+s dir
[root@rocky zqh]# touch dir/file2
[zqh@rocky ~]$ ll -d dir
drwxrwsr-x 2 zqh zqh 32 Nov 21 18:41 dir
[zqh@rocky ~]$ ll dir
total 0
-rw-r--r-- 1 root root 0 Nov 21 18:39 file1
-rw-r--r-- 1 root zqh  0 Nov 21 18:41 file2
```

设置 Sticky Bit 权限

```bash
[root@rocky tmp]# ll -d dir
drwxrwxrwx 2 root root 6 Nov 21 19:09 dir

# 不同用户创建文件
[root@rocky tmp]# touch /tmp/dir/root1.txt
[zqh@rocky ~]$ touch /tmp/dir/zqh1.txt
[demo@rocky ~]$ touch /tmp/dir/demo1.txt
[root@rocky tmp]# ll /tmp/dir/
total 0
-rw-rw-r-- 1 demo demo 0 Nov 21 19:11 demo1.txt
-rw-r--r-- 1 root root 0 Nov 21 19:10 root1.txt
-rw-rw-r-- 1 zqh  zqh  0 Nov 21 19:12 zqh1.txt

# 普通用户可以删除文件
[zqh@rocky ~]$ rm -f /tmp/dir/root1.txt 
[zqh@rocky ~]$ rm -f /tmp/dir/zqh1.txt 
[zqh@rocky ~]$ rm -f /tmp/dir/demo1.txt 

# 添加 Sticky Bit(t) 权限
[root@rocky tmp]# chmod o+t dir
[root@rocky tmp]# ll -d /tmp/dir
drwxrwxrwt 2 root root 23 Nov 21 19:16 /tmp/dir

[root@rocky tmp]# touch /tmp/dir/root2.txt
[zqh@rocky ~]$ touch /tmp/dir/zqh2.txt
[demo@rocky ~]$ touch /tmp/dir/demo2.txt

# 普通用户只能删除自己的文件
[zqh@rocky ~]$ rm -f /tmp/dir/zqh2.txt 
[zqh@rocky ~]$ rm -f /tmp/dir/demo2.txt 
rm: cannot remove '/tmp/dir/demo2.txt': Operation not permitted
[zqh@rocky ~]$ rm -f /tmp/dir/root2.txt 
rm: cannot remove '/tmp/dir/root2.txt': Operation not permitted

# root 用户可以删除
[root@rocky tmp]# rm -f /tmp/dir/demo2.txt 
```

### 特殊属性

- `chattr`：修改文件特殊属性
- `lsattr`：列出文件特殊属性

常用属性
- `i`：只能查看、复制，不能修改、删除、重命名或链接文件
- `a`：文件只能进行追加写入操作
```bash
# i 属性
[root@rocky ~]# echo "testfile" > test.txt
[root@rocky ~]# chattr +i test.txt 
[root@rocky ~]# lsattr test.txt 
----i--------------- test.txt

[root@rocky ~]# rm -f test.txt 
rm: cannot remove 'test.txt': Operation not permitted
[root@rocky ~]# echo "new" > test.txt 
bash: test.txt: Operation not permitted
[root@rocky ~]# echo "new" >> test.txt 
bash: test.txt: Operation not permitted
[root@rocky ~]# mv test.txt new.txt 
mv: cannot move 'test.txt' to 'new.txt': Operation not permitted

# a 属性
[root@rocky ~]# chattr -i test.txt 
[root@rocky ~]# chattr +a test.txt 
[root@rocky ~]# lsattr test.txt 
-----a-------------- test.txt
[root@rocky ~]# rm -f test.txt 
rm: cannot remove 'test.txt': Operation not permitted
[root@rocky ~]# echo "new" > test.txt 
bash: test.txt: Operation not permitted
[root@rocky ~]# echo "new" >> test.txt 
[root@rocky ~]# cat test.txt 
testfile
new
```

### 访问控制列表

**访问控制列表**（Access Control List，ACL）是一种用于控制对资源访问权限的机制，它提供了更细粒度的权限控制

`setfacl`：设置文件 ACL

常用选项
- `-m`：修改文件的 ACL 权限
- `-x`：删除文件的 ACL 权限
- `-b`：删除所有 ACL 权限

`getfacl`：查看文件 ACL

```bash
[root@rocky tmp]# ll test.txt 
-rw-r--r-- 1 root root 13 Nov 21 20:19 test.txt

# zqh 用户可以查看文件内容
[zqh@rocky ~]$ cat /tmp/test.txt 
testfile
new

# 设置 zqh 用户对该文件无任何权限
[root@rocky tmp]# setfacl -m u:zqh:- test.txt 
[root@rocky tmp]# getfacl test.txt 
# file: test.txt
# owner: root
# group: root
user::rw-
user:zqh:---
group::r--
mask::r--
other::r--

[root@rocky tmp]# ll test.txt 
-rw-r--r--+ 1 root root 13 Nov 21 20:19 test.txt

# 再次查看文件内容
[zqh@rocky ~]$ cat /tmp/test.txt 
cat: /tmp/test.txt: Permission denied

# 删除 ACL 权限
[root@rocky tmp]# setfacl -x u:zqh test.txt 
```

## 7. vim 编辑器

打开文件（文件不存在则自动创建）
```bash
[root@rocky ~]# vim mage.txt 
```

按 i 进入插入模式，输入内容
```bash
"马哥出品，必属精品"
```

按 esc 回到命令模式，然后按 : 进入末行模式，输入 wq 保存文件并退出

验证文件内容
```bash
[root@rocky ~]$ cat mage.txt 
"马哥出品，必属精品"
```


**命令模式常用操作**

字符间移动
- h：向左移动一个字符
- l：向右移动一个字符
- j：向下移动一个字符
- k：向上移动一个字符

单词间移动
- w：向右移动一个单词
- b：向左移动一个单词

行首行尾移动
- 0：光标移动到行首第一个非空字符
- ^：光标移动到行首
- $：光标移动到行尾

行间移动
- NG：光标移动到第 N 行
- gg：光标移动到文件开头
- G：光标移动到文件末尾

复制
- y^：复制光标到行首第一个非空字符的内容
- y0：复制光标到行首第一个字符的内容
- y$：复制光标到行尾的内容
- yy：复制当前行的内容
- Nyy：从当前行开始，复制 N 行

删除
- dd：删除当前行
- Ndd：从当前行开始，删除 N 行
- dgg：删除光标开始到文件开头的内容
- dG：删除光标开始到文件末尾的内容

粘贴
- p：将复制的内容粘贴到当前光标所在行的下方
- P：将复制的内容粘贴到当前光标所在行的上方

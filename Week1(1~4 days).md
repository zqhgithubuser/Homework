
## 1. 计算机基础



## 2. VMware Workstations 安装 Linux 系统

### 安装前准备

- 确保电脑开启虚拟化功能（默认已开启）
- 下载 ISO 文件
	- Rocky
		- [桌面版](https://download.rockylinux.org/pub/rocky/8/isos/x86_64/Rocky-8.8-x86_64-dvd1.iso)
		- [服务器版](https://download.rockylinux.org/pub/rocky/8/isos/x86_64/Rocky-8.8-x86_64-minimal.iso)
	- Ubuntu 22.04
		- [桌面版](https://releases.ubuntu.com/jammy/ubuntu-22.04.3-desktop-amd64.iso)
		- [服务器版](https://releases.ubuntu.com/jammy/ubuntu-22.04.3-live-server-amd64.iso)

### Rocky 8


### Ubuntu 22.04

#### 创建虚拟机

1. 选择 "文件 -> 新建虚拟机"
![[images/Pasted image 20231114230942.png | 650]]

2. 选择"自定义安装"，点击 "下一步"
![[Pasted image 20231115141942.png | 650]]

3. "硬件兼容性" 默认，点击 "下一步"
![[Pasted image 20231115142255.png | 650]]

4. 选择 "稍后安装操作系统"，点击 "下一步"
![[Pasted image 20231115142427.png | 650]]

5. 客户机操作系统选择 "Linux"，版本选择 "Ubuntu 64 位"，点击 "下一步"
![[Pasted image 20231115142612.png | 650]]

6. 填写 "虚拟机名称"，选择虚拟机目录存放的 "位置"，点击 "下一步"
![[Pasted image 20231115142914.png | 650]]

7. 选择 "处理器数量"，点击 "下一步"
![[Pasted image 20231115143006.png | 650]]


8. "此虚拟机的内存" 填写 2048 MB，点击 "下一步"
![[Pasted image 20231115143317.png | 650]]

9. "网络类型" 选择 "使用网络地址转换(NAT)"，点击 "下一步"

![[Pasted image 20231115143741.png | 650]]

10. "SCSI 控制器" 默认，点击 "下一步"
![[Pasted image 20231115144642.png | 650]]

11. "虚拟磁盘类型" 默认，点击 "下一步"
![[Pasted image 20231115144719.png | 650]]

12. 选择 "创建新虚拟磁盘"，点击 "下一步"
![[Pasted image 20231115144954.png |650]]

12. "最大磁盘大小" 填写 "100GB"，不要勾选 "立即分配所有磁盘空间"，点击 "下一步"
![[Pasted image 20231115145242.png | 650]]

13. "磁盘文件" 名默认，点击 "下一步"
![[Pasted image 20231115145329.png | 650]]

14. 点击 "完成"
![[Pasted image 20231115145403.png | 650]]

#### 安装操作系统

1. 点击 "编辑虚拟机设置"
![[Pasted image 20231115153757.png | 650]]

2. 点击 "CD/DVD"，选择下载的 ISO 文件，点击 "确定"
![[Pasted image 20231115154024.png | 650]]

3. 点击 "启动虚拟机"
![[Pasted image 20231115154148.png | 650]]

4. 选择 "Try or install Ubuntu Server"，回车
![[Pasted image 20231115154254.png | 650]]

5. 语言选择 "English"，回车
![[Pasted image 20231115154449.png |650]]

6. 选择 "Continue without updating"，回车
![[Pasted image 20231115154633.png | 650]]

7. 选择 "Done"，回车
![[Pasted image 20231115154810.png |650]]

8. 选择 "Done"，回车
![[Pasted image 20231115154937.png |650]]
![[Pasted image 20231115155029.png |650]]
![[Pasted image 20231115155133.png|650]]

9. 镜像地址修改为国内的软件下载源（https://mirrors.aliyun.com/ubuntu）
![[Pasted image 20231115155335.png | 650]]

10. 选择 "Done"，回车
![[Pasted image 20231115155539.png | 650]]
![[Pasted image 20231115155643.png |650]]
![[Pasted image 20231115155829.png|650]]

11. 配置主机名、用户和密码
![[Pasted image 20231115160030.png|650]]

12. 选择 "Install OpenSSh server"
![[Pasted image 20231115160205.png|650]]

13. 开始安装
![[Pasted image 20231115160244.png|650]]


14. 安装完成，重启
![[Pasted image 20231115163003.png|650]]

12. 登录
![[Pasted image 20231115163255.png |650]]


## 3. Linux 常用基本命令

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
输出结果的字段
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


## 5. Linux 安全模型


## 6. Linux 权限


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


移动
- 字符间移动
	- h：左
	- l：右
	- j：下
	- k：上
- 单词间移动
	- w：下一个单词的词首
	- b：前一个单词的词首
- 行首行尾跳转
	- ^：跳转至行首
	- $：跳转至行尾
- 行间跳转











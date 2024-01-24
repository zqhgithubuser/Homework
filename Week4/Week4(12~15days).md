
## 1. Shell 编程：计算用户 id 之和

脚本内容如下

```bash
#!/usr/bin/bash

userid_sum=0
while read line; do
    userid=$(echo $line | cut -d: -f3)
    userid_sum=$(( userid_sum + userid ))
done < /etc/passwd

echo "用户id之和: ${userid_sum}"
```

脚本执行结果

```bash
[root@rocky scripts]# ./userid_sum.sh
用户id之和: 90672
```

## 2. 数组，字符串处理，高级变量

### 2.1 索引数组

列出所有索引数组变量

```bash
[root@rocky ~]# declare -a
declare -a BASH_ARGC=()
declare -a BASH_ARGV=()
declare -a BASH_COMPLETION_VERSINFO=([0]="2" [1]="7")
declare -a BASH_LINENO=()
declare -a BASH_SOURCE=()
declare -ar BASH_VERSINFO=([0]="4" [1]="4" [2]="20" [3]="1" [4]="release" [5]="x86_64-redhat-linux-gnu")
declare -a DIRSTACK=()
declare -a FUNCNAME
declare -a GROUPS=()
declare -a PIPESTATUS=([0]="0")
```

索引数组赋值

```bash
# 按索引顺序赋值
[root@rocky ~]# weekdays=("Sunday" "Monday" "Tuesday")

# 赋值指定元素
[root@rocky ~]# weekdays=([0]="Sunday" [1]="Monday" [4]="Thursday")
```

引用数组

```bash
# 元素的值
[root@rocky ~]# echo ${weekdays[@]}
Sunday Monday Thursday

# 对应的索引
[root@rocky ~]# echo ${!weekdays[@]}
0 1 4

# 元素个数
[root@rocky ~]# echo ${#weekdays[@]}
3
```

添加元素

```bash
[root@rocky ~]# weekdays+=([2]="Tuesday" [3]="Wednesday")

[root@rocky ~]# echo ${weekdays[@]}
Sunday Monday Tuesday Wednesday Thursday
```

修改元素

```bash
[root@rocky ~]# weekdays+=("Saturday")
[root@rocky ~]# echo ${weekdays[@]}
Sunday Monday Tuesday Wednesday Thursday Saturday

# 修改元素
[root@rocky ~]# weekdays[5]="Friday"
[root@rocky ~]# echo ${weekdays[@]}
Sunday Monday Tuesday Wednesday Thursday Friday
```

删除数组

```bash
[root@rocky ~]# unset weekdays
```

### 2.2 关联数组

声明关联数组

```bash
[root@rocky ~]# declare -A nums
```

关联数组赋值

```bash
[root@rocky ~]# nums=(["One"]=1 ["Two"]=2 ["Three"]=3)
[root@rocky ~]# echo ${nums[@]}
3 1 2
[root@rocky ~]# echo ${!nums[@]}
Three One Two
```

遍历数组

```bash
# 遍历索引，通过索引获取元素的值
[root@rocky ~]# for i in ${!nums[@]}; do echo "$i ${nums[$i]}"; done
Three 3
One 1
Two 2
```

### 2.3 字符串处理

字符串切片

```bash
[root@rocky ~]# str="abcdefg"

# 从索引为 3 的字符开始截取到字符串末尾
[root@rocky ~]# echo ${str:3}
defg

# 从索引为 3 的字符开始，向右截取 3 个字符
[root@rocky ~]# echo ${str:3:3}
def

# 从字符串末尾开始向左截取 3 个字符
[root@rocky ~]# echo ${str:(-3)}
efg

# 从索引为 3 的字符开始截取到字符串末尾，再从字符串末尾向左偏移 3 个字符
[root@rocky ~]# echo ${str:3:-3}
d

# 从字符串末尾开始向左截取 4 个字符，再从字符串末尾开始向左偏移 4 个字符
[root@rocky ~]# echo ${str:(-4):-3}
d
```

字符串子串

```bash
[root@rocky ~]# str2="ABC123ABCabc"

# 从字符串开头删除 "AB"
[root@rocky ~]# echo ${str2#AB}
C123ABCabc

# 从字符串开头删除第一个匹配的 "BC" 及前面的所有字符
[root@rocky ~]# echo ${str2#*BC}
123ABCabc

# 从字符串开头删除最后一个匹配的 "BC" 及前面的所有字符
[root@rocky ~]# echo ${str2##*BC}
abc

# 从字符串末尾删除第一个匹配 "BC" 及后面的所有字符
[root@rocky ~]# echo ${str2%BC*}
ABC123A

# 从字符串末尾删除最后一个匹配 "BC" 及后面的所有字符
[root@rocky ~]# echo ${str2%%BC*}
A
```

查找替换

```bash
[root@rocky ~]# echo $str2
ABC123ABCabc

[root@rocky ~]# echo ${str2/abc/ABC}
ABC123ABCABC

# 字符串末尾是 "ABC" 时，替换为 "ABC"
[root@rocky ~]# echo ${str2/%abc/ABC}
ABC123ABCABC

[root@rocky ~]# echo ${str2/ABC/abc}
abc123ABCabc

# 将字符串中所有匹配的 "ABC" 替换为 "abc"
[root@rocky ~]# echo ${str2//ABC/abc}
abc123abcabc
```

查找删除

```bash
[root@rocky ~]# echo $str2
ABC123ABCabc

[root@rocky ~]# echo ${str2/ABC}
123ABCabc

[root@rocky ~]# echo ${str2/%abc}
ABC123ABC

# 删除字符串中所有匹配的 "ABC"
[root@rocky ~]# echo ${str2//ABC}
123abc
```

### 2.4 高级变量

高级变量赋值

| 变量未设置                                            | 变量值为空                                         | 变量值非空                              |
| ----------------------------------------------------- | -------------------------------------------------- | --------------------------------------- |
|# unset str3; echo ${str3-"abc"}<br>abc               | # str3=""; echo ${str3-"abc"}<br>                                         | # str3=123; echo ${str3-"abc"}<br/>123  |
|# unset str3; echo ${str3:-"abc"}<br/>abc             | # str3=""; echo ${str3:-"abc"}<br/>abc             | # str3=123; echo ${str3:-"abc"}<br/>123 |
|# unset str3; echo ${str3+"abc"}<br>                  | # str3=""; echo ${str3+"abc"}<br/>abc              | # str3=123; echo ${str3+"abc"}<br/>abc  |
| # unset str3; echo ${str3:+"abc"}<br>                 | # str3=""; echo ${str3:+"abc"}<br>                 | # str3=123; echo ${str3:+"abc"}<br/>abc |
| # unset str3; echo ${str3="abc"}<br/>abc              | # str3=""; echo ${str3="abc"}<br>                  | # str3=123; echo ${str3="abc"}<br/>123  |
| # unset str3; echo ${str3?"abc"}<br/>bash: str3: abc  | # str3=""; echo ${str3?"abc"}<br>                  | # str3=123; echo ${str3?"abc"}<br/>123  |
| # unset str3; echo ${str3:?"abc"}<br/>bash: str3: abc | # str3=""; echo ${str3:?"abc"}<br/>bash: str3: abc | # str3=123; echo ${str3:?"abc"}<br/>123 |

间接引用变量

```bash
[root@rocky ~]# var1="abc"
[root@rocky ~]# var2=var1
[root@rocky ~]# echo $var2
var1
[root@rocky ~]# echo ${!var2}
abc
```

## 3. Shell 编程：10 个随机数的最大值和最小值

脚本内容如下

```bash
#!/usr/bin/bash

DEFAULT_NUMS=10

#######################################
# 输出传入参数的最大值
#######################################
max() {
    local max_random_num=$1
    shift

    for num in "$@"; do
        if (( num > max_random_num )); then
            max_random_num=$num
        fi
    done

    echo ${max_random_num}
}

#######################################
# 输出传入参数的最小值
#######################################
min() {
    local min_random_num=$1
    shift

    for num in "$@"; do
        if (( num < min_random_num )); then
            min_random_num=$num
        fi
    done

    echo ${min_random_num}
}

#######################################
# 输出 n 个随机数的最大值和最小值
# 参数：
#   n: 随机数的个数
#######################################
random() {
    local n=$1
    local randoms=()
    for (( i=0; i<$n; i++ )); do
        randoms+=($RANDOM)
    done
    echo "${randoms[@]}"

    max_num=$(max "${randoms[@]}")
    min_num=$(min "${randoms[@]}")
    echo "Max random number: ${max_num}"
    echo "Min random number: ${min_num}"
}

random ${1:-DEFAULT_NUMS}
```

脚本执行结果

```bash
[root@rocky scripts]# ./random_num.sh
2070 217 21270 1400 6670 17714 22146 2548 12857 27484
Max random number: 27484
Min random number: 217
```

## 4. Shell 编程：递归实现阶乘

脚本内容如下

```bash
#!/usr/bin/bash

#######################################
# 求 n 的阶乘
# 参数：
#   n
#######################################
factorial() {
    local n=$1
    if (( n == 0 || n == 1 )); then
        echo 1
        return
    fi

    echo $(( n * $(factorial $(( n-1 ))) ))
}

res=$(factorial $1)
echo "factorial($1): $res"
```

脚本执行结果

```bash
[root@rocky scripts]# ./factorial.sh 0
factorial(0): 1
[root@rocky scripts]# ./factorial.sh 1
factorial(1): 1
[root@rocky scripts]# ./factorial.sh 5
factorial(5): 120
```

## 5. 进程与线程的区别

| 进程                              | 线程                               |
| --------------------------------- | ---------------------------------- |
| 操作系统中资源分配的基本单位      | 操作系统中调度的基本单位           |
| 每个进程拥有自己独立的内存空间    | 同一进程中的线程共享相同的内存空间 |
| 进程间的切换开销大                | 同一进程中的线程切换开销较小       |
| 通常用于并行，适合 CPU 密集型任务 | 用于并发，适合 I/O 密集型任务      |
## 6. 进程的结构

**进程控制块**（PCB）是操作系统用来存储有关进程的所有信息的数据结构。当一个进程被创建时，操作系统会创建一个对应的进程控制块

进程控制块的信息包括：
- 进程优先级
- 进程结构信息：父进程 ID 和子进程 ID 
- 进程间通信信息：进程间通信相关的标志、信号和消息等
- 进程权限
- 进程状态：创建、就绪、运行、阻塞、终止
- 进程号（PID）
- 程序计数器：要执行的下一条指令的地址
- CPU 寄存器
- CPU 调度信息：CPU 时间片的调度信息
- 内存管理信息：页表、段表、内存限制
- 审计信息：进程的资源使用情况
- I/O 状态信息：进程使用的 I/O 设备列表
- 打开的文件列表

## 7. 结合进程管理命令，说明进程的各种状态

**创建状态**：操作系统正在为进程分配所需的资源

**就绪状态**：进程已经准备好运行，等待操作系统调度程序分配 CPU 资源

**运行状态**：进程被调度执行时，处于运行状态

```bash
zqh@ubuntu:~$ yes > /dev/null &
zqh@ubuntu:~$ ps axo pid,state,cmd | grep yes
   4582 R yes
```

**阻塞状态**：进程在等待某事件发生时，例如等待 I/O 操作完成，进程变为阻塞状态

**终止状态**：进程执行完毕或被提前终止，从运行状态变为终止状态

**睡眠状态**：等待资源可用或某事件发生，分为可中断睡眠（S）状态和不可中断睡眠（D）状态。处于可中断睡眠状态的进程在可以通过信号唤醒

```bash
zqh@ubuntu:~$ sleep 10000 &
[1] 4623

# S 表示进程处于可中断睡眠状态
zqh@ubuntu:~$ ps axo pid,state,cmd | grep sleep
   4623 S sleep 10000
```

**停止状态**：进程被暂停执行，停止的进程暂时不会被调度执行，但仍然在内存中，可以通过发送 SIGCONT 信号恢复执行

```bash
# 暂停进程的执行
zqh@ubuntu:~$ fg
sleep 10000
^Z
[1]+  Stopped                 sleep 10000
zqh@ubuntu:~$ jobs
[1]+  Stopped                 sleep 10000

# T 表示进程处于停止状态
zqh@ubuntu:~$ ps axo pid,state,cmd | grep sleep
   4623 T sleep 10000
```

**僵尸状态**：进程终止后，其父进程不能正常地获取进程的退出状态，该进程将成为僵尸进程，僵尸进程的进程控制块仍然保留在系统中，但不再执行任何操作

```bash
zqh@ubuntu:~$ sleep 10000

zqh@ubuntu:~$ pstree -p
           ├─sshd(915)─┬─sshd(1115)───sshd(1222)───bash(1223)───sleep(4623)

# 暂停 sleep 的父进程的执行
zqh@ubuntu:~$ kill -19 1223

# 终止 sleep 进程
zqh@ubuntu:~$ kill 4623

# Z 表示进程处于僵尸状态
zqh@ubuntu:~$ pstree -p
           ├─sshd(915)─┬─sshd(1115)───sshd(1222)───bash(1223)───sleep(4623)

zqh@ubuntu:~$ ps axo pid,state,cmd | grep sleep
   4623 Z [sleep] <defunct>

# 恢复 sleep 的父进程的执行
zqh@ubuntu:~$ kill -18 1223

# 僵尸进程被父进程回收
zqh@ubuntu:~$ pstree -p
           ├─sshd(915)─┬─sshd(1115)───sshd(1222)───bash(1223)

zqh@ubuntu:~$ ps axo pid,state,cmd | grep sleep
```

## 8. IPC 通信和 RPC 通信的实现方式

**进程间通信**（inter-process communication，IPC）是操作系统为进程提供的管理共享数据的机制

不同的 IPC 方法：

| 方法 | 描述 |  |
| ---- | ---- | ---- |
| 信号 | 从一个进程发送到另一个进程的系统消息 |  |
| 网络套接字 | 同一主机或不同主机上的不同进程通过网络进行通信 |  |
| Unix 域套接字 | 所有通信发生在内核内，不需要通过网络传输 |  |
| 消息队列 | 允许多个进程读取和写入队列 |  |
| 匿名管道 | 进程间的双向通信可以通过两个相反“方向”的管道实现 |  |
| 命名管道 | 被视为管道文件 |  |
| 共享内存 | 多个进程可以访问同一块共享缓冲区 |  |
| 内存映射文件 | 一段虚拟内存，可以通过直接更改内存地址修改文件 |  |

**远程过程调用**（Remote procedure call，RPC）是一种客户端和服务端进程交互的模式（调用者是客户端，执行者是服务端），经典实现是一个通过请求-响应进行消息传递的系统

## 9. Linux 前台作业和后台作业的区别，如何进行切换

**前台作业**：将标准输入和标准输出附加到当前终端的作业。执行过程中会占用终端

**后台作业**：不从终端接收用户输入的作业。执行过程中不会占用终端

前台 -> 后台

```bash
zqh@ubuntu:~$ sleep 1000
^Z
[1]+  Stopped                 sleep 1000

zqh@ubuntu:~$ sleep 2000 &
[2] 4652
```

查看当前终端的所有作业

```bash
zqh@ubuntu:~$ jobs
[1]+  Stopped                 sleep 1000
[2]-  Running                 sleep 2000 &
```

停止 -> 运行

```bash
# 将作业放到后台执行
zqh@ubuntu:~$ bg 1
[1]+ sleep 1000 &

zqh@ubuntu:~$ jobs
[1]-  Running                 sleep 1000 &
[2]+  Running                 sleep 2000 &
```

后台 -> 前台

```bash
# 将作业放到到前台执行，Ctrl+c 终止
zqh@ubuntu:~$ fg 1
sleep 1000
^C
zqh@ubuntu:~$ jobs
[2]+  Running                 sleep 2000 &
```

运行 -> 停止

```bash
zqh@ubuntu:~$ jobs
[2]+  Running                 sleep 2000 &

# Ctrl+z
zqh@ubuntu:~$ fg 2
sleep 2000
^Z
[2]+  Stopped                 sleep 2000
zqh@ubuntu:~$ jobs
[2]+  Stopped                 sleep 2000
```

## 10. 内核设计流派及特点

大内核（Linux，Unix）
- 内核中包含操作系统的主要功能模块（包括进程管理、存储管理、设备管理等)
- 优点：性能较高
- 缺点：内核代码庞大，结构复杂，难以维护

微内核（Windows）
- 内核中保留最基本的功能（例如中断处理）
- 优点：内核功能少，结构清晰，方便维护
- 缺点：需要在用户态和内核态之间频繁切换，性能相对较低

## 11. Rocky Linux 的启动流程

1. BIOS 上电自检（POST），检查系统硬件
2. BIOS 选择一个引导设备（通常是第一块磁盘）
3. 引导加载程序（BootLoader）：GRUB2
	- stage 1：BIOS 加载并执行 MBR 中的前 446 字节
	- stage 1.5：通过 stage1 加载并执行，负责找到 stage2
	- stage 2：`/boot/grub2` 中的文件，通过 stage1.5 加载并执行，负责解析配置文件、显示引导菜单、加载内核
4. 用户选择操作系统后，GRUB2 将内核镜像和 initramfs 镜像文件加载到内存，initramfs 作为临时的根文件系统
5. 内核初始化，配置内存，加载所需的驱动，连接各种硬件设备
6. 内核执行 initramfs 中的 systemd 程序，作为 PID=1 的进程
7. systemd 实例执行 initrd.target，挂载并切换到真正的根文件系统
8. 执行根文件系统中的 systemd 程序替换原来的  systemd 进程（PID 仍为 1），进入用户空间
9. 执行 default.target（指向 multi-user.target 或 graphical.target）
10. 用户可以登录

## 12. 编写 chkconfig 服务脚本，实现服务的启动、停止、重启

创建 `/etc/init.d/test` 文件

```bash
#!/bin/bash

# chkconfig: 2345 96 3
# description: This is test service script

# source function library
. /etc/rc.d/init.d/functions

prog=testsvc
lockfile=/var/lock/subsys/testsvc

start() {
    [ -e $lockfile ] && exit 1 || touch $lockfile
    action "Starting testsvc"
    sleep 2
}

stop() {
    [ -e $lockfile ] && rm -f $lockfile || exit 1
    action "Stopping testsvc"
}

status() {
    [ -e $lockfile ] && echo "testsvc is running..." || echo "testsvc is stopped"
}

restart() {
    stop
    start
}

case "$1" in
    start)
        start
        ;;
    stop)
        stop
        ;;
    restart)
        restart
        ;;
    status)
        status
        ;;
    *)
        echo $"Usage: $0 {start|stop|restart|status}"
        exit 2
esac
```

启动服务

```bash
[root@centos6 ~]# service test start
Starting test                                              [  OK  ]
```

停止服务

```bash
[root@centos6 ~]# service test stop
Stopping test                                              [  OK  ]
```

## 13. systemd 服务配置文件

| 目录                      | 描述                                                         |
| ------------------------- | ------------------------------------------------------------ |
| `/usr/lib/systemd/system` | RPM 软件包中包含的 `systemd` 单元文件                        |
| `/run/systemd/system`     | 系统运行过程中创建的 `systemd` 单元文件。该目录优先于 `/usr/lib/systemd/system` |
| `/etc/systemd/system`     | 使用 `systemctl enable` 命令创建的 `systemd` 单元文件，以及用于扩展服务的单元文件。该目录优先于 `/run/systemd/system` |
## 14. awk 工作原理及使用

### 14.1 awk 工作原理

1. 处理输入前执行 `BEGIN { action... }` 块中的语句
2. 从标准输入或文件中一次读取一行，匹配的行执行 `pattern { action... }` 块中的语句
3. 处理输入后执行 `END { action... }` 块中的语句

格式

```bash
awk 'program' file
```

### 14.2 print

默认情况下，字段以空白字符（空格、制表符和换行符）分隔，字段编号从 `$1` 到 `$n`

```bash
[root@rocky ~]# df -h | awk '{ print $1, $5 }'
Filesystem Use%
devtmpfs 0%
tmpfs 0%
tmpfs 2%
tmpfs 0%
/dev/mapper/rl-root 11%
/dev/mapper/rl-home 1%
/dev/sda1 27%
tmpfs 0%
```

`-F`：指定字段分隔符

```bash
[root@rocky ~]# awk -F: '{ print $1, $6 }' /etc/passwd
root /root
bin /bin
daemon /sbin
adm /var/adm
lp /var/spool/lpd
sync /sbin
shutdown /sbin
halt /sbin
mail /var/spool/mail
......
```

### 14.2  printf

格式化输出

`-`：左对齐（默认为右对齐）

```bash
[root@rocky ~]# awk -F: '{ printf "%-20s %-30s\n", $1, $6 }' /etc/passwd
root                 /root
bin                  /bin
daemon               /sbin
adm                  /var/adm
lp                   /var/spool/lpd
sync                 /sbin
shutdown             /sbin
......
```

### 14.3 变量

**内置变量**

`FS`：输入内容字段分隔符，`-F` 选项优先级更高

```bash
[root@rocky ~]# awk -v FS=':' '{ print $1, $6 }' /etc/passwd
root /root
bin /bin
daemon /sbin
adm /var/adm
lp /var/spool/lpd
sync /sbin
shutdown /sbin
......
```

`OFS`：输出内容字段分隔符

```bash
[root@rocky ~]# awk -v FS=':' -v OFS='+---+' '{ print $1, $6 }' /etc/passwd
root+---+/root
bin+---+/bin
daemon+---+/sbin
adm+---+/var/adm
lp+---+/var/spool/lpd
sync+---+/sbin
shutdown+---+/sbin
......
```

`RS`：输入内容记录分隔符（默认为换行符）

```bash
[root@rocky ~]# awk -v RS=':' '{ print }' /etc/passwd
root
x
0
0
root
/root
/usr/bin/zsh
bin
x
1
1
bin
/bin
/sbin/nologin
......
```

`ORS`：输出内容记录分隔符

```bash
[root@rocky ~]# awk -v FS=':' -v ORS='\n\n' '{ print $1, $6 }' /etc/passwd
root /root

bin /bin

daemon /sbin

adm /var/adm

lp /var/spool/lpd

sync /sbin

shutdown /sbin
......
```

`NF`：字段数量

```bash
[root@rocky ~]# awk -F: '{ print NF; exit }' /etc/passwd
7

# 倒数第 2 个字段
[root@rocky ~]# awk -F: '{ print $(NF-1) }' /etc/passwd
/root
/bin
/sbin
/var/adm
/var/spool/lpd
/sbin
```

`NR`：记录编号

```bash
[root@rocky ~]# awk -F: '{ print NR, $1, $6 }' /etc/passwd
1 root /root
2 bin /bin
3 daemon /sbin
4 adm /var/adm
5 lp /var/spool/lpd
6 sync /sbin
7 shutdown /sbin
```

**自定义变量**

```bash
[root@rocky ~]# awk -v name='gawk' 'BEGIN { print name }'
gawk

# 在 BEGIN 块中定义变量
[root@rocky ~]# awk 'BEGIN { name="gawk"; print name }'
gawk
```

### 14.4 模式

正则表达式

```bash
[root@rocky ~]# df -h | awk '/^\/dev/'
/dev/mapper/rl-root   70G  7.5G   63G  11% /
/dev/mapper/rl-home  127G  951M  126G   1% /home
/dev/sda1           1014M  265M  750M  27% /boot
```

关系表达式（结果为真才会执行块中的语句）

```bash
[root@rocky ~]# awk -F: '$NF=="/bin/bash" { print $1, $NF }' /etc/passwd
zqh /bin/bash
nginx /bin/bash
varnish /bin/bash
demo /bin/bash
```

行范围

```bash
[root@rocky ~]# awk 'NR>=3 && NR<7 { print NR, $0 }' /etc/passwd
3 daemon:x:2:2:daemon:/sbin:/sbin/nologin
4 adm:x:3:4:adm:/var/adm:/sbin/nologin
5 lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
6 sync:x:5:0:sync:/sbin:/bin/sync
```

### 14.5 条件判断

判断用户的 shell 是否以 sh 结尾

```bash
[root@rocky ~]# awk -F: '{ if($NF~"sh$") { printf "%-20s login\n", $1 } else { printf "%-20s nologin\n", $1 } }' /etc/passwd
root                 login
bin                  nologin
daemon               nologin
adm                  nologin
lp                   nologin
sync                 nologin
shutdown             nologin
......
```

### 14.6 while 循环

```bash
[root@rocky ~]# awk 'BEGIN { sum=0; i=1; while(i<=100) { sum+=i; i++ }; print sum }'
5050
```

### 14.7 for 循环

```bash
[root@rocky ~]# awk 'BEGIN { sum=0; for(i=0; i<=100; i++) { sum+=i }; print sum }'
5050
```

### 14.8 continue 和 break

```bash
[root@rocky ~]# awk 'BEGIN { sum=0; for(i=0; i<=100; i++) { if(i==50) { continue } sum+=i }; print sum }'
5000

[root@rocky ~]# awk 'BEGIN { sum=0; for(i=0; i<=100; i++) { if(i==100) { break } sum+=i }; print sum }'
4950
```

## 15. awk 数组、函数

### 15.1 数组

遍历数组元素

```bash
[root@rocky ~]# awk 'BEGIN { weekdays[0]="Sunday"; weekdays[1]="Monday"; weekdays[2]="Tuesday"; for(i in weekdays) { print i, weekdays[i
] } }'
0 Sunday
1 Monday
2 Tuesday
```

统计 IP 地址出现次数

```bash
[root@rocky ~]# awk '{ips[$1]++} END { for(i in ips) { printf "%-20s %-10s\n", i, ips[i]} }' access_log
172.20.0.200         1482
172.20.21.121        2
172.20.30.91         29
172.16.102.29        864
172.20.0.76          1565
172.20.9.9           15
......
```

### 15.2 函数

生成随机数

>rand()：返回一个 [0, 1) 之间的随机浮点数
>
>srand()：设置随机数生成器的种子
>
>int()：向下取整

```bash
[root@rocky ~]# awk 'BEGIN { srand(); for(i=1; i<=10; i++) { printf "%d ", int(rand()*10) } }'
7 8 2 6 7 1 1 4 8 3 #
```

>length([s])：返回字符串 s 的长度
>
>gsub(r, s [, t])：替换所有匹配的子串
>
>sub(r, s [, t])：替换第一个匹配的子串
>
>split(s, a [, r [, seps]])：指定分隔符 seps 分割字符串 s，并将分割后的子串存储在数组 a 中

```bash
[root@rocky ~]# awk 'BEGIN { print length("hello, world!") }' /etc/passwd
13

[root@rocky ~]# awk 'sub(":", " ")' /etc/passwd | head -n 1
root x:0:0:root:/root:/bin/bash

[root@rocky ~]# awk 'gsub(":", " ")' /etc/passwd | head -n 1
root x 0 0 root /root /bin/bash

[root@rocky ~]# ss -nt | awk '/ESTAB/{ split($4, addr, ":"); print addr[1]"\t"addr[2] }'
10.0.0.150      22
```

>systime()：返回时间戳
>
>strftime(format [, timestamp])：格式化时间戳

```bash
[root@rocky ~]# awk 'BEGIN { print systime() }'
1705932228

[root@rocky ~]# awk 'BEGIN { print strftime("%Y-%m-%d %H:%M:%S", systime()) }'
2024-01-22 22:04:36
```

## 16. CA 管理相关工具

### 16.1 生成消息摘要

```bash
[root@rocky ~]# openssl dgst -sha256 /etc/passwd
SHA256(/etc/passwd)= f71d895ba92c67f0345ca5c87cf5900f3a961114a910a91ad87d4acda2d27573
```

### 16.2 生成 RSA 密钥

生成私钥（使用 AES 算法加密）

```bash
[root@rocky ~]# openssl genrsa -aes256 -out private.pem
Generating RSA private key, 2048 bit long modulus (2 primes)
..............................+++++
.............+++++
e is 65537 (0x010001)
Enter pass phrase for private.pem:
Verifying - Enter pass phrase for private.pem:

[root@rocky ~]# cat private.pem
-----BEGIN RSA PRIVATE KEY-----
Proc-Type: 4,ENCRYPTED
DEK-Info: AES-256-CBC,AC1174824F536D8A2B5D0AC7DE5D4874

0Y6dV17ISiWatU+/4NzV5eAw/2u6g/w8buaml7kcKoJFutKgdEvYuTvx9Ee1UANx
zFTk0hmg6G+vl+KxMg0SF5EsEy1Gd38Ibe2CaGMPpuZhbKpi9SR59gm33/aLiSlP
Zfd1f9uXgYOsXVsJZomgjo6w0w4qZ1uW25a7Wk+w4H20fXwFqp1x4CkzbQkFNu8p
IlWZD2AvfHPo0zTOciGSk4viFjFWnSwsm2Bv1T/kGBMS8Xlf+YrIDK1p0VARQ5rg
elClHCupfVZnoE5InTbVq+/uHrfHLE6oiIIQrH7739Wp/Vm5UVVlKizuoG1tw5QK
......
-----END RSA PRIVATE KEY-----
```

从私钥中提取公钥

```bash
[root@rocky ~]# openssl rsa -in private.pem -outform PEM -pubout -out public.pem
Enter pass phrase for private.pem:
writing RSA key

[root@rocky ~]# cat public.pem
-----BEGIN PUBLIC KEY-----
MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAtguW7BZav43VURZ2q3Iu
m1E7PtEMmXzYvv0ixR1NIwJrhCudtX85oE03vTLp3g+2NUp2tuRD3cy7mvhdfFPU
xuANhW3LJY/FW3gdCTxH7cWfRi1Mxk85evsllCb3pZ60jQExm/oUjqRJbyvhtcac
JLNtXjsWVE7A2nxe7YeRDWUfrLaYQcuq41SY0Yrd9ukBvQ/4ctZKE4eJyWFg0GbW
5Aivv2kcLIQCR85M0I8gBAfol9N5Ci0TX4QIiuhN1AgGMmKMaq4WqUTtI9fyfstp
L80/sY8nnft+WxKkAmKvN0dBk8ijHeok2ISbuH6zNjzK+7m6ca7t3DzZDacNLgMo
NwIDAQAB
-----END PUBLIC KEY-----
```

## 17. 对称加密与非对称加密算法，Openssl 签发证书步骤

### 17.1 对称加密算法

![](https://github.com/zqhgithubuser/Homework/blob/main/Week4/images/Pasted_image_20240123151729.png)

对称加密算法：发送方和接收方使用相同的密钥加密和解密数据。流行的对称加密算法是 AES，包含 3 种密钥长度：AES-128、AES-192 和 AES-256

### 17.2 非对称加密算法

**非对称加密**

![](https://github.com/zqhgithubuser/Homework/blob/main/Week4/images/Pasted_image_20240123141229.png)

非对称加密算法（公钥加密算法）：每个用户都有一对加密密钥——私钥和公钥。私钥是保密的，公钥可以公开。发送方使用密钥所有者的公钥加密数据，将加密后的数据发送给密钥所有者，密钥所有者使用私钥解密数据

**结合使用**

![](https://github.com/zqhgithubuser/Homework/blob/main/Week4/images/Pasted_image_20240124145757.png)

发送方使用对称密钥加密数据，使用接收方的公钥加密对称密钥，将加密数据和加密的对称密钥发送给接收方。接收方使用自己的私钥解密得到对称密钥，再使用对称密钥解密数据。这种方式的好处是可以提高解密速度


**数字签名**

![](https://github.com/zqhgithubuser/Homework/blob/main/Week4/images/Pasted_image_20240123150415.png)

非对称密钥还可以用于数字签名，验证数据的发送方以及数据的完整性。密钥所有者使用哈希函数对数据生成消息摘要，使用私钥加密消息摘要，然后将数据和加密的消息摘要发送给接收方。接收方使用密钥所有者的公钥解密加密的消息摘要，得到消息摘要，表明加密的消息摘要是由密钥所有者发送的。再使用相同的哈希函数对数据生成一个新的消息摘要，将两个消息摘要进行比较，如果完全一致，则可以验证数据的完整性

### 17.3 签发证书

创建所需的目录和文件

```bash
[root@rocky ~]# mkdir -p /etc/pki/CA/{certs,crl,newcerts,private}

[root@rocky ~]# tree /etc/pki/CA
/etc/pki/CA
├── certs
├── crl
├── newcerts
└── private

# 签发和吊销的证书条目信息
[root@rocky ~]# touch /etc/pki/CA/index.txt
# 签署下一个证书的序列号
[root@rocky ~]# echo 0F > /etc/pki/CA/serial
```

生成 CA 私钥

```bash
[root@rocky ~]# openssl genrsa -aes256 -out /etc/pki/CA/private/cakey.pem
Generating RSA private key, 2048 bit long modulus (2 primes)
................+++++
..............................................................+++++
e is 65537 (0x010001)
Enter pass phrase for /etc/pki/CA/private/cakey.pem:
Verifying - Enter pass phrase for /etc/pki/CA/private/cakey.pem:
```

生成 CA 自签名证书

```bash
[root@rocky ~]# openssl req -new -x509 -days 3650 -key /etc/pki/CA/private/cakey.pem -out /etc/pki/CA/cacert.pem
Enter pass phrase for /etc/pki/CA/private/cakey.pem:
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [XX]:CN
State or Province Name (full name) []:Beijing
Locality Name (eg, city) [Default City]:Beijing
Organization Name (eg, company) [Default Company Ltd]:example
Organizational Unit Name (eg, section) []:
Common Name (eg, your name or your server's hostname) []:www.example.com
Email Address []:
```

查看证书的详细信息

```bash
[root@rocky ~]# openssl x509 -in /etc/pki/CA/cacert.pem -noout -text
```

用户生成私钥

```bash
zqh@ubuntu:~$ openssl genrsa -aes256 -out test.key
Enter PEM pass phrase:
Verifying - Enter PEM pass phrase:
```

用户生成证书签署请求（CSR）

```bash
zqh@ubuntu:~$ openssl req -new -key test.key -out test.csr
Enter pass phrase for test.key:
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:CN
State or Province Name (full name) [Some-State]:Beijing
Locality Name (eg, city) []:Beijing
Organization Name (eg, company) [Internet Widgits Pty Ltd]:example
Organizational Unit Name (eg, section) []:
Common Name (e.g. server FQDN or YOUR name) []:www.test.com
Email Address []:

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:
```

CA 签发证书

```bash
[root@rocky ~]# openssl ca -in test.csr -out /etc/pki/CA/certs/test.crt -days 365
Using configuration from /etc/pki/tls/openssl.cnf
Enter pass phrase for /etc/pki/CA/private/cakey.pem:
Check that the request matches the signature
Signature ok
Certificate Details:
        Serial Number: 15 (0xf)
        Validity
            Not Before: Jan 23 11:16:31 2024 GMT
            Not After : Jan 22 11:16:31 2025 GMT
        Subject:
            countryName               = CN
            stateOrProvinceName       = Beijing
            organizationName          = example
            commonName                = www.test.com
        X509v3 extensions:
            X509v3 Basic Constraints:
                CA:FALSE
            Netscape Comment:
                OpenSSL Generated Certificate
            X509v3 Subject Key Identifier:
                52:4D:8D:5F:15:C9:78:D1:2C:00:65:CE:11:F0:0B:58:E5:8A:20:17
            X509v3 Authority Key Identifier:
                keyid:FA:C3:D2:2F:9A:D8:1F:CB:D5:34:DE:C6:D6:A0:12:39:32:03:D1:B5

Certificate is to be certified until Jan 22 11:16:31 2025 GMT (365 days)
Sign the certificate? [y/n]:y


1 out of 1 certificate requests certified, commit? [y/n]y
Write out database with 1 new entries
Data Base Updated
```

### 17.4 吊销证书

吊销证书

```bash
# 显示证书的状态
[root@rocky ~]# openssl ca -status 0F
Using configuration from /etc/pki/tls/openssl.cnf
0F=Valid (V)

[root@rocky ~]# openssl ca -revoke /etc/pki/CA/newcerts/0F.pem
Using configuration from /etc/pki/tls/openssl.cnf
Enter pass phrase for /etc/pki/CA/private/cakey.pem:
Revoking Certificate 0F.
Data Base Updated 

[root@rocky ~]# openssl ca -status 0F
Using configuration from /etc/pki/tls/openssl.cnf
0F=Revoked (R)
```

生成证书吊销列表

```bash
# 第一次吊销证书时，写入证书吊销列表的初始序列号
[root@rocky ~]# echo 01 > /etc/pki/CA/crlnumber

生成证书吊销列表文件
[root@rocky ~]# openssl ca -gencrl -out /etc/pki/CA/crl.pem
Using configuration from /etc/pki/tls/openssl.cnf
Enter pass phrase for /etc/pki/CA/private/cakey.pem:

# 查看证书吊销列表的详细信息
[root@rocky ~]# openssl crl -in /etc/pki/CA/crl.pem -noout -text
```


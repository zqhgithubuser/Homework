
## 1. 文本处理

### 显示文本内容

**`cat`**：将文件内容连接到标准输出（在终端上显示文件内容）

常用选项
- `-A`：显示所有控制字符（例如制表符）和行尾符（$）
- `-b`：显示非空行的行号
- `-n`：显示每行的行号

```bash
[root@rocky ~]# cat /tmp/file1.txt
a b
c
d       e       f

[root@rocky ~]# cat -A /tmp/file1.txt
a b$
c$
d^Ie^If$
```

**`tac`**：从最后一行开始逐行显示文件的内容

```bash
[root@rocky ~]# cat /tmp/file1.txt
1
2
3
4
5

[root@rocky ~]# tac /tmp/file1.txt
5
4
3
2
1
```

**`rev`**：逆序输出每一行的字符

```bash
[root@rocky ~]# cat /tmp/file1.txt
1 2 3 4 5
a b c d e

[root@rocky ~]# rev /tmp/file1.txt
5 4 3 2 1
e d c b a
```

**`head`**：默认打印文件的前 10 行

常用选项
- `-n [-]NUM`：打印文件的前 n 行

```bash
[root@rocky ~]# seq 10 | head -n 3
1
2
3

# -NUM（打印除了最后 n 行的所有行）
[root@rocky ~]# seq 10 | head -n -3
1
2
3
4
5
6
7
```

**`tail`**：默认打印文件的最后 10 行

常用选项
- `-n [+]NUM`：打印文件的最后 n 行
- `-f`：实时显示文件的末尾内容

```bash
[root@rocky ~]# seq 10 | tail -n 3
8
9
10

# +NUM（从第 n 行开始打印）
[root@rocky ~]# seq 10 | tail -n +3
3
4
5
6
7
8
9
10
```

```bash
[root@rocky ~]# tail -f /var/log/messages
Dec  5 22:43:44 rocky systemd[2242]: Starting D-Bus User Message Bus Socket.
Dec  5 22:43:44 rocky systemd[2242]: Listening on Multimedia System.
Dec  5 22:43:44 rocky systemd[2242]: Reached target Timers.
Dec  5 22:43:44 rocky systemd[2242]: Listening on D-Bus User Message Bus Socket.
Dec  5 22:43:44 rocky systemd[2242]: Reached target Sockets.
Dec  5 22:43:44 rocky systemd[2242]: Reached target Basic System.
Dec  5 22:43:44 rocky systemd[2242]: Reached target Default.
Dec  5 22:43:44 rocky systemd[2242]: Startup finished in 132ms.
Dec  5 22:43:44 rocky systemd[1]: Started User Manager for UID 0.
Dec  5 22:43:44 rocky systemd[1]: Started Session 2 of user root.
^C
[root@rocky ~]#
```

**`more`**：分页查看文件内容，仅支持向前翻页

**`less`**：分页查看文件内容，支持向前和向后翻页，支持搜索、跳转、执行各种命令

```bash
[root@rocky ~]# less /etc/profile
# /etc/profile

# System wide environment and startup programs, for login setup
# Functions and aliases go in /etc/bashrc

# It's NOT a good idea to change this file unless you know what you
# are doing. It's much better to create a custom.sh shell script in
# /etc/profile.d/ to make custom changes to your environment, as this
# will prevent the need for merging in future updates.

pathmunge () {
    case ":${PATH}:" in
        *:"$1":*)
            ;;
        *)
            if [ "$2" = "after" ] ; then
                PATH=$PATH:$1
            else
                PATH=$1:$PATH
            fi
    esac
}
......
/etc/profile
```

### 提取文本内容

**`cut`**：提取文本的特定部分

常用选项
- `-c`：选取指定的字符、字符集或字符范围
- `-d`：指定字段分隔符，默认为制表符
- `-f`：选取指定的字段、字段集或字段范围
- `--output-delimiter=STRING`：指定输出时的字段分隔符

```bash
[root@rocky ~]# cut -d ':' -f 1,6 /etc/passwd
root:/root
bin:/bin
daemon:/sbin
adm:/var/adm
lp:/var/spool/lpd
sync:/sbin
......
```

### 合并文本内容

**`paste`**：按列合并文本内容

常用选项
- `-d`：指定分隔符
- `-s`：一次读取一个文件，将每个文件的所有列合并成一行

```bash
[root@rocky tmp]# cat letter.txt
a
b
c
d
e
[root@rocky tmp]# cat num.txt
1
2
3
4
5

[root@rocky tmp]# paste letter.txt num.txt
a       1
b       2
c       3
d       4
e       5
[root@rocky tmp]# paste -d ':' letter.txt num.txt
a:1
b:2
c:3
d:4
e:5

[root@rocky tmp]# paste -s letter.txt
a       b       c       d       e
[root@rocky tmp]# paste -s letter.txt num.txt
a       b       c       d       e
1       2       3       4       5

# - - - 表示从标准输入中读取数据，并将它们合并成 3 列
[root@rocky ~]# seq 9 | paste - - -
1       2       3
4       5       6
7       8       9
```

### 分析文本内容

**`wc`**：统计文本的行数、单词数和字符数

常用选项
- `-c`：统计字符数
- `-l`：统计行数
- `-w`：统计单词数

```bash
[root@rocky ~]# wc -l /etc/passwd
51 /etc/passwd

[root@rocky ~]# wc -l < /etc/passwd
51
```

**`sort`**：排序

常用选项
- `-r`：逆序排序
- `-n`：按数值顺序进行排序
- `-k`：按某个字段进行排序
- `-t`：指定字段分隔符

```bash
[root@rocky ~]# df -h | tail -n +2 | tr -s ' ' | sort -t ' ' -k 5 -nr
/dev/sr0 12G 12G 0 100% /run/media/root/Rocky-8-8-x86_64-dvd
/dev/sda1 1014M 265M 750M 27% /boot
/dev/mapper/rl-root 70G 18G 53G 26% /
tmpfs 874M 9.5M 865M 2% /run
tmpfs 175M 32K 175M 1% /run/user/0
/dev/mapper/rl-home 127G 951M 126G 1% /home
tmpfs 874M 0 874M 0% /sys/fs/cgroup
tmpfs 874M 0 874M 0% /dev/shm
```

**`uniq`**：合并重复的行

常用选项
- `-c`：显示每行重复的次数
- `-d`：仅显示重复的行
- `-u`：仅显示不重复的行

```bash
[root@rocky tmp]# cat file1.txt
a
b
1
c
[root@rocky tmp]# cat file2.txt
b
e
f
c
1
2

[root@rocky tmp]# cat file1.txt file2.txt | sort -n | uniq -c
      1 a
      2 b
      2 c
      1 e
      1 f
      2 1
      1 2
      
[root@rocky tmp]# cat file1.txt file2.txt | sort -n | uniq -cd
      2 b
      2 c
      2 1
[root@rocky tmp]# cat file1.txt file2.txt | sort -n | uniq -cu
      1 a
      1 e
      1 f
      1 2
```

**`diff`**：逐行比较两个文件

```bash
[root@rocky yum.repos.d]# diff Rocky-BaseOS.repo Rocky-BaseOS.repo.bak
13,14c13,14
< #mirrorlist=https://mirrors.rockylinux.org/mirrorlist?arch=$basearch&repo=BaseOS-$releasever
< baseurl=https://mirrors.aliyun.com/rockylinux/$releasever/BaseOS/$basearch/os/
---
> mirrorlist=https://mirrors.rockylinux.org/mirrorlist?arch=$basearch&repo=BaseOS-$releasever
> #baseurl=http://dl.rockylinux.org/$contentdir/$releasever/BaseOS/$basearch/os/
```

### 格式化打印文本内容

**`printf`**：格式化并打印数据

常用格式说明符
- `%d`：十进制数
- `%f`：浮点数
- `%s`：字符串
- `-o`：八进制数
- `%x|X`：十六进制数

常用转义字符
- `\"`：打印双引号（"）
- `\\`：打印反斜杠（\\）
- `\n`：打印换行符
- `\t`：打印制表符

```bash
[root@rocky ~]# printf "%s\n" 1 2 3 4
1
2
3
4
[root@rocky ~]# printf "%s %s\n" 1 2 3 4
1 2
3 4
[root@rocky ~]# printf "%s %s %s\n" 1 2 3 4
1 2 3
4

[root@rocky ~]# printf "%.2f\n" 12
12.00

[root@rocky ~]# printf "%x\n" 10
a
```

### 文本处理三剑客 grep 

**`grep`**：Global Regular Expression Print 的缩写，用于搜索并打印与正则表达式中的模式匹配的行

常用选项
- `-A`：显示匹配的行及其后面的 n 行
- `-B`：显示匹配的行及其前面的 n 行
- `-C`：显示匹配的行及其前面和后面的 n 行
- `-c`：统计匹配的行数
- `-e`：允许指定多个模式，它们之间是或关系
- `-E`：使用扩展正则表达式
- `-f`：从指定文件获取模式
- `-i`：忽略大小写
- `-n`：显示匹配行的行号
- `-o`：仅显示匹配的字符串，而不是整行
- `-r`：递归搜索目录下的文件
- `-R`：递归搜索目录下的文件，处理所有软链接
- `-v`：显示不匹配的行

```bash
# 主机 CPU 数量
[root@rocky ~]# grep -c processor /proc/cpuinfo
2

# 提取主机 IP 地址
[root@rocky ~]# ip addr show ens33 | grep -Eo '([0-9]{1,3}\.){3}[0-9]{1,3}' | head -n 1
10.0.0.150

# 过滤掉文件的注释行和空行
[root@rocky ~]# grep -Ev '^#|^$' /etc/fstab
/dev/mapper/rl-root     /                       xfs     defaults        0 0
UUID=e700351e-7648-404e-9555-b5e4ce0b6dd8 /boot                   xfs     defaults        0 0
/dev/mapper/rl-home     /home                   xfs     defaults        0 0
```

### 文本处理三剑客 sed

#### 工作原理

sed 是 stream editor 的缩写，是一种非交互式文本编辑器。它可以查找、替换、添加、删除和修改文本中的行。sed 的工作原理是按顺序一次将文件的一行读取到内存中。执行指定的编辑指令，将处理后的结果发送到标准输出或写回到文件中

#### 基本用法

常用选项
- `-n`：不自动打印模式空间的内容
- `-r`：使用扩展正则表达式
- `-i.bak`：备份并修改文件

常用命令
- `p`：打印模式空间的内容
- `d`：删除模式空间的内容
- `a`：在行后追加文本
- `i`：在行前插入文本
- `c`：替换选取的行
- `s/regexp/replacement/`：查找替换
- `g`：全局替换
- `p`：显示替换成功的行

打印指定的行

```bash
[root@rocky ~]# sed -n '1p' /etc/passwd
root:x:0:0:root:/root:/usr/bin/zsh

[root@rocky ~]# sed -n '$p' /etc/passwd
demo:x:1005:1006::/home/demo:/bin/bash

# 3 到 6 行
[root@rocky ~]# seq 10 | sed -n '3,6p'
3
4
5
6

# 奇数行
[root@rocky ~]# seq 10 | sed -n '1~2p'
1
3
5
7
9

# 偶数行
[root@rocky ~]# seq 10 | sed -n '2~2p'
2
4
6
8
10

# 打印不以 # 开头和非空的行
[root@rocky ~]# sed -rn '/^(#|$)/!p' /etc/fstab
/dev/mapper/rl-root     /                       xfs     defaults        0 0
UUID=e700351e-7648-404e-9555-b5e4ce0b6dd8 /boot                   xfs     defaults        0 0
/dev/mapper/rl-home     /home                   xfs     defaults        0 0
```

删除指定的行

```bash
[root@rocky tmp]# seq 10 > seq.txt
[root@rocky tmp]# sed '2,9d' seq.txt
1
10
[root@rocky tmp]# cat seq.txt
1
2
3
4
5
6
7
8
9
10

[root@rocky tmp]# sed -i.bak '2,9d' seq.txt
[root@rocky tmp]# ll seq.txt*
-rw-r--r-- 1 root root  5 Dec  7 15:39 seq.txt
-rw-r--r-- 1 root root 21 Dec  7 15:38 seq.txt.bak
[root@rocky tmp]# cat seq.txt.bak
1
2
3
4
5
6
7
8
9
10
[root@rocky tmp]# cat seq.txt
1
10

# 删除 # 开头的行和空行
[root@rocky tmp]# sed -rn -i.bak '/^(#|$)/d' /etc/fstab
```

查找和替换指定的行

```bash
# 批量注释
[root@rocky ~]# sed -rn 's/^[^#]/# &/p' /etc/fstab
# /dev/mapper/rl-root     /                       xfs     defaults        0 0
# UUID=e700351e-7648-404e-9555-b5e4ce0b6dd8 /boot                   xfs     defaults        0 0
# /dev/mapper/rl-home     /home                   xfs     defaults        0 0

# 禁用 SELinux
[root@rocky ~]# cat /etc/selinux/config

# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
#     enforcing - SELinux security policy is enforced.
#     permissive - SELinux prints warnings instead of enforcing.
#     disabled - No SELinux policy is loaded.
SELINUX=enforcing
# SELINUXTYPE= can take one of these three values:
#     targeted - Targeted processes are protected,
#     minimum - Modification of targeted policy. Only selected processes are protected.
#     mls - Multi Level Security protection.
SELINUXTYPE=targeted
```

>[!note]
>cat、tac、rev、head、tail、more、less、cut、paste、wc、sort、uniq、grep、sed 命令均支持标准输入，可以通过管道接收命令的输出结果

## 2. 文件查找

**`locate`**：在文件索引数据库中搜索匹配的文件名，速度非常快，但不提供实时更新

常用选项
- `-i`：不区分大小写
- `-r`：使用基本正则表达式

```bash
# 手动更新文件索引数据库
[root@rocky ~]# updatedb
[root@rocky ~]# ll /var/lib/mlocate/mlocate.db
-rw-r----- 1 root slocate 4034529 Dec  7 17:34 /var/lib/mlocate/mlocate.db

# 搜索以 .conf 结尾的文件
[root@rocky ~]# locate -r '.*\.conf$'
```


**`find`**：在文件系统中实时搜索文件

常用选项
- `-empty`：查找空文件或目录
- `-maxdepth levels`：按目录深度查找
- `-name`：按文件名查找
- `-atime`：按访问时间查找
- `-ctime`：按状态改变时间查找
- `-mtime`：按修改时间查找
- `-perm mode`：精确匹配指定的权限模式
- `-perm -mode`：user、group 和 other 至少同时满足指定模式的所有权限位
- `-perm /mode`：user、group 或 other 满足指定模式的一个权限位即可
- `-prune`：不查找指定目录
- `-size`：按文件大小查找
- `-type`：按文件类型查找
- `-user`：按所有者查找
- `-group`：按所属组查找
- `-uid`：按用户 ID 查找
- `-gid`：按组 ID 查找

指定目录深度

```bash
[root@rocky ~]# find /etc -maxdepth 2
/etc
/etc/mtab
/etc/fstab
/etc/crypttab
/etc/resolv.conf
......
```

按文件名查找

```bash
[root@rocky ~]# find /var -name "*.log"
/var/log/audit/audit.log
/var/log/sssd/sssd_kcm.log
/var/log/tuned/tuned.log
/var/log/anaconda/anaconda.log
/var/log/anaconda/X.log
......
```

按文件大小查找

```bash
[root@rocky ~]# find /var -name '*.log' -size +1M
/var/log/audit/audit.log
/var/log/anaconda/journal.log
```

按文件类型查找

```bash
[root@rocky ~]# find /home -maxdepth 1 -type d
/home
/home/zqh
/home/gentoo
/home/nginx
/home/varnish
/home/mysql
/home/demo
```

按所有者、所属组查找

```bash
[root@rocky ~]# find /var -user zqh -ls
201326724      0 -rw-rw----   1  zqh      mail            0 Nov  3 19:36 /var/spool/mail/zqh
```

按权限查找

```bash
[root@rocky test]# ll
total 0
-rw-r--r--. 1 root root 0 Dec  9 17:47 1.txt
-r--rw-r--. 1 root root 0 Dec  9 17:47 2.txt
-r--r--rw-. 1 root root 0 Dec  9 17:47 3.txt
-rw-rw-r--. 1 root root 0 Dec  9 17:47 4.txt
-rw-rw-rw-. 1 root root 0 Dec  9 17:47 5.txt
-rwxrwxrwx. 1 root root 0 Dec  9 17:55 7.txt

[root@rocky test]# find -perm /666 -ls
  1602605      0 drwxr-xr-x   2  root     root           84 Dec  9 17:55 .
  1602610      0 -rw-r--r--   1  root     root            0 Dec  9 17:47 ./1.txt
  1602611      0 -r--rw-r--   1  root     root            0 Dec  9 17:47 ./2.txt
  1602612      0 -r--r--rw-   1  root     root            0 Dec  9 17:47 ./3.txt
  1602614      0 -rw-rw-r--   1  root     root            0 Dec  9 17:47 ./4.txt
  1602615      0 -rw-rw-rw-   1  root     root            0 Dec  9 17:47 ./5.txt
  1602617      0 -rwxrwxrwx   1  root     root            0 Dec  9 17:55 ./7.txt
[root@rocky test]# find -perm -666 -ls

  1602615      0 -rw-rw-rw-   1  root     root            0 Dec  9 17:47 ./5.txt
  1602617      0 -rwxrwxrwx   1  root     root            0 Dec  9 17:55 ./7.txt
```

按组合条件查找
- `-a|-and`：连接两个表达式，两个表达式同时为真时才匹配
- `-o|-or`：连接两个表达式，两个表达式中至少有一个为真时才匹配
- `!|-not`：否定后面的表达式，不满足该表达式时才匹配

```bash
[root@rocky ~]# find / -not \( -type d -a -empty \) | wc -l
329029

[root@rocky ~]# find / -not -type d -o -not -empty | wc -l
329029
```

动作

- `-print`：默认的动作，打印匹配的文件名
- `-ls`：以类似 `ls -l` 的格式列出文件的详细信息
- `-delete`：删除匹配的文件
- `-ok COMMAND {} \;`：询问用户是否执行指定的命令，`{}` 代表匹配的文件
- `-exec COMMAND {} \;`：执行指定的命令，`{}` 代表匹配的文件

```bash
[root@rocky ~]# find /etc -name '*.conf' -exec basename {} \;
resolv.conf
dnf.conf
kpatch.conf
copr.conf
debuginfo-install.conf
dnf.conf
......
```

## 3. 正则表达式

正则表达式（Regular Expression，regex）是用于描述搜索模式的一组特殊字符串

### 基本正则表达式

字符类
- `[ABC]`：匹配 \[ ] 内的任意一个字符
- `[^ABC]`：匹配除了 \[ ] 内的任意一个字符
- `[A-Z]`：匹配任意一个大写字母
- `.`：匹配任意字符（除了换行符 `\n`）

字符类简写
- `\w`：匹配任意字母、数字、下划线
- `\W`：匹配任意非单词字符
- `\d`：匹配任意数字字符
- `\D`：匹配任意非数字字符
- `\s`：匹配任意空白字符（空格、制表符、换行符）
- `\S`：匹配任意非空白字符

锚定
- `^`：匹配字符串的开头
- `$`：匹配字符串的结尾
- `\b`：单词边界，匹配独立的单词

转义字符
- `\t`：制表符
- `\n`：换行符

分组和引用
- `(ABC)`：匹配 "ABC"，并将匹配的部分作为一个整体
- `\1`：引用第一个分组的内容

量词与交替
- `+`：匹配前面的字符 1 次或多次
- `*`：匹配前面的字符 0 次或多次
- `{1,3}`：匹配前面的字符 1 到 3 次
- `?`：匹配前面的字符 0 次到 1 次
- `|`：表示或，匹配两个选择中的任意一个

### 扩展正则表达式

扩展正则表达式不需要在 '?'、'+'，'{'、'}'、'|'、'('、')' 这些符号前加上反斜杠 '\\'

## 4. Shell 变量

### 变量命名规则

- 变量名可以包含数字、字母和下划线
- 变量名不能以数字开头
- 变量名区分大小写
- 避免使用保留字作为变量名
### 环境变量

父进程的环境变量在子进程中可见

声明环境变量

```bash
export NAME=value
```

列出所有环境变量

```bash
$ env
$ printenv
$ export
$ declare -x
```

查看指定进程的环境变量

```bash
$ cat /proc/$PID/environ
```

### 只读变量

只读变量的值不能被修改

声明只读变量

```bash
readonly name=value
```

列出所有只读变量

```bash
$ readonly
```

### 位置变量

- `$1、$2、...`：对应第 1 个、第 2 个、第 n 个参数
- `$0`：脚本或命令本身的名称
- `$*`：表示所有位置参数的列表，作为一个单词保存
- `$@`：表示所有位置参数的列表，每个参数作为一个独立的单词保存
- `$#`：表示传递给脚本或命令的参数个数

```bash
[root@rocky scripts]# cat paras.sh
#!/usr/bin/bash

echo "first parameter: $1"
echo "second parameter: $2"
echo "third parameter: $3"

echo "all parameters: $*"
echo "all parameters: $@"

echo "number of parameters: $#"
echo "script name: $0"

# 执行脚本
[root@rocky scripts]# ./paras.sh a b c
first parameter: a
second parameter: b
third parameter: c
all parameters: a b c
all parameters: a b c
number of parameters: 3
script name: ./paras.sh
```

### 状态变量

- `$?` 的值为 0，代表命令执行成功
- `$?` 的值为 1 ~255，代表命令执行失败

```bash
[root@rocky ~]# ping -c1 qq.com &> /dev/null
[root@rocky ~]# echo $?
0

[root@rocky ~]# lss
bash: lss: command not found...
Similar command is: 'ls'
[root@rocky ~]# echo $?
127
```

## 5. Shell 编程：鸡兔同笼

**问题描述**：鸡和兔共有 80 个头，30 只脚，分别有几只鸡和几只兔？

脚本内容如下

```bash
#!/usr/bin/bash

head=30
feet=80
# 穷举兔子的数量
for rabbit in $(seq $[ $feet / 4 ]); do
    chicken=$[head-rabbit]
    if [ $[chicken * 2 + rabbit * 4 ] -eq $feet ]; then
        echo "rabbit: $rabbit chicken: $chicken"
    fi
done
```

脚本执行结果

```bash
rabbit: 10 chicken: 20
```

## 6. Shell 编程：批量创建 100 个用户

**问题描述**：
- 判断用户 id 是否存在
- 用户存在则输出用户已存在，不存在则创建用户并说明已创建

脚本内容如下

```bash
#!/usr/bin/bash

for i in {1..100}; do
    username=user$i
    # 判断用户 id 是否存在
    if [ $(id -u $username &> /dev/null) ]; then
        echo "useradd: user '$username' already exists"
    else
        useradd $username && echo "user '$username' created successfully."
    fi
done
```

脚本执行结果

```bash
user 'user1' created successfully.
user 'user2' created successfully.
user 'user3' created successfully.
user 'user4' created successfully.
user 'user5' created successfully.
user 'user6' created successfully.
user 'user7' created successfully.
user 'user8' created successfully.
user 'user9' created successfully.
user 'user10' created successfully.
......
```

再次执行脚本，输出结果如下

```bash
useradd: user 'user1' already exists
useradd: user 'user2' already exists
useradd: user 'user3' already exists
useradd: user 'user4' already exists
useradd: user 'user5' already exists
useradd: user 'user6' already exists
useradd: user 'user7' already exists
useradd: user 'user8' already exists
useradd: user 'user9' already exists
useradd: user 'user10' already exists
......
```

## 7. 机械磁盘术语

- 盘片（Platter）：磁盘中用来存储磁性数据的圆形磁盘。磁盘的主轴上通常有多个盘片，每个盘片的上下两面都能存储信息，因此一个盘片需要两个磁头来读写数据
- 磁头（Read/Write Head）：磁盘中的关键组件之一，负责从磁盘读取数据或将数据写入磁盘
- 磁道（Track）：当磁盘旋转时，磁头保持在一个位置，则每个磁头都会在磁盘表面划出一个圆形轨迹，这些圆形轨迹叫作磁道。一个磁盘表面包含多个同心圆状的磁道
- 扇区（Sector）：磁盘上的每个磁道被等分成若干个弧段，这些弧段是硬盘的扇区
- 柱面（Cylinder）：由不同盘片的面，但处于同一半径圆的多个磁道组成的一个圆柱面

## 8. MBR 和 GPT 

### MBR

主引导记录（Master Boot Record，MBR）又叫作主引导扇区，是计算机开机后访问硬盘时必须要读取的第一个扇区，主引导扇区记录着硬盘本身的相关信息以及硬盘各个分区的大小和位置信息

标准 MBR 结构

![](https://github.com/zqhgithubuser/Homework/blob/main/Week2/images/Pasted_image_20231209200157.png)

特点：
- MBR 分区表的大小为 64 字节，每个分区条目的大小为 16 字节。因此硬盘可以划分 4 个主分区或 3 个主分区 + 1 个扩展分区，扩展分区可以划分为多个逻辑分区
- MBR 分区使用 8 字节（32 位）表示扇区号范围，因此 MBR 分区表支持的最大分区大小为 2^32 * 512 字节，约等于 2T

### GPT

全局唯一标识分区表（GUID Partition Table，GPT）是硬盘的一种分区表布局标准

逻辑块寻址（LBA）作为一种更现代、更简单的寻址方式，取代了早期的柱面-磁头-扇区（CHS）寻址方式。硬盘被划分为块，每个块都有一个唯一的逻辑块地址

GPT 结构：
- LBA 0：保护性 MBR。用于兼容仅支持 MBR 的系统
- LBA 1：主 GPT 表头。包含分区表头本身的位置和大小，备份分区表头的位置以及分区条目的大小和数量等信息
- LBA 2 ~ 23：每个分区条目的信息

## 9. 磁盘管理与文件系统相关操作

### 分区

**`fdisk`**：管理 MBR 分区表

常用选项
- `-l`：显示分区的详细信息

常用交互命令
- `d`：删除分区
- `n`：创建新分区
- `p`：打印分区表
- `t`：改变分区类型
- `w`：保存分区表后退出
- `q`：不保存并退出

```bash
[root@rocky ~]# fdisk /dev/sdb

# 创建主分区
Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (1-4, default 1):
First sector (2048-41943039, default 2048):
Last sector, +sectors or +size{K,M,G,T,P} (2048-41943039, default 41943039): +1G

Created a new partition 1 of type 'Linux' and of size 1 GiB.

Command (m for help): p
Disk /dev/sdb: 20 GiB, 21474836480 bytes, 41943040 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xdba7efe1

Device     Boot Start     End Sectors Size Id Type
/dev/sdb1        2048 2099199 2097152   1G 83 Linux

# 创建扩展分区
Command (m for help): n
Partition type
   p   primary (1 primary, 0 extended, 3 free)
   e   extended (container for logical partitions)
Select (default p): e
Partition number (2-4, default 2): 4
First sector (2099200-41943039, default 2099200):
Last sector, +sectors or +size{K,M,G,T,P} (2099200-41943039, default 41943039): +5G

Created a new partition 4 of type 'Extended' and of size 5 GiB.

Command (m for help): p
Disk /dev/sdb: 20 GiB, 21474836480 bytes, 41943040 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xdba7efe1

Device     Boot   Start      End  Sectors Size Id Type
/dev/sdb1          2048  2099199  2097152   1G 83 Linux
/dev/sdb4       2099200 12584959 10485760   5G  5 Extended

# 创建逻辑分区
Command (m for help): n
Partition type
   p   primary (1 primary, 1 extended, 2 free)
   l   logical (numbered from 5)
Select (default p): l

Adding logical partition 5
First sector (2101248-12584959, default 2101248):
Last sector, +sectors or +size{K,M,G,T,P} (2101248-12584959, default 12584959): +1G

Created a new partition 5 of type 'Linux' and of size 1 GiB.

Command (m for help): p
Disk /dev/sdb: 20 GiB, 21474836480 bytes, 41943040 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xdba7efe1

Device     Boot   Start      End  Sectors Size Id Type
/dev/sdb1          2048  2099199  2097152   1G 83 Linux
/dev/sdb4       2099200 12584959 10485760   5G  5 Extended
/dev/sdb5       2101248  4198399  2097152   1G 83 Linux

# 保存分区表
Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.
```

**`gdisk`**：管理 GPT 分区表

常用选项
- `-l`：显示分区的详细信息

### 创建文件系统

**`mkfs`**：创建文件系统

创建 ext4 文件系统

```bash
[root@rocky ~]# mkfs.ext4 /dev/sdb1
mke2fs 1.45.6 (20-Mar-2020)
Creating filesystem with 262144 4k blocks and 65536 inodes
Filesystem UUID: fc5e8a64-f4c3-44c0-9636-8967d743b194
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376

Allocating group tables: done
Writing inode tables: done
Creating journal (8192 blocks): done
Writing superblocks and filesystem accounting information: done
```

创建 xfs 文件系统

```bash
[root@rocky ~]# mkfs.xfs /dev/sdb5
meta-data=/dev/sdb5              isize=512    agcount=4, agsize=65536 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=1, sparse=1, rmapbt=0
         =                       reflink=1    bigtime=0 inobtcount=0
data     =                       bsize=4096   blocks=262144, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0, ftype=1
log      =internal log           bsize=4096   blocks=2560, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
```

**`tune2fs -l`**：显示 ext 文件系统信息

```bash
[root@rocky ~]# tune2fs -l /dev/sdb1
tune2fs 1.45.6 (20-Mar-2020)
Filesystem volume name:   <none>
Last mounted on:          <not available>
Filesystem UUID:          fc5e8a64-f4c3-44c0-9636-8967d743b194
Filesystem magic number:  0xEF53
Filesystem revision #:    1 (dynamic)
......
```

**`xfs_info`**：显示 xfs 文件系统信息

```bash
[root@rocky ~]# xfs_info /dev/sdb5
meta-data=/dev/sdb5              isize=512    agcount=4, agsize=65536 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=1, sparse=1, rmapbt=0
         =                       reflink=1    bigtime=0 inobtcount=0
data     =                       bsize=4096   blocks=262144, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0, ftype=1
log      =internal log           bsize=4096   blocks=2560, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
```

### 挂载

**`mount`**：挂载文件系统
- `-t`：指定文件系统类型
- `-o`：指定挂载选项

挂载选项
- `async`：文件系统不会等待数据被写入磁盘后才认为写入操作完成
- `sync`：文件系统会等待数据被写入磁盘后才认为写入操作完成
- `dev`：允许访问设备文件
- `nodev`：不允许访问设备文件
- `exec`：允许执行二进制文件
- `noexec`：不允许执行二进制文件
- `suid`：允许设置 SUID 和 SGID 位
- `nosuid`：不允许设置 SUID 和 SGID 位
- `auto`：允许使用 `-a` 选项挂载文件系统
- `noauto`：不允许使用 `-a` 选项挂载文件系统
- `ro`：以只读的方式挂载文件系统
- `rw`：以读写的方式挂载文件系统
- `user`：允许普通用户挂载文件系统
- `nouser`：不允许普通用户挂载文件系统
- `defaults`：默认使用 `rw`、`suid`、`dev`、`exec`、`auto`、`nouser` 和 `async` 选项

```bash
# 创建挂载点
[root@rocky ~]# mkdir /sdb1 /sdb5

# 挂载文件系统
[root@rocky ~]# mount /dev/sdb1 /sdb1
[root@rocky ~]# mount /dev/sdb5 /sdb5

# 显示挂载情况
[root@rocky ~]# lsblk /dev/sdb
NAME   MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
sdb      8:16   0  20G  0 disk
├─sdb1   8:17   0   1G  0 part /sdb1
├─sdb4   8:20   0   1K  0 part
└─sdb5   8:21   0   1G  0 part /sdb5
```

**`umount`**：卸载文件系统

```bash
# 指定分区
[root@rocky ~]# umount /dev/sdb1

# 指定挂载点
[root@rocky ~]# umount /sdb5

# 查看挂载情况
[root@rocky ~]# lsblk /dev/sdb
NAME   MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
sdb      8:16   0  20G  0 disk
├─sdb1   8:17   0   1G  0 part
├─sdb4   8:20   0   1K  0 part
└─sdb5   8:21   0   1G  0 part
```

开机自动挂载文件系统
- 查看分区的 UUID 

```bash
[root@rocky ~]# blkid
......
dev/sdb1: UUID="fc5e8a64-f4c3-44c0-9636-8967d743b194" BLOCK_SIZE="4096" TYPE="ext4" PARTUUID="dba7efe1-01"
/dev/sdb5: UUID="2241eb35-22ef-4ff6-8e2b-61281c7fe4b1" BLOCK_SIZE="512" TYPE="xfs" PARTUUID="dba7efe1-05"
......
```

- 编辑 `/etc/fstab` 文件，添加挂载条目

```bash
UUID=fc5e8a64-f4c3-44c0-9636-8967d743b194 /sdb1                   ext4    defaults        0 0
UUID=2241eb35-22ef-4ff6-8e2b-61281c7fe4b1 /sdb5                   xfs     defaults        0 0
```

### swap 分区管理

**`mkswap`**：创建 swap 分区

```bash
[root@rocky ~]# mkswap /dev/sdc1
Setting up swapspace version 1, size = 4 GiB (4294963200 bytes)
no label, UUID=64795a67-a603-4e47-8b84-3b8e1c2c9d8f
```

编辑 `/etc/fstab` 文件，添加 swap 挂载条目

```bash
/dev/sdc1               none                    swap    defaults        0 0
```

**`swapon`**：启用 swap 分区

常用选项
- `-a`：启用 `/etc/fstab` 文件中的所有的 swap 分区

```bash
[root@rocky ~]# swapon -a

# 查看 swap 空间变化
[root@rocky ~]# free -h
              total        used        free      shared  buff/cache   available
Mem:          1.7Gi       576Mi       747Mi        10Mi       422Mi       1.0Gi
Swap:         6.0Gi          0B       6.0Gi

# 显示当前已启用的 swap 分区的信息
[root@rocky ~]# cat /proc/swaps
Filename                                Type            Size            Used            Priority
/dev/dm-1                               partition       2117628         0               -2
/dev/sdc1                               partition       4194300         0               -3
```

**`swapoff`**：禁用 swap 分区
- `-a`：禁用 `/etc/fstab` 文件中的所有的 swap 分区

```bash
[root@rocky ~]# swapoff /dev/sdc1
[root@rocky ~]# cat /proc/swaps
Filename                                Type            Size            Used            Priority
/dev/dm-1                               partition       2117628         0               -2
```

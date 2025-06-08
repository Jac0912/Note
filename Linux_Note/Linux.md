# 1. setup
```shell
wsl --list  #查看当前安装的wsl
wsl --unregister <版本>  #卸载相关版本
sudo apt-get update  #更新软件包
sudo  passwd root  #更改root密码
```

# 2. shell
```shell
#标准输出重定向
<命令> > <文件名>  #进程输出覆盖文件
<命令> >> <文件名>  #进程输出追加到文件，不覆盖
<命令>: echo / cat ...

#标准输入重定向
<命令> < <文件名>  #模拟手动把文件中的内容输入命令中

#标准错误输出重定向
<命令> 2> <文件名>  #将运行中的错误信息重定向到文件中
cat file1 file2 > file3 2> errfile  #若正常，连接file1、file2存入file3中；若错误，将错误信息放到errfile中

#管道
<命令1> | <命令2>  #对命令1来说标准输出重定向到管道，对命令2来说标准输入重定向为管道

<命令1>; <命令2>; ...  #连续执行多个命令
<命令> &  #后台执行命令

echo <内容>  #输出字符串、变量值，若有空格则用 " "
echo This   is   a     test.  #每个字符独立的输入echo，echo挨个输出并用空格分隔，结果：This if a test.
echo "This   is   a     test."  #作为整体输入echo，结果：This   is   a     test.

$ history  #显示历史命令
$ !3  #快速执行编号为3的命令

$ cp sour.txt dest.txt
#将sour文件内容复制到dest
#参数-i 或 --interactive：在覆盖文件之前提示用户进行确认。
#参数-n 或 --no-clobber：不覆盖已存在的文件。

$ cp -r sour/ dest/  #将文件夹内容递归复制
$ mv file.txt /path/to/destination/new.txt  #移动并重命名文件为new.txt
$ mv dir /path/to/destination/  #移动文件夹

$ tar -tf foo.tar.gz
#-c：创建一个新的归档文件（压缩文件）。
#-x：从归档文件中提取文件。
#-f：指定归档文件的名称，可以是新创建的或已存在的。
#-v：详细模式（verbose），在执行操作时显示详细信息。
#-z：使用 gzip 压缩归档文件。
#-j：使用 bzip2 压缩归档文件。
#-t：列出归档文件中包含的文件。
#-r：向归档文件中添加文件。
#-u：更新归档文件，只添加新文件或更新已更改的文件。
#--delete：从归档文件中删除文件。
#--list：列出归档文件中包含的文件。
#--strip-components：在提取文件时去除前面的路径组件。
#-C：指定提取文件的目标目录。

$ su <username>  #切换用户
$ mkdir -p <文件路径>  # -p:如果任意一级父目录不存在,则创建
$ groups  #查看当前用户所属组
$ ./operate_file  #Linux下执行c语言编译好的执行文件

#Linux查看当前进程
$ ps aux
$ ps aux --sort=-%mem

$ vim ~./bashrc  #将环境变量持久化

df -h  #显示外存占用情况
sudo du -sh <文件夹名>  #显示文件夹占用外存

free -h  #内存使用情况
htop  #动态显示内存

sudo systemctl list-units  #查看存在的服务

who  #查看已登录用户的状况
pwd  #输出工作路径，Print Working Directory
kill  #删除执行中的程序

ps  #列出当前运行的进程
-e  #显示所有活动进程信息
-f  #显示进程所有信息，UID：所有者用户标识，PID：进程标识，PPID：父进程标识，C：进程调度参数，STIME：进程启动时间，TTY：启动进程的终端，TIME：累计占用CPU的时间，COMMAND：启动进程的命令名
-e  #

man  #查看帮助，Manual
sort <文件>  #输出文件排序后的结果

chmod <谁><操作><权限> 文件名
#角色：u(用户)，g(组)，o(其他人)，a(所有人)
#操作符：+(添加权限)，-(删除权限)，=(赋予权限)
#权限类型：r(读)，w(写)，x(执行)

chmod <用户权限><组权限><其他人权限> 文件名
r=4, w=2, x=1
7 = r+w+x
6 = r+w
5 = r+x
4 = r
0 = 无权限
```

# 3. 环境变量
```shell
HOME=...  #用户主目录
PATH=...  #键盘命令搜索路径
SHELL=..  #用户初始shell路径
TERM=vt100  #所用终端类型
PS1=$  #shell主提示符
```

# 4. 变量

| 系统变量  | 含义               |
| ----- | ---------------- |
| $0    | 当前shell程序名字      |
| $1~$9 | 命令行上第1到9个参数      |
| $#    | 命令行上参数个数         |
| $*    | 命令行上所有参数         |
| $@    | 分别用双引号引用命令行上所有参数 |
| \$\$  | 当前进程标识号（PID）     |
| $?    | 上一条命令退出状态        |
| $!    | 最后一个后台进程的PID     |

**用户变量**
```shell
#变量定义
AA=123
B="fs fsdf"

echo $B  #引用变量

#shell规定，单引号禁止变量替换，双引号允许变量替换
a="he is a student" 
echo "She said: $a"
She said: he is a student 

b='The value of a is $a'
echo $b
The value of a is $a

#反撇号
a=date
echo $a
date    #(变量a的值是字符串date)
b=`date`  #(反撇号中的字符串作为命令名)
echo $b
Sat Feb 1 16:28:19 Beijing 2003

#花括号
c="There is a teach"
echo "$cer reading room"
reading room  #(未定义变量cer, 其值用空串替代)

echo "${c}er reading room"
There is a teacher reading room  #(花括号将变量名和后面的字符串区分开)

export <变量>  #输出变量，变量前不加 $，输出后可在其他shell使用
```

# 5. win shell
```shell
# 测试端口连接
Test-NetConnection 1.95.183.0 -Port 3306
```

# 6. hot key
```shell
Ctrl + h  #删除字符
Ctrl + u  #删除一整行
Ctrl + c  #终止程序
Ctrl + d  #退出shell
```

# 7. 创建配置普通用户
```shell
sudo useradd -m -s /usr/bin/bash <username>  #-m 创建用户主目录;  -s 设定shell
useradd <选项> <用户名>
-c: comment，指定一段注释性描述
-d: 指定用户主目录，若目录不存在，同时使用 -m 来创建主目录
-g: 用户组，指定用户默认的用户组
-G: 配合 -a 完成向其他组的添加
-s: 指定用户登录的shell
-u: 指定用户的用户号，若同时有 -o，则可以重复使用其他用户的标识号

adduser <名称>  #创建新用户
userdel <名称>  #删除用户，加 -r 可删除用户的主目录
passwd <username>  #设置登录密码

usermod <选项> <用户名>  #修改已经存在的用户信息，选项与useradd相同
sudo usermod -G sudo <username>  #加入sudo组,提供sudo权限
sudo usermod -G docker <username>  #加入docker组,提供docker权限
```
# 8. 配置 ssh
```shell
~/.ssh/authorized_keys  #服务器存放本地公钥
sudo systemctl status redis  #redis状态
```
# 9. 扫描病毒
```shell
sudo apt install clamav
sudo freshclam  # 更新病毒库
sudo clamscan -r /  # 扫描整个系统
```


# 10. boom
1. apt 无法使用，可以正常 ping 通，但是无法安装软件
`sudo vim  /etc/resolv.conf `，将 nameserver 换为谷歌 DNS：`8.8.8.8`

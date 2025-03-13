## shell
```shell
$ echo "It is a test" > myfile  #显示结果定向到文件并覆盖文件内容，若文件不存在则创建文件
$ echo "It is a test" >> myfile  #显示结果定向到文件末尾，若文件不存在则创建文件
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
```
## 创建配置普通用户
```shell
sudo useradd -m -s /usr/bin/bash <username>  #-m 创建用户主目录;  -s 设定shell
adduser <名称>  #创建新用户
deluser <名称>  #删除用户
passwd <username>  #设置登录密码
sudo usermod -G sudo <username>  #加入sudo组,提供sudo权限
sudo usermod -G docker <username>  #加入docker组,提供docker权限
```
## 配置ssh
```shell
~/.ssh/authorized_keys  #服务器存放本地公钥
sudo systemctl status redis  #redis状态
```
## 扫描病毒
```shell
sudo apt install clamav
sudo freshclam  # 更新病毒库
sudo clamscan -r /  # 扫描整个系统
```
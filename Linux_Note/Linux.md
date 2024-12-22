# Linux

## shell

```shell
$ echo "It is a test" > myfile   
#显示结果定向到文件并覆盖文件内容，若文件不存在则创建文件
$ echo "It is a test" >> myfile 
#显示结果定向到文件末尾，若文件不存在则创建文件

$ history
#显示历史命令

$ !3
#快速执行编号为3的命令

$ cp sour.txt dest.txt
#将sour文件内容复制到dest
#参数-i 或 --interactive：在覆盖文件之前提示用户进行确认。
#参数-n 或 --no-clobber：不覆盖已存在的文件。

$ cp -r sour/ dest/
#将文件夹内容递归复制


$ mv file.txt /path/to/destination/new.txt
#移动并重命名文件为new.txt

$ mv dir /path/to/destination/
#移动文件夹

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

$ su <username>                  #切换用户
$ mkdir -p <文件路径>              # -p:如果任意一级父目录不存在,则创建
$ groups                          #查看当前用户所属组

$ ./operate_file
#Linux下执行c语言编译好的执行文件

#Linux查看当前进程
$ ps aux
$ ps aux --sort=-%mem


```

## 创建配置普通用户

```shell
sudo useradd -m -s /usr/bin/bash <username>   #-m 创建用户主目录;  -s 设定shell
adduser <名称>   #创建新用户
deluser <名称>   #删除用户
passwd <username>                             #设置登录密码
sudo usermod -G sudo <username>               #加入sudo组,提供sudo权限
sudo usermod -G docker <username>             #加入docker组,提供docker权限
```



```shell
sudo systemctl start mysql  #启动MySQL

/usr/lib/jvm/java-11-openjdk-amd64/bin/java  #阿里云中java的位置
mysql -u root -p #登陆sql
CREATE DATABASE <name>; #创建数据库
CREATE USER 'Jac0912'@'%' IDENTIFIED BY '012284';    #创建可以通过网络访问的用户
GRANT ALL PRIVILEGES ON *.* TO 'Jac0912'@'%' WITH GRANT OPTION;     #赋予所有权利
GRANT ALL PRIVILEGES ON <库名称>.* TO 'Jac0912'@'%';   #<库>.<表>，赋予操作某一数据库所有表的权利
SELECT user, host FROM mysql.user; #查询用户
 cat /etc/mysql/mysql.conf.d/mysqld.cnf #配置文件所在位置
 sudo systemctl restart mysql #重启sql服务
nmap -p 3306 47.109.141.56 #测试端口
http://localhost:8080/tsAdmin/org/login


#针对低版本sql修改认证
ALTER USER 'traffic'@'%' IDENTIFIED WITH mysql_native_password BY 'fucking';
FLUSH PRIVILEGES；

#交通
traffic
fucking

#一哥
yiGe     #账号名
yiGeee    #密码
tsadmin     #库名

启动暂停tomcat
cd /opt/tomcat/bin
sudo sh startup.sh
sudo sh shutdown.sh
```


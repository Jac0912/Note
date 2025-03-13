# 常用命令
```shell
sudo systemctl start mysql  #启动sql
sudo systemctl restart mysql  #重启sql
mysql -u root -p  #以root用户使用密码登陆sql
CREATE DATABASE <name>;  #创建数据库
CREATE USER '<用户名>'@'%' IDENTIFIED BY '<密码>';  #创建可以通过网络访问的用户
GRANT ALL PRIVILEGES ON *.* TO '<用户名>'@'%' WITH GRANT OPTION;  #赋予所有权利
GRANT ALL PRIVILEGES ON <库名称>.* TO '<用户>'@'%';  #<库>.<表>，赋予操作某一数据库所有表的权利
SELECT user, host FROM mysql.user;  #查询用户
cat /etc/mysql/mysql.conf.d/mysqld.cnf  #配置文件所在位置
nmap -p 3306 <服务器ip>  #测试端口

#针对低版本sql修改认证
ALTER USER 'traffic'@'%' IDENTIFIED WITH mysql_native_password BY 'fucking';
FLUSH PRIVILEGES;
```
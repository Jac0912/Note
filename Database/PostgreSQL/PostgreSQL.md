```sql
sudo -i -u postgres  --以postgres身份进入新shell环境（-i: 模拟登录，获得行的shell环境）
psql  --进入postgresql命令行客户端工具

CREATE USER <user> WITH PASSWORD '<password>';  --创建新用户
ALTER USER <user> WITH SUPERUSER;  --赋予<user>SUPERUSER的权限
\du  --产看用户权限

SELECT datname FROM pg_database;  --查看所有数据库
SELECT nspname FROM pg_namespace;  --查看所有的模式

\c <数据库名>  --进入数据库

```

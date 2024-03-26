# Linux

## shell

```shell
$ echo "It is a test" > myfile   #显示结果定向到文件
$ su <username>                  #切换用户
$ mkdir -p <文件路径>              # -p:如果任意一级父目录不存在,则创建
$ groups                          #查看当前用户所属组
```

## 创建配置普通用户

```shell
sudo useradd -m -s /usr/bin/bash <username>   #-m 创建用户主目录;  -s 设定shell
passwd <username>                             #设置登录密码
sudo usermod -G sudo <username>               #加入sudo组,提供sudo权限
sudo usermod -G docker <username>             #加入docker组,提供docker权限
```


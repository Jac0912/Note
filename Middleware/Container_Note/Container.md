# 1. Docker 命令
## 1.1. 安装
```shell
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done

# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update

sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

sudo apt install -y docker-compose  #安装docker compose
```
## 1.2. 拉取运行容器镜像
```shell
$ docker search <ImageName>  #查询镜像
$ docker pull <ImageName>    #拉取镜像
$ docker images              #查看本地镜像

# 挂载宿主目录
#<容器名>:<容器文件路径> <宿主文件路径>，从启动的容器中将配置文件复制到宿主中 (避免挂载后容器找不到配置文件)
$ docker cp nginx:/etc/nginx/nginx.conf /opt/docker/nginx/  
#运行
$ docker run -d --restart=always \
			--name nginx \
			-p 80:80 \
			-v <宿主目录>:<容器目录>
			……
			nginx

# 简单运行
$ docker run -d --name <启动后容器名称> -p <宿主端口>:<容器端口> <镜像名称>  #在后台运行该docker容器，并打印容器ID
$ docker ps     #查看正在运行的容器信息
$ docker ps -a  #查看正在或停止运行的容器信息

$ docker stop <容器名>               #停止容器
$ docker start <容器名>              #启动容器
$ curl <本地IP>                      #命令行验证
$ docker exec -it <容器名> /bin/bash 
#-i:保持交互模式 -t:分配伪终端 /bin/bash:待执行命令,进入bash shell
$ exit                              #出容器

# 查看容器日志
$ docker logs <容器名>

# 删除容器
$ docker stop <容器名>
$ docker rm <容器名>
$ docker rmi <镜像名>
   ```
## 1.3. Dockerfile
Dockerfile 文件放在项目的根目录下
```dockerfile
From ubuntu:22.04
#使用Ubuntu为基础镜像

LABEL maintainer="<2023091201003@uestc.edu.cn>"
#维护者信息

WORKDIR /app
#设置工作目录

RUN apt-get update && apt-get install -y \
    nginx \
    python3 \
    python3-distutils
#安装nginx&python

RUN rm -rf /var/lib/apt/lists/*
#清理apt软件包缓存

EXPOSE 80
#声明暴露端口

CMD["nginx","-g","daemon off;"]
#设置启动命令
```

基于 Dockerfile 创建容器镜像
```shell
$ docker build -t <自定义镜像名称> .
#-t:镜像名称(REPOSITORY:TAG)  
#.:PATH,执行命令的上下文路径,构造过程中可引用该上下文路径中的任何文件

$ docker run -d --name npu -p 80:80 npu
$ curl <私有IP>:80

$ docker exec -it npu /bin/bash
#进入容器

$ python3
#检测python
```
## 1.4. ACR 镜像管理与应用

实际应用时需配置持续集成、持续部署流水线，在⌈构建镜像⌋阶段将代码打包到基础镜像后推送至指定镜像仓库 (Registry),在⌈服务部署⌋阶段从镜像仓库拉取镜像并部署在 ECS 服务器或集群。
### 1.4.1. 推送容器镜像至 ACR
查询仓库 URL
```shell
registry.cn-wulanchabu.aliyuncs.com/docker_practice_jac0912/exp_pyms
#registry.<region>.aliyuncs.com/<namespace>/exp_pyms
```
重命名**镜像**
```shell
$ docker tag <IMAGE ID或镜像名称> registry.<region>.aliyuncs.com/<namespace>/npu

$ docker images|grep <镜像名>
#查看结果   grep:过滤器
```
登录阿里云 ACR，并推送镜像
```shell
$ docker login --username=<用户名> registry.<region>.aliyuncs.com
$ docker push registry.<region>.aliyuncs.com/<namespace>/npu
```
### 1.4.2. 拉取容器镜像至 ECS
1. 需先删除本地相关容器以及镜像，以免干扰
2. 拉取并创建容器
   ```shell
   $ docker pull registry.<region>.aliyuncs.com/<namespace>/npu
   $ docker run -d --name npu -p 80:80 registry.<region>.aliyuncs.com/<namespace>/npu
   $ curl <ECS公网IP>
   ```
注：`镜像名后:TAG` 默认 TAG 为 `latest`,可省略 `:TAG`
## 1.5. 配置远程
修改 docker.service 文件
```
vim /usr/lib/systemd/system/docker.service
```
需要修改的部分：
```
ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock
```
修改后的部分：
```
ExecStart=/usr/bin/dockerd -H tcp://0.0.0.0:2375 -H unix://var/run/docker.sock
```
## 1.6. 常用命令
```shell
systemctl daemon-reload  #配置生效

systemctl stop docker  #停止
systemctl start docker  #开始
systemctl restart docker  #重启

docker exec -it <容器id> /bin/bash  #进入容器

#以TLS方式连接docker
dockerd \
    --tlsverify \
    --tlscacert=ca.pem \
    --tlscert=server-cert.pem \
    --tlskey=server-key.pem \
    -H=0.0.0.0:2376
#TLS方式下，将环境变量持久化
export DOCKER_TLS_VERIFY="1"
export DOCKER_CERT_PATH="/root"
export DOCKER_HOST="tcp://127.0.0.1:2376"

docker tag <容器id> <名字>:<版本>  #镜像打标签
ps aux | grep docker  #获取docker进程
/etc/docker/daemon.json  #守护进程的配置
sudo systemctl disable docker  #关闭开机自启
docker logs <容器id或容器名>  #查看容器日志
```

## 1.7. 持久化存储
```shell
docker run -v <宿主主机路径>:<容器路径> <容器id或容器名>  #进行数据卷挂载，若不指定宿主主机路径，则由docker来创建文件并管理
```

docker run -p 8080:8080 -v /var/www/html/images/:/usr/src/photo/ 5cd02430a62d


```yml
version: '3.8'

services:
  redis:
    image: redis:latest
    container_name: redis-server
    ports:
      - "6379:6379"
    command: ["redis-server", "--requirepass", "synxspringboot", "--bind", "0.0.0.0", "--protected-mode", "no"]
    volumes:
      - redis_data:/data
    restart: always

  # MySQL 服务
  mysql:
    image: mysql:8.0
    container_name: mysql
    environment:
      MYSQL_ROOT_PASSWORD: "synxspringboot"  # 修改为你的 root 密码
      MYSQL_DATABASE: "user_service"       # 可选：初始数据库
    ports:
      - "3306:3306"
    volumes:
      - mysql_data:/var/lib/mysql
    restart: always
    
  # RabbitMQ 服务
  rabbitmq:
    image: rabbitmq:management
    container_name: rabbitmq
    environment:
      RABBITMQ_DEFAULT_USER: "admin"     # 默认用户名
      RABBITMQ_DEFAULT_PASS: "synxspringboot"  # 默认密码
    ports:
      - "5672:5672"   # AMQP 端口
      - "15672:15672" # Web 管理界面
    volumes:
      - rabbitmq_data:/var/lib/rabbitmq
    restart: always
  
volumes:
  redis_data:
    driver: local
    driver_opts:
      type: none
      device: /root/JacDocker/data/redis_data
      o: bind
  mysql_data:
    driver: local
    driver_opts:
      type: none
      device: /root/JacDocker/data/mysql_data
      o: bind
  rabbitmq_data:
    driver: local
    driver_opts:
      type: none
      device: /root/JacDocker/data/rabbitmq_data
      o: bind
```
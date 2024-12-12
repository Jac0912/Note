# Container

## 容器原理

### namespaces

Linux内核提供的一种机制，通过创建独立的命名空间来实现进程之间的资源隔离和沙箱化

```shell
ll /proc/$$/ns   //查看进程所属命名空间
```

1. 每个namespace是一个软链接，软链接名指示namespace的类型
2. 指向进程所属真正的namespace对象

### cgroups

可以限制单个或多个进程所使用资源的机制，可对CPU、内存的资源实现精细化控制

- 资源限制
- 优先级设置
- 资源统计与监控
- 进程组控制

### Container

被隔离、被限制的Linux进程，试用时需对进程容器化，是一个将应用程序及其必要依赖打包在一起构成的标准化执行单元

### 容器镜像（Container Image）

将应用程序及其依赖打包为可移植单元，是一个静态文件，用与在计算基础设施上进行隔离的进程，可被部署在不同的环境中

### 容器镜像服务

创建、储存、分享容器镜像，提供中心化储存库，可将创建的容器镜像上传到该库，从中下载已有的镜像

### 容器引擎(Container Engine)

一种软件平台，实现一个操作系统内核上运行多个独立的容器实例

负责：

- 处理来自用户(或容器编排器)请求

- 从远程仓库拉取容器镜像

- 解包容器镜像

- 准备容器挂载点

- 准备需要传递给容器运行时的元数据

- 调用容器运行时

  ![image-20240316154259123](C:\Users\welco\AppData\Roaming\Typora\typora-user-images\image-20240316154259123.png)

### 容器编排(Container Orchestration)

一种自动化技术，提供了一种无需关注底层基础架构的方式管理容器化应用程序

自动处理容器的创建、分发和调度以及容器间的网络连接

## Docker原理

### Docker

一种开源的容器化平台，用于构建、分享、运行应用程序

### Docker容器数据存储

默认容器运行产生的临时文件都存储在容器内部，并且随容器的生命周期结束而丢失

弊端：

- 生命周期结束时，数据销毁，不利于容器间数据共享
- 容器可写层与运行容器的主机耦合度高，数据迁移困难
- 写入可写层需要存储驱动来完成，相比直接写入Docker主机的文件系统，引入额外开销

因此引入数据卷和绑定挂载两种数据管理方式

#### 数据卷(Volumes)

Docker管理的特殊目录，存储在Docker主机的指定位置，数据卷挂载会将容器内对应的挂载目录内容填充到数据卷中(数据卷将被容器内的文件和目录内容初始化)

##### 场景：

- Docker宿主机目录或文件结构不确定
- 需要进行Docker主机备份、恢复、迁移到另一台Dockers主机

##### 使用：

1. `docker volume create [VOLUME_NAME]`显示创建卷(具名卷)

未指定名称会创建随机名称的数据卷(匿名卷)

2. 对数据卷挂载

#### 绑定挂载(Bind Mounts)

将Docker主机上的任意文件或目录挂载到容器中，用Docker主机的文件或目录填充容器内挂载的目录(可通过容器中运行的进程直接修改主机文件系统的内容)

##### 场景：

- 文件从主机共享到容器
- 在Docker主机上的开发环境和容器之间共享源代码或构建工件时
- 保证Docker主机的文件或目录结构与容器所需的绑定挂载一致时

##### 使用：

1. Docker主机文件准备
2. 数据卷挂载

### Dockerfile

静态文本文件，Docker引擎通过解析Dockerfile中的指令来自动化构建镜像

格式：

- 指令忽略大小写(建议大写)
- 每条指令后至少有一个参数

#### 优化

##### 减少镜像层数

Docker镜像分层储存，上层需要修改文件时，通过CoW策略复制一份下层文件并修改(下层文件始终存在)，层数越多，体积越大

##### 删除非必要的包和缓存

安装软件包时，包管理工具会将下载的软件包及其相关文件储存到缓存目录中

##### 使用多阶段构建

解决编译环境留下来的多余文件(不常用)

##### 使用.dockerignore文件进行忽略

减少考虑环境上下文内容，减少压缩文件以及网络传输时间的开销

##### 充分利用缓存镜像层

镜像构建会先检查可用缓存镜像层，若存在则直接使用

若对某层进行修改，则该层包括其所有下游层均失效

`Docker指令按更改频率从低到高排列`

## Docker命令

### 安装

```shell
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
 #安装Docker：
 sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
  #加入用户组：
  sudo usermod -aG docker <用户名>
```

### 拉取容器镜像

```shell
$ docker search <ImageName>  #查询镜像
$ docker pull <ImageName>    #拉取镜像
$ docker images              #查看本地镜像
```

### 基于镜像创建并行容器

#### 创建简单运行

```shell
$ docker run -d --name <启动后容器名称> -p <宿主端口>:<容器端口> <镜像名称>  #在后台运行该docker容器，并打印容器ID
$ docker ps     #查看正在运行的容器信息
$ docker ps -a  #查看正在或停止运行的容器信息
```

#### 挂载宿主目录

对容器中部署的服务，需将所依赖的配置文件、数据文件持久化储存(否则容器销毁后，文件也随之销毁)

需在容器创建运行时，将宿主系统中用于存储配置文件、代码文件、数据文件的目录(甚至具体文件)映射到容器中对应的目录或文件

让容器创建启动时，从宿主系统中指定目录文件加载配置文件；生成数据时，写入宿主系统指定的目录文件

1. 创建宿主目录

2. 从启动的容器中将配置文件复制到宿主中(避免挂载后容器找不到配置文件)

   ```shell
   $ docker cp nginx:/etc/nginx/nginx.conf /opt/docker/nginx/  #<容器名>:<容器文件路径> <宿主文件路径>
   ……
   ```

3. 为创建挂载宿主目录的容器，需删除当前容器

   ```shell
   $ docker stop <容器名或容器ID>  #先停止运行
   $ docker rm <容器名>           #删除容器
   ```

4. 重新设置，完成文件与目录的挂载

   ```shell
   $ docker run -d --restart=always \
                --name nginx \
                -p 80:80 \
                -v <宿主目录>:<容器目录>
                ……
                nginx
   ```


### 停止、启动、进出容器

```shell
$ docker stop <容器名>               #停止容器
$ docker start <容器名>              #启动容器
$ curl <本地IP>                      #命令行验证
$ docker exec -it <容器名> /bin/bash 
#-i:保持交互模式 -t:分配伪终端 /bin/bash:待执行命令,进入bash shell
$ exit                              #出容器
```

### 查看容器日志

```shell
$ docker logs <容器名>
```

### 删除容器镜像

如果有基于镜像创建的容器存在，需先停止、删除容器实例后再删除容器镜像

```shell
$ docker stop <容器名>
$ docker rm <容器名>
$ docker rmi <镜像名>
```

## 构建Docker容器镜像

使用Ubuntu为基础镜像，制作包含python基础环境以及Nginx服务器的新镜像

1. 撰写Dockerfile文件
2. 使用`docker build`命令基于Dockerfile创建镜像

### 编写Dockerfile文件

Dockerfile文件放在项目的根目录下

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

### 基于Dockerfile创建容器镜像

```shell
$ docker build -t <自定义镜像名称> .
#-t:镜像名称(REPOSITORY:TAG)  
#.:PATH,执行命令的上下文路径,构造过程中可引用该上下文路径中的任何文件
```

运行镜像，访问80端口，检测Nginx服务。进入容器，检测python。

```shell
$ docker run -d --name npu -p 80:80 npu
$ curl <私有IP>:80

$ docker exec -it npu /bin/bash
#进入容器

$ python3
#检测python
```

## ACR镜像管理与应用

实际应用时需配置持续集成、持续部署流水线，在⌈构建镜像⌋阶段将代码打包到基础镜像后推送至指定镜像仓库(Registry),在⌈服务部署⌋阶段从镜像仓库拉取镜像并部署在ECS服务器或集群。

### 推送容器镜像至ACR

查询仓库URL

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

登录阿里云ACR，并推送镜像

```shell
$ docker login --username=<用户名> registry.<region>.aliyuncs.com
$ docker push registry.<region>.aliyuncs.com/<namespace>/npu
```

### 拉取容器镜像至ECS

1. 需先删除本地相关容器以及镜像，以免干扰

2. 拉取并创建容器

   ```shell
   $ docker pull registry.<region>.aliyuncs.com/<namespace>/npu
   $ docker run -d --name npu -p 80:80 registry.<region>.aliyuncs.com/<namespace>/npu
   $ curl <ECS公网IP>
   ```



注：`镜像名后:TAG`默认TAG为`latest`,可省略`:TAG`

## ECS

修改docker.service文件

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

配置生效

> systemctl daemon-reload

重新启动Docker服务

> systemctl stop docker
>
> systemctl start docker

## 开启2375端口

```
firewall-cmd --zone=public --add-port=2375/tcp --permanent
firewall-cmd --reload
```

sudo systemctl start docker

docker exec -it a45500f9135c /bin/bash #进入容器

docker run -d -p 8081:8080 tsadmin:1.0

http://47.109.141.56:8081/tsAdmin-1.0-SNAPSHOT/org/login



mvn install:install-file -Dfile=D:/A_Jac0912/Java_projects/tsAdmin/src/main/webapp/WEB-INF/lib/aliyun-java-sdk-dysmsapi-1.0.0.jar \
    -DgroupId=com.aliyun \
    -DartifactId=aliyun-java-sdk-dysmsapi \
    -Dversion=1.0.0 \
    -Dpackaging=jar
```shell
sudo rabbitmqctl status
service rabbitmq-server start # 启动 
service rabbitmq-server stop # 停止 
service rabbitmq-server restart # 重启

#拉取镜像 
docker pull rabbitmq:management 
# 运行容器 
docker run -d --hostname rabbitmq --name rabbitmq --network abc -e RABBITMQ_DEFAULT_USER=admin -e RABBITMQ_DEFAULT_PASS=admin -e RABBITMQ_DEFAULT_VHOST=my_vhost -p 15672:15672 -p 5672:5672 rabbitmq:management
```
# docker rabbitmq
docker安装rabbitmq

## 拉取镜像(最新版本)
```shell
docker pull rabbitmq:management
```
## 启动rabbitmq
```shell
docker run -d --hostname my-rabbit --name rabbitmq -e RABBITMQ_DEFAULT_USER=admin -e RABBITMQ_DEFAULT_PASS=admin -p 15672:15672 -p 5672:5672 -p 25672:25672 -p 61613:61613 -p 1883:1883 rabbitmq:management
```

# docker redis

docker安装redis

## 拉取镜像(最新版本)

```shell
docker pull redis
```

## 启动redis

```shell
docker run --name redis -p 6379:6379 -d redis redis-server
```

# docker mysql
docker安装mysql

## 拉取镜像(5.7版本)
```shell
docker pull mysql:5.7
```
## 启动mysql
```shell
docker run -p 3306:3306 --name mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.7 --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci
```

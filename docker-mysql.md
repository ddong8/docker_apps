# docker mysql
docker安装mysql

## 拉取镜像(5.7版本)
```shell
docker pull mysql:5.7
```
## 启动mysql
```shell
docker run -p 3306:3306 --name mysql -v $PWD/conf:/etc/mysql/conf.d -v $PWD/logs:/logs -v $PWD/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.7
```
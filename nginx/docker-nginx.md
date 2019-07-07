# docker nginx
docker安装nginx

## 创建webserver文件夹并cd到指定路径

1.创建webserver文件夹

``` bash
[root@iZwz93zgcarazww3ajhaonZ ~]# mkdir -p /usr/local/webserver
```

2.cd到webserver文件夹

``` bash
[root@iZwz93zgcarazww3ajhaonZ ~]# cd /usr/local/webserver
```

## 拉取镜像(最新版本)

``` bash
[root@iZwz93zgcarazww3ajhaonZ webserver]# docker pull nginx
```

使用nginx默认的配置来启动一个nginx容器实例：

``` bash
[root@iZwz93zgcarazww3ajhaonZ webserver]# $ docker run --name nginx -p 80:80 -p 443:443 -d nginx
```

## 部署nginx

1.创建nginx运行使用的用户 www:

``` bash
[root@iZwz93zgcarazww3ajhaonZ webserver]# /usr/sbin/groupadd www 
[root@iZwz93zgcarazww3ajhaonZ webserver]# /usr/sbin/useradd -g www www
```

2.创建目录nginx，用于存放网页，日志，配置文件

``` bash
[root@iZwz93zgcarazww3ajhaonZ webserver]# mkdir -p ~/nginx/www ~/nginx/logs ~/nginx/conf
```

3.创建证书文件夹

``` bash
[root@iZwz93zgcarazww3ajhaonZ webserver]# mkdir -p ~/nginx/conf/cert
```

4.上传证书文件，用以支持https

最终目录结构
``` bash
nginx
|__ ...
|__ www
|__ logs
|__ conf
    |  
    |__ nginx.conf
    |
    |__ cert
            |__ a.key
            |__ a.pem
```

5.`docker ps`查找容器ID

``` bash
[root@iZwz93zgcarazww3ajhaonZ webserver]# docker ps
```

6.将nginx默认配置文件拷贝到本地当前目录下的conf目录

``` bash
[root@iZwz93zgcarazww3ajhaonZ webserver]# docker cp 容器ID:/etc/nginx/nginx.conf ~/nginx/conf
```

7.将nginx.conf内容替换成[nginx.conf](./nginx.conf)

- [nginx.conf](./nginx.conf)

8.部署命令

``` bash
[root@iZwz93zgcarazww3ajhaonZ webserver]# docker run -d -p 80:80 -p 443:443 --name nginx -v ~/nginx/www:/usr/share/nginx/html -v ~/nginx/conf/nginx.conf:/etc/nginx/nginx.conf -v ~/nginx/logs:/var/log/nginx nginx
```
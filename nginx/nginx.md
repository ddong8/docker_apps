目录

<!-- TOC -->

- [Nginx](#nginx)
    - [安装编译工具及库文件](#安装编译工具及库文件)
    - [首先要安装PCRE](#首先要安装pcre)
    - [安装nginx](#安装nginx)
    - [nginx配置](#nginx配置)
    - [启动nginx](#启动nginx)
    - [nginx其它命令](#nginx其它命令)

<!-- /TOC -->

# Nginx
CentOS部署nginx

## 安装编译工具及库文件

``` bash
yum -y install make zlib zlib-devel gcc-c++ libtool  openssl openssl-devel
```

## 首先要安装PCRE
PCRE作用是让nginx支持Rewrite功能。

1.下载PCRE安装包，下载地址：[http://downloads.sourceforge.net/project/pcre/pcre/8.35/pcre-8.35.tar.gz](http://downloads.sourceforge.net/project/pcre/pcre/8.35/pcre-8.35.tar.gz)

``` bash
[root@iZwz93zgcarazww3ajhaonZ ~]# cd /usr/local/src/
[root@iZwz93zgcarazww3ajhaonZ src]# wget http://downloads.sourceforge.net/project/pcre/pcre/8.35/pcre-8.35.tar.gz
```

2.解压安装包

``` bash
[root@iZwz93zgcarazww3ajhaonZ src]# tar zxvf pcre-8.35.tar.gz
```

3.进入安装包目录

``` bash
[root@iZwz93zgcarazww3ajhaonZ src]# cd pcre-8.35
```

4.编译安装

``` bash
[root@iZwz93zgcarazww3ajhaonZ pcre-8.35]# ./configure
[root@iZwz93zgcarazww3ajhaonZ pcre-8.35]# make && make install
```

5.查看PCRE版本

``` bash
[root@iZwz93zgcarazww3ajhaonZ pcre-8.35]# pcre-config --version
```

## 安装nginx

1.下载nginx，下载地址：[http://nginx.org/download/nginx-1.16.0.tar.gz](http://nginx.org/download/nginx-1.16.0.tar.gz)

``` bash
[root@iZwz93zgcarazww3ajhaonZ pcre-8.35]# cd /usr/local/src/
[root@iZwz93zgcarazww3ajhaonZ src]# wget http://nginx.org/download/nginx-1.16.0.tar.gz
```

2.解压安装包

``` bash
[root@iZwz93zgcarazww3ajhaonZ src]# tar zxvf nginx-1.16.0.tar.gz
```

3.进入安装包目录

``` bash
[root@iZwz93zgcarazww3ajhaonZ src]# cd nginx-1.16.0
```

4.编译安装

``` bash
[root@iZwz93zgcarazww3ajhaonZ nginx-1.16.0]# ./configure --prefix=/usr/local/webserver/nginx --with-http_stub_status_module --with-http_ssl_module --with-pcre=/usr/local/src/pcre-8.35
[root@iZwz93zgcarazww3ajhaonZ nginx-1.16.0]# make
[root@iZwz93zgcarazww3ajhaonZ nginx-1.16.0]# make install
```

5.查看nginx版本

``` bash
[root@iZwz93zgcarazww3ajhaonZ nginx-1.16.0]# /usr/local/webserver/nginx/sbin/nginx -v
```

到此，nginx安装完成。

## nginx配置

创建nginx运行使用的用户 www:

``` bash
[root@iZwz93zgcarazww3ajhaonZ nginx-1.16.0]# /usr/sbin/groupadd www 
[root@iZwz93zgcarazww3ajhaonZ nginx-1.16.0]# /usr/sbin/useradd -g www www
```

配置nginx.conf

`[root@iZwz93zgcarazww3ajhaonZ nginx-1.16.0]# vim /usr/local/webserver/nginx/conf/nginx.conf`

将/usr/local/webserver/nginx/conf/nginx.conf替换为以下内容

``` conf
user www www;
worker_processes 2; #设置值和CPU核心数一致
error_log /usr/local/webserver/nginx/logs/nginx_error.log crit; #日志位置和日志级别
pid /usr/local/webserver/nginx/nginx.pid;
#Specifies the value for maximum file descriptors that can be opened by this process.
worker_rlimit_nofile 65535;
events
{
  use epoll;
  worker_connections 65535;
}
http
{
  include mime.types;
  default_type application/octet-stream;
  log_format main  '$remote_addr - $remote_user [$time_local] "$request" '
               '$status $body_bytes_sent "$http_referer" '
               '"$http_user_agent" $http_x_forwarded_for';
  
#charset gb2312;
     
  server_names_hash_bucket_size 128;
  client_header_buffer_size 32k;
  large_client_header_buffers 4 32k;
  client_max_body_size 8m;
     
  sendfile on;
  tcp_nopush on;
  keepalive_timeout 60;
  tcp_nodelay on;
  fastcgi_connect_timeout 300;
  fastcgi_send_timeout 300;
  fastcgi_read_timeout 300;
  fastcgi_buffer_size 64k;
  fastcgi_buffers 4 64k;
  fastcgi_busy_buffers_size 128k;
  fastcgi_temp_file_write_size 128k;
  gzip on; 
  gzip_min_length 1k;
  gzip_buffers 4 16k;
  gzip_http_version 1.0;
  gzip_comp_level 2;
  gzip_types text/plain application/x-javascript text/css application/xml;
  gzip_vary on;
 
  #limit_zone crawler $binary_remote_addr 10m;
 #下面是server虚拟主机的配置
 server
  {
    listen 80;#监听端口
    server_name localhost;#域名
    index index.html index.htm index.php;
    root /usr/local/webserver/nginx/html;#站点目录
      location ~ .*\.(php|php5)?$
    {
      #fastcgi_pass unix:/tmp/php-cgi.sock;
      fastcgi_pass 127.0.0.1:9000;
      fastcgi_index index.php;
      include fastcgi.conf;
    }
    location ~ .*\.(gif|jpg|jpeg|png|bmp|swf|ico)$
    {
      expires 30d;
  # access_log off;
    }
    location ~ .*\.(js|css)?$
    {
      expires 15d;
   # access_log off;
    }
    access_log off;
  }

}
```

检查配置文件nginx.conf的正确性命令:
`[root@iZwz93zgcarazww3ajhaonZ nginx-1.16.0]# /usr/local/webserver/nginx/sbin/nginx -t`

## 启动nginx

`[root@iZwz93zgcarazww3ajhaonZ nginx-1.16.0]# /usr/local/webserver/nginx/sbin/nginx`

## nginx其它命令

``` bash
/usr/local/webserver/nginx/sbin/nginx -s reload            # 重新载入配置文件
/usr/local/webserver/nginx/sbin/nginx -s reopen            # 重启 Nginx
/usr/local/webserver/nginx/sbin/nginx -s stop              # 停止 Nginx
```
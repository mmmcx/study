## Nginx

### 一、Nginx 简介

*Nginx* (engine x) 是一个高性能的[HTTP](https://baike.baidu.com/item/HTTP)和[反向代理](https://baike.baidu.com/item/反向代理/7793488)web服务器，同时也提供了IMAP/POP3/SMTP服务。Nginx是由伊戈尔·赛索耶夫为[俄罗斯](https://baike.baidu.com/item/俄罗斯/125568)访问量第二的Rambler.ru站点（俄文：Рамблер）开发的，第一个公开版本0.1.0发布于2004年10月4日。

#### 正向代理

​	在客户端(浏览器)配置代理服务器，通过代理服务器进行互联网访问

#### 反向代理

反向代理，其实客户端对代理是无感知的，因为客户端不需要任何配置就可以访问，我们只

需要将请求发送到反向代理服务器，由反向代理服务器去选择目标服务器获取数据后,在返
回给客户端，此时反向代理服务器和目标服务器对外就是一个服务器，暴露的是代理服务器
地址，隐藏了真实服务器IP地址。

#### 负载均衡

	单个服务器解决不了，我们增加服务器的数量，然后将请求分发到各个服务器上,将原先请求集中到单个服务器上的情况改为将请求分发到多个服务器上,将负载分发到不同的服务器，也就是我们所说的负载均衡
#### 动静分离

为了加快网站的解析速度，可以把动态页面和静态页面由不同的服务器来解析，加快解析速
度。降低原来单个服务器的压力。

### 二、Nginx安装

#### 1.Ubuntu 安装

##### 1.1apt-get 方式

```shell
sudo su root
apt-get install nginx	
```

##### 1.2 下载Nginx包安装

如已经安装了Nginx，先卸载Nginx

```shell
# 彻底卸载Nginx
apt-get --purge autoremove nginx
# 查看Nginx版本号
nginx -v
```

安装依赖包

```shell
apt-get install gcc
apt-get install libpcre3 libpcre3-dev
apt-get install zlib1g zlib1g-dev
# Ubuntu14.04的仓库中没有发现openssl-dev，由下面openssl和libssl-dev替代
#apt-get install openssl openssl-dev
sudo apt-get install openssl 
sudo apt-get install libssl-dev
```

安装nginx

```shell
cd /usr/local
mkdir nginx
cd nginx
wget http://nginx.org/download/nginx-1.13.7.tar.gz
tar -xvf nginx-1.13.7.tar.gz
```

编译Nginx

```shell

# 进入nginx目录
/usr/local/nginx/nginx-1.13.7
# 执行命令
./configure
# 执行make命令
make
# 执行make install命令
make install
```

启动nginx

```shell

#进入nginx启动目录
cd /usr/local/nginx/sbin
# 启动nginx
./nginx
```

#### 2.centos 安装

##### 安装编译工具及库文件

```shell
[root@localhost usr]# yum -y install make zlib zlib-devel gcc-c++ libtool  openssl openssl-devel

# 下载PCRE，PCRE的作用是让Nginx支持rewrite功能

[root@localhost usr]# wget https://sourceforge.net/projects/pcre/files/pcre/8.44/pcre-8.44.tar.gz

# 解压缩PCRE

[root@localhost usr]# tar -zxvf pcre-8.44.tar.gz

# 进入安装包目录

[root@localhost usr]# cd pcre-8.44

# 编译安装

[root@localhost pcre-8.44]# ./configure
[root@localhost pcre-8.44]# make && make install

# 查看PCRE的版本

[root@localhost pcre-8.44]# pcre-config --version

# 下载Nginx

[root@localhost usr]# wget http://nginx.org/download/nginx-1.18.0.tar.gz

# 解压缩Nginx

[root@localhost usr]# tar -zxvf nginx-1.18.0.tar.gz

# 进入安装目录

[root@localhost usr]# cd nginx-1.18.0

# 编译安装

[root@localhost nginx-1.18.0]# ./configure
[root@localhost nginx-1.18.0]# make && make install

# 把Nginx加到环境变量中，就是在/usr/local/bin下加了一个软链接

[root@localhost nginx-1.18.0]# ln -s /usr/local/nginx/sbin/nginx /usr/local/bin

# 查看Nginx版本号

[root@localhost nginx-1.18.0]# nginx -v

```

### 三、Nginx 常用指令

```shell
#启动nginx 
cd /usr/local/nginx/sbin
执行 ./nginx
#停止nginx
./nginx -s stop # 快速关闭，不管有没有正在处理的请求
./nginx -s quit # 相对优雅的方式，在退出前完成已经接收的连接请求
# 重新加载
./nginx -s reload

```

### 四、Nginx配置文件

​	**Nginx配置文件分为三部分：**

​		**1.全局块**

```tex
从配置文件开始到events块之间的内容，主要是设置一些影响nginx服务器整体运行的配置指令，比如worker_processes 1; worker_processes值越大，可以支持的并发处理量也越多
```



​		**2.events块**

```tex
events块涉及的指令主要影响nginx服务器与用户的网络连接，
比如worker_connections 1024; 支持的最大连接数
```



​		**3.http（重要）**

```
nginx 服务器配置中最频繁的部分

http块也可以包括http全局快、server块
```

完整conf.conf文件如下：

```shell

#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {

    accept_mutex on; #解决惊群问题
    multi_accept on; #设置work进程同时可以接收多个请求
    worker_connections  1024; #worker进程最大连接数
    use epoll;#使用epoll驱动
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    server {
        listen       80;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   html;
            index  index.html index.htm;
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}

}

```

### 五、配置实例

#### 5.1 反向代理

> 使用nginx反向代理，根据访问的路径跳转到不同端口的服务中。
> nginx.监听端口为9001，。
> 访问http://127.0.0.1:9001/edu/ 直接跳转到127.0.0.1:8081
> 访问http://127.0.0.1:9001/vod/ 直接跳转到127.0.0.1:8082

启动两个Tomcat 分别是8080，8081

```shell
vim /usr/local/nginx/conf/nginx.conf

#配置如下：
server {
	listen 9001;
	server_name 192.168.5.101
	
	location ~ /edu/{
		proxy_pass http://192.168.5.101:8080
	}
	location ~/vod/ {
		proxy_pass http://192.168.5.101:8081
	}


}



```

> (1) 找到nginx配置文件，进行反向代理配置。
>
> (2) 开放对外访问的端口号9001
>
> (3) 重启nginx服务器，使配置文件生效

> location 说明
>
> 该指令用于匹配URL
> 语法如下:

```shell
location [ = | ~ | ~* | ^~] uri {

}

#
1、=: 用于不含正则表达式的uri前，要求请求字符串与uri严格匹配，如果匹配成功，
	就停止继续向下搜索并立即处理该请求
2、~: 用于表示uri包含正则表达式，并且区分大小写
3、~*: 用于表示uri包含正则表达式，并且不区分大小写
4、^~: 用于不含正则表达式的uri前，要求Nginx服务器找到标识uri和请求字
	符串匹配度最高的location后，立即使用此location处理请求，而不再使用location
	块中的正则uri和请求字符串做匹配
注意: 如果uri包含正则表达式，则必须要有~或者~*标识。

```

#### 5.2负载均衡

```shell
 upstream myserver {
      server 192.168.5.101:8001 weight=5;
      server 192.168.5.101:8002 weight=15;
    }

    server {

     listen   9002;
     server_name 192.168.5.101;

     location / {

       proxy_pass http://myserver;
      }


    }

```

**负载均衡分配策略**

- **1、轮询(默认)**

  每个请求按时间顺序逐一分配到不 同的后端服务器，如果后端服务器down掉，能自动剔除

- **2、weight**
  weight代表权重默认为1,权重越高被分配的客户端越多。
  指定轮询几率，weight和访问比率成正比，用于后端服务器性能不均的情况。例如: 。

- **3、ip hash**

  每个请求按访问ip的hash结果分配, 这样每个访客固定访问一个后端服务器,可以解诀session的问题。例如:

  ```shell
  upstream server pool{
    ip_ hash
    server 192.168.5.21:80
    server 192.168.5.22:80
  }
  ```

- **4、fair (第三方)**
  按后端服务器的响应时间来分配请求，响应时间短的优先分配

  ```shell
  upstream server_pool 
  	server 192.168.5.101:80;
  	server 192.168.5.101:80;
  	fair;
  }
  ```

### 六、Nginx 高可用集群

 #### 每台都需要安装keepalived

```shell
apt-get install keepalived
```

> 使用apt-get 安装keepalived 在/etc/keepalived/目录下不会创建keepalived.conf文件，需自己创建该文件,文件内容如下:

```shell
global_defs {
        notification_email {
          acassen@firewall.loc
          failover@firewall.loc
          sysadmin@firewall.loc
        }
        notification_email_from Alexandre.Cassen@firewall.loc
        smtp_ server 192.168.17.129
        smtp_connect_timeout 30
        router_id LVS_DEVEL     # LVS_DEVEL这字段在/etc/hosts文件中看；通过它访问到主机
}

vrrp_script chk_http_ port {
        script "/usr/local/src/nginx_check.sh"
        interval 2   # (检测脚本执行的间隔)2s
        weight 2  #权重，如果这个脚本检测为真，服务器权重+2
}

vrrp_instance VI_1 {
        state MASTER   # 备份服务器上将MASTER 改为BACKUP
        interface ens33 //网卡名称
        virtual_router_id 51 # 主、备机的virtual_router_id必须相同
        priority 100   #主、备机取不同的优先级，主机值较大，备份机值较小
        advert_int 1    #每隔1s发送一次心跳
        authentication {        # 校验方式， 类型是密码，密码1111
        auth type PASS
        auth pass 1111
    }
        virtual_ipaddress { # 虛拟ip
                192.168.5.50 // VRRP H虛拟ip地址
        }
}
```

**分别启动keepalived** 

```shell
systemctl start keepalived.service
```





在/usr/local/src/ 新建 检测脚本nginx_check.sh

```shell
#! /bin/bash
A=`ps -C nginx -no-header | wc - 1`
if [ $A -eq 0];then
	/usr/local/nginx/sbin/nginx
	sleep 2
	if [`ps -C nginx --no-header| wc -1` -eq 0 ];then
		killall keepalived
	fi
fi

```

> 添加权限

```sh
chmod 777 nginx_check.sh
```


**worker 数和服务器的cpu数相等是最为适合的**
> 连接数:worker_connection
> 发送一个请求，占用了worker的几个连接数？
> 2个或4个

> nginx有一个master ，4个worker，每个worker支持的最大连接数1024，支持的最大并发数是多少？
> - 普通静态访问最大并发数是： worker_connections * work_process/2;
> - 而如果是http作为反向代理来说，最大并发数量是 worker_connections * worker_process/4

### 七、升级nginx版本，且nginx不停机
> 两种解决方案

**1、使用nginx服务信号完成nginx的升级**

> 第一步：将低版本的sbin目录下的nginx进行备份，以便升级失败进行回滚
```shell
cd /usr/local/nginx/sbin
mv nginx nginxold
```
> 第二步：将高版本目录编译后的objs目录下的nginx文件，拷贝到原来/usr/local/nginx/sbin
```shell
cd ~/nginx/core/nginx-版本号/objs
cp nginx /usr/local/nginx/sbin
```
> 第三步：发送信号USR2 给nginx的低版本对应的master进程
```shell
kill -USER2 `more /usr/local/logs/nginx.pid`
```
> 第四步：发送信号QUIT给nginx的低版本对应的master进程
```shell
kill -QUIT `more /usr/local/logs/nginx.pid.oldbin`
```

**2、使用nginx安装目录的make命令完成升级**
> 第一步，对低版本sbin目录下的nginx进行备份
```shell
cd /usr/local/nginx/sbin
mv nginx nginxold
```
> 第二步：将高版本安装目录编译编译后的objs目录下的nginx文件，拷贝到原来/usr/local/nginx/sbin目录下
```shell
cd ~/nginx/core/nginx-高版本号/objs
cp nginx /usr/local/nginx/sbin
```
> 第三步：进入安装目录，执行make upgrade
> 第四步：查看是否更细成功
```shell
./nginx -v
```



















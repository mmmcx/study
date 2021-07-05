### Docker指令

#### 一、简介

> Docker引擎是一个包含以下主要组件的客户端服务器应用程序
>
> 一种服务器，它是一种被称为守护进程并且长时间运行的程序
>
> REST API 用于指定程序可以用来与守护进程通信的接口，并指示它做什么。
>
> 一个有命令行界面（CLI）工具的客户端
>
>  
>
> 一个完整的Docker有以下几个部分组成：
>
> DockerClient客户端
>
> Docker Daemon守护进程
>
> Docker Image镜像
>
> DockerContainer容器 

 

#### 二、Docker 架构

> Docker 使用客户端-服务器 (C/S) 架构模式，使用远程API来管理和创建Docker容器。Docker 容器通过 Docker 镜像来创建。容器与镜像的关系类	似于面向对象编程中的对象与类。 

| Docker | 面向对象 |
| ------ | -------- |
| 容器   | 对象     |
| 镜像   | 类       |

#### 三、安装docker

**1. 使用自动脚本安装docker**

```shell
curl -fsSL get.docker.com -o get-docker.sh

sh get-docker.sh --mirror Aliyun
```

**2.配置镜像加速器**

 ```shell
在/etc/docker/daemon.json 中写入如下内容（如果文件不存在请新建该文件）
 {“registry-mirrors”:[“https://registry.docker-cn.com”]}
 在重新启动服务
 sudo systemctl daemon-reload

 sudo systemctl restart docker 
 ```

#### 四、基本使用

**1.拉取镜像**

```shell
Docker pull imagename
```

**2.运行镜像**

```shell
运行镜像：docker run -it --rm ubuntu:16.04 bash
Docker run -it --rm \
Ubuntu:16.04 \
```

**3.bash参数简介**

```shell
-it 是两个参数，-i：已交互式操作，-t：是终端
--rm:表示退出容器后随即删除容器，为了排障需求，退出的容器并不会立即删除，除非手动docker rm，因此使用--rm可以避免浪费空间。
Bash：放在镜像名后的是命令，这里我们希望有个交互式shell，因此用的是bash。
“\”:表示换行，多条命令执行
```

**4.常用指令操作**

```shell
查看镜像：docker images或者 docker image ls

查看正在运行的容器：docker ps 

查看所有容器：docker ps -a

删除所有停止的容器：docker rm $(docker ps -a -q)

docker删除所有无名称的镜像(***\*虚悬镜像)\****
***\*显示虚悬镜像：docker image ls -f dangling=true\****
docker rmi $(docker images -f “dangling=true” -q)或者docker image prune -a -f

交互的方式进入容器  ：docker exec -it 容器id bash

交互的方式前启动容器 docker run -it tomcat bash
清理overlay2:
docker system prune -a命令清理得更加彻底，可以将没有容器使用Docker镜像都删掉
```

**5.镜像制作**

```powershell
使用dockerfile定制镜像

进入/usr/local目录

创建docker目录：mkdir docker

进入docker目录：cd docker

创建tomcat目录：mkdir tomcat

进入tomcat目录: cd tomcat

创建Dockerfile 文件 :vi Dockerfile

编写Dockerfile 文件：

FROM tomcat WORKDIR cd /usr/local/tomcat/webapps/ROOT/WORKDIR :相当于cd命令 RUN rm -fr *RUN echo "Hello Docker" > /usr/local/tomcat/webapps/ROOT/index.html 



FROM 指定基础镜像，必须是第一条指令

RUN 执行命令


构建镜像：docker build -t myshop .

. 表示上下文路径
```

 

#### 五、数据卷

**实现数据共享**

```shell
docker run -p 8081:8080 --name tomcat1 -d -v /usr/local/docker/tomcat/ROOT/:/usr/local/tomcat/webapps/ROOT tomcat 
```



**Docker以数据共享的方式启动mysql：**

```shell
docker run -p 3306:3306 --name mysql -v /usr/local/docker/mysql/conf:/etc/mysql -v /usr/local/docker/mysql/logs:/var/log/mysql -v /usr/local/docker/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.7.22 
```

 

**启动自制镜像且挂载数据库：**

```shell
docker run -p 8081:8080 --name tomcat1 -d -v /usr/local/docker/tomcat/ROOT/:/usr/local/tomcat/webapps/ROOT tomcat 

-v /usr/local/docker/tomcat/ROOT/:/usr/local/tomcat/webapps/ROOT/冒号左边表示：宿主机的位置，右边表示容器的位置 
```



**查看docker容器的日志; docker logs 容器名 或 docker logs -f 容器名**

#### 六、Docker之compose

**安装compose：**

```shell
curl -L https://github.com/docker/compose/releases/download/1.22.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-composechmod +x /usr/local/bin/docker-compose 

给compose添加一个权限：chmod +x /usr/local/bin/docker-compose 
```

```powershell
启动：docker-compose up

停止并移除容器：docker-compose down

守护态运行：docker-compose up -d

查看日志：docker-compose logs tomcat 或docker-compose logs -f tomcat

注意：docker-compose，命令只能在有docker-compose.yml文件中使用
```

**编写dockercompose.yml**

docker-compose.yml:

```yaml
version: '3'
services: 
  web:
    restart: always
    image: tomcat
    container_name: web
    ports: 
     - 8080:8080
    volumes: 
     - /usr/local/docker/myshop/ROOT:/usr/local/tomcat/webapps/ROOT
  mysql:
   restart: always
   image: mysql:5.7.22
   container_name: mysql
   ports:
    - 3306:3306
   environment:
    TZ: Asia/Shanghai
    MYSQL_ROOT_PASSWORD: 123456
   command:
    --character-set-server=utf8mb4
    --collation-server=utf8mb4_general_ci
    --explicit_defaults_for_timestamp=true
    --lower_case_table_names=1
    --max_allowed_packet=128M
    --sql-mode="STRICT_TRANS_TABLES,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION,NO_ZERO_IN_DATE,ERROR_FOR_DIVISION_BY_ZERO"
   volumes:
    - mysql-data:/var/lib/mysql
volumes:
 mysql-data:
```

##### docker搭建gitlab平台

> 拉取gitlab中文版镜像：docker pull twang2218/gitlab-ce-zh

> 在/usr/local下新建docker文件夹

> 进入到docker目录中，新建gitlab文件夹；

> 进入gitlab中，新建docker-compose.yml文件

> docker-compose.yml文件如下：



```yaml
version: '3'
services:
    gitlab:
      image: 'twang2218/gitlab-ce-zh'
      restart: always
      hostname: '192.168.110.131'
      environment:
        TZ: 'Asia/Shanghai'
        GITLAB_OMNIBUS_CONFIG: |
          external_url 'http://192.168.110.131'
          gitlab_rails['time_zone'] = 'Asia/Shanghai'
          gitlab_rails['gitlab_shell_ssh_port'] = 2222
          unicorn['port'] = 8888
          nginx['listen_port'] = 80
          # 需要配置到 gitlab.rb 中的配置可以在这里配置，每个配置一行，注意缩进。
          # 比如下面的电子邮件的配置：
          # gitlab_rails['smtp_enable'] = true
          # gitlab_rails['smtp_address'] = "smtp.exmail.qq.com"
          # gitlab_rails['smtp_port'] = 465
          # gitlab_rails['smtp_user_name'] = "xxxx@xx.com"
          # gitlab_rails['smtp_password'] = "password"
          # gitlab_rails['smtp_authentication'] = "login"
          # gitlab_rails['smtp_enable_starttls_auto'] = true
          # gitlab_rails['smtp_tls'] = true
          # gitlab_rails['gitlab_email_from'] = 'xxxx@xx.com'
      ports:
        - '80:80'
        - '8443:443'
        - '2222:22'
      volumes:
        - config:/etc/gitlab
        - data:/var/opt/gitlab
        - logs:/var/log/gitlab
volumes:
    config:
    data:
    logs:
```

**启动容器：docker-compose up**

**gitlab 备份指令**
```
docker exec gitlab gitlab-rake gitlab:backup:create
```
**自动备份脚本 gitlab_backup.sh**
```shell
#! /bin/bash
case "$1" in 
    "start")
        docker exec gitlab gitlab-rake gitlab:backup:create
        ;;
esac

```
执行该脚本即可实现备份gitlab数据 执行命令：
```shell
sh gitlab_backup.sh start
```
注意：需要定时器才能实现定时备份

**还原备份**

```shell
gitlab-rake gitlab:backup:restore BACKUP=备份的文件名
```
参考：

[gitlab自动备份并挂载到Windows目录](https://www.cnblogs.com/isyefeng/p/11906925.html)

[gitlab自动备份](https://blog.csdn.net/u014258541/article/details/79317180)

[gitlab自动备份及还原](https://blog.csdn.net/anron/article/details/107426696?utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7Edefault-5.control&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7Edefault-5.control)

**重置gitlab管理员登录名**
```shell
进入容器docker exec -it <容器id> bash
执行gitlab-rails console -e production
user = User.where(username: ‘root’).first
user.password = ‘password’
user.save!
```
##### 构建gitlab-runner，实现持续交付

**1.创建environment目录**

```shell
mkdir /usr/local/docker/runner/environment

```
**2.创建daemon.json文件**
```shell
vim daemon.json
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "registry-mirrors": [
    "https://k7da99jp.mirror.aliyuncs.com/",
    "https://1zcopfh1.mirror.aliyuncs.com",
    "https://registry.docker-cn.com"
  ],
  "storage-driver": "overlay2"
}


```
**3.创建Dockerfile文件**
```shell
vim Dockerfile

FROM gitlab/gitlab-runner:v11.0.2
MAINTAINER cx <cx@qq.com>

# 修改软件源
RUN echo 'deb http://mirrors.aliyun.com/ubuntu/ xenial main restricted universe multiverse' > /etc/apt/sources.list && \
    echo 'deb http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted universe multiverse' >> /etc/apt/sources.list && \
    echo 'deb http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted universe multiverse' >> /etc/apt/sources.list && \
    echo 'deb http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse' >> /etc/apt/sources.list && \
    apt-get update -y && \
    apt-get clean

# 安装 Docker
RUN apt-get -y install apt-transport-https ca-certificates curl software-properties-common && \
    curl -fsSL http://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | apt-key add - && \
    add-apt-repository "deb [arch=amd64] http://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable" && \
    apt-get update -y && \
    apt-get install -y docker-ce
COPY daemon.json /etc/docker/daemon.json

# 安装 Docker Compose
WORKDIR /usr/local/bin
RUN wget  https://github.com/docker/compose/releases/download/1.17.1/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
#  https://raw.githubusercontent.com/topsale/resources/master/docker/docker-compose
RUN chmod +x docker-compose

# 安装 Java
RUN mkdir -p /usr/local/java
WORKDIR /usr/local/java
COPY jdk-8u152-linux-x64.tar.gz /usr/local/java
RUN tar -zxvf jdk-8u152-linux-x64.tar.gz && \
    rm -fr jdk-8u152-linux-x64.tar.gz

# 安装 Maven
RUN mkdir -p /usr/local/maven
WORKDIR /usr/local/maven
RUN wget https://raw.githubusercontent.com/topsale/resources/master/maven/apache-maven-3.5.3-bin.tar.gz
# COPY apache-maven-3.5.3-bin.tar.gz /usr/local/maven
RUN tar -zxvf apache-maven-3.5.3-bin.tar.gz && \
    rm -fr apache-maven-3.5.3-bin.tar.gz
# COPY settings.xml /usr/local/maven/apache-maven-3.5.3/conf/settings.xml

# 配置环境变量
ENV JAVA_HOME /usr/local/java/jdk1.8.0_152
ENV MAVEN_HOME /usr/local/maven/apache-maven-3.5.3
ENV PATH $PATH:$JAVA_HOME/bin:$MAVEN_HOME/bin

WORKDIR /


```
**4.gitliab-runner docker-compose.yml文件如下：**

```shell
version: '3.1'
services:
  gitlab-runner:
    build: environment
    restart: always
    container_name: gitlab-runner
    privileged: true
    volumes:
      - /usr/local/docker/runner/config:/etc/gitlab-runner
      - /var/run/docker.sock:/var/run/docker.sock

```




##### docker-compose 搭建jenkins

```shell
version: '3.7'
services:
    jenkins:
        image: jenkins/jenkins:lts
        container_name: jenkins
        environment:
            - TZ=Asia/Shanghai
        volumes:
            - /usr/local/jenkins/jenkins_home:/var/jenkins_home 
            - /usr/local/jenkins/java/jdk1.8.0_152:/usr/local/java # 将本地Java目录挂载在容器中
            - /usr/local/jenkins/maven/apache-maven-3.6.1:/usr/local/maven  # 将本地maven挂载到容器中，不然在Jenkins配置maven是会报找不到/usr/local/jdk1.8.0_191/ is not a directory #on the Jenkins master (but perhaps it exists on some agents)


            - /var/run/docker.sock:/var/run/docker.sock
            - /usr/bin/docker:/usr/bin/docker
            - /usr/lib/x86_64-linux-gnu/libltdl.so.7:/usr/lib/x86_64-linux-gnu/libltdl.so.7
        ports:
            - "8080:8080"
        expose:
            - "8080"
            - "50000"
        privileged: true
        user: root
        restart: always

```



##### docker搭建nexus服务

> 拉取镜像：docker pull sonatype/nexus3

> 步骤：

> cd /usr/local

> mkdir docker

> cd docker

> mkdir nexus

> cd nexus

> vi docker-compose.yml

> docker-compose.yml:

```yaml
version: '3.1'
services:
  nexus:
    restart: always
    image: sonatype/nexus3
    container_name: nexus
    ports:
     - 8081:8081
    volumes:
     - /usr/local/docker/nexus/data:/nexus-data
```

##### docker搭建registry服务

> cd /usr/local

> mkdir docker

> cd docker

> mkdir registry

> cd registry

> vi docker-compose.yml

```yaml
version: '3.1'
services:
  registry:
    image: registry
    restart: always
    container_name: registry
    ports:
      - 5000:5000
    volumes:
      - /usr/local/docker/registry/data:/var/lib/registry

  frontend:
    image: konradkleine/docker-registry-frontend:v2
    ports:
      - 8080:80
    volumes:
      - ./certs/frontend.crt:/etc/apache2/server.crt:ro
      - ./certs/frontend.key:/etc/apache2/server.key:ro
    environment:
      - ENV_DOCKER_REGISTRY_HOST=192.168.110.133
      - ENV_DOCKER_REGISTRY_PORT=5000

```

 

##### 安装registry客户端

> 进入docker配置目录增加配置

> cd /etc/docker

> vi daemon.json

> 新增如下：

> "insecure-registries":[   "192.168.110.133:5000"  ] 

完整如下：

```shell
{"registry-mirrors": ["https://1vd6gm7z.mirror.aliyuncs.com","https://registry.docker-cn.com"], "insecure-registries":[   "192.168.110.133:5000"  ] } 
```

**重启docker：systemctl restart docker**

**使用 docker info 查看是否配置成功**

如有下图所示则配置成功：

![img](file:///C:\Users\admin\AppData\Local\Temp\ksohtml\wps5A77.tmp.jpg) 

  

将本地的tomcat标记为ip+镜像名：

 

docker tag tomcat 192.168.110.133:5000/tomcat 

 

使用push命令将本地镜像推送的远程

 

docker push 192.168.110.133:5000/tomcat

 

浏览器输入地址查看：

http://192.168.110.133:5000/v2/_catalog http://192.168.110.133:5000/v2/tomcat/tags/list

 

 

 

 



### Linux常用指令

## [#](https://www.funtl.com/zh/service-mesh-kubernetes/安装前的准备.html#修改主机名)修改主机名

在同一局域网中主机名不应该相同，所以我们需要做修改，下列操作步骤为修改 **18.04** 版本的 Hostname，如果是 16.04 或以下版本则直接修改 `/etc/hostname` 里的名称即可

**查看当前 Hostname**

```bash
# 查看当前主机名
hostnamectl
# 显示如下内容
   Static hostname: ubuntu
         Icon name: computer-vm
           Chassis: vm
        Machine ID: 33011e0a95094672b99a198eff07f652
           Boot ID: dc856039f0d24164a9f8a50c506be96d
    Virtualization: vmware
  Operating System: Ubuntu 18.04.2 LTS
            Kernel: Linux 4.15.0-48-generic
      Architecture: x86-64
```

**修改 Hostname**

```bash
# 使用 hostnamectl 命令修改，其中 kubernetes-master 为新的主机名
hostnamectl set-hostname kubernetes-master
```

**修改 cloud.cfg**

如果 `cloud-init package` 安装了，需要修改 `cloud.cfg` 文件。该软件包通常缺省安装用于处理 cloud

```bash
# 如果有该文件
vi /etc/cloud/cloud.cfg

# 该配置默认为 false，修改为 true 即可
preserve_hostname: true
```

**验证**

```bash
root@kubernetes-master:~# hostnamectl
   Static hostname: kubernetes-master
         Icon name: computer-vm
           Chassis: vm
        Machine ID: 33011e0a95094672b99a198eff07f652
           Boot ID: 8c0fd75d08c644abaad3df565e6e4cbd
    Virtualization: vmware
  Operating System: Ubuntu 18.04.2 LTS
            Kernel: Linux 4.15.0-48-generic
      Architecture: x86-64
```
## [#](https://www.funtl.com/zh/service-mesh-kubernetes/配置网络.html#附：配置固定-ip-和-dns)附：配置固定 IP 和 DNS

当关机后再启动虚拟机有时 IP 地址会自动更换，导致之前的配置不可用；配置完 Kubernetes 网络后虚拟机还会出现无法联网的情况，后经研究发现是 DNS 会被自动重写所致，Ubuntu Server 18.04 LTS 版本的 IP 和 DNS 配置也与之前的版本配置大相径庭，故在此说明下如何修改 IP 和 DNS

### [#](https://www.funtl.com/zh/service-mesh-kubernetes/配置网络.html#修改固定-ip)修改固定 IP

编辑 `vi /etc/netplan/50-cloud-init.yaml` 配置文件，注意这里的配置文件名未必和你机器上的相同，请根据实际情况修改。修改内容如下：

```yaml
network:
    ethernets:
        ens33:
          addresses: [192.168.141.134/24]
          gateway4: 192.168.141.2
          nameservers:
            addresses: [192.168.141.2]
    version: 2
```

使配置生效 `netplan apply`

### [#](https://www.funtl.com/zh/service-mesh-kubernetes/配置网络.html#修改-dns)修改 DNS

#### [#](https://www.funtl.com/zh/service-mesh-kubernetes/配置网络.html#方法一)方法一

- 停止 `systemd-resolved` 服务：`systemctl stop systemd-resolved`
- 修改 DNS：`vi /etc/resolv.conf`，将 `nameserver` 修改为如 `114.114.114.114` 可以正常使用的 DNS 地址

#### [#](https://www.funtl.com/zh/service-mesh-kubernetes/配置网络.html#方法二)方法二

```bash
vi /etc/systemd/resolved.conf
```

把 DNS 取消注释，添加 DNS，保存退出，重启即可

![img](https://www.funtl.com/assets1/Lusifer_20190602201826.png)




```shell
Opssh

检查软件是否安装

apt-cache policy openssh-client openssh-server

安装服务端

apt-get install openssh-server

安装客户端

apt-get install openssh-client

启动客户端

eval ssh-agent

启动ssh服务器

/etc/init.d/ssh start  或者ssh server start

重启ssh服务器

/etc/init.d/ssh restart 或者： ssh server restart

查看服务是否正确启动

ps -e|grep ssh

判断是否安装ssh服务，可以通过如下命令进行：

ps -e|grep ssh

输出如下：

root:~$ ps -e|grep ssh

 2151 ?    00:00:00 ssh-agent------对应客户端

 5313 ?    00:00:00 sshd------------对应服务器端

 

操作文件目录

ls  显示文件和目录		ls

ls -l :列出文件的详细信息

ls -la ：列出当前目录所有文件，包含隐藏文件

mkdir 创建目录  		mkdir[-p] dirname  -p:父目录									不存在的情况下先生成父目录

cd 切换目录

touch  生成一个空文件

echo  生成一个带内容的文件

echo abcd>1.txt  创建文件并写入abcd

echo 1234>>1.txt 将1234 追加到文本中

cat  显示文本文件内容

cp 复制文件内容 

rm 删除文件

find 在文件系统中查找指定的文件 

find -name abc.txt

grep 在指定的文本文件中查找指定的字符串

ex:grep efs hello.txt

ln 建立软连接

ln helloUbuntu/hello myhello.txt

helloUbuntu/hello:软连接的位置，

myhello.txt 目标位置

系统管理命令

 stat 显示指定文件的相关信息，比ls命令显示内容更多

 who 显示在线登录用户

 hostname 显示主机名字

 uname 显示系统信息

 top 显示当前系统中耗费资源最多的进程  相当于win中的任务管理器

 ps 显示瞬间的进程状态

 du 显示指定的文件（目录）已使用的磁盘空间的总量

 df 显示文件系统磁盘空间的使用情况

 free 显示当前内存和交换空间的使用情况

 ifconfig 显示网络接口信息

 ping 测试网络的连痛性

 netstat 显示网络状态属性

 clear 清屏

 kill 杀死一个进程 kill -9 彻底杀死一个进程

重启：

reboot

shutdown -r now

关机：

shutdown -h now

tar tar[-cxzjvtf] 压缩打包文档的名称，欲打包目录

-c 建立一个归档文件的参数指令

-x解开一个归档文件的参数指令

-z 是否需要用gzip压缩

-j 是否需要用bzip2压缩

-v 压缩的过程中显示文件

-f 使用档名，在f之后要立即接档名

-tf 查看归档文件里面的文件

例子：

 压缩文件夹： tar -zcvf test.tar.gz test \

 解压文件夹： tar -zxvf test.tar.gz

 

vim

命令 

 

:q 直接退出vi

:wq 保存后退出，并可以新建文件

:q! 强制退出

:w file 将当前内容保存成某个文件

:set number 在编辑文件显示行号
：set nonumber 在编辑文件不显示行号

查看系统版本

 lsb_release -a

更换Ubuntu数据源为阿里云：

1.进入 cd /ect/apt 目录

2.sudo vim sources.list

3.按d删除以前的，将阿里云的地址拷贝进去

4.保存退出

5.执行 sudo apt-get update命令 不然不会生效

 

设置root用户密码：

sudo passwd root

设置允许远程登录root

vi /etc/ssh/sshd_config

切换用户： su cx

更改操作权限：

chmod [who] [+|-|] [mode] 文件名

+表示添加某个权限

-表示删除某个权限

例子：chmod +x shell.sh

x:表示可执行的权限

给shell.sh文件添加一个可执行的权限

 

 

安装java

解压jdk安装包

创建java文件夹（mkdir /usr/local/java）

将文件移动到java目录下

配置环境变量：

 配置系统环境变量：

vi /etc/environment

添加如下语句

export JAVA_HOME=/usr/local/java/jdk1.8.0_152

export JRE_HOME=/usr/local/java/jdk1.8.0_152/jre

​    export CLASSPATH=$CLASSPATH:$JAVA_HOME/lib:$JAVA_HOME/jre/lib

 配置用户环境变量

  vi /etc/profile

export JAVA_HOME=/usr/local/java/jdk1.8.0_152

export JRE_HOME=/usr/local/java/jdk1.8.0_152/jre

​    export CLASSPATH=$CLASSPATH:$JAVA_HOME/lib:$JAVA_HOME/jre/lib

​    export PATH=$JAVA_HOME/bin:$JAVA_HOME/jre/bin:$PATH:$HOME/bin

执行生效命令：

 source /etc/profile

mysql

 配置远程访问

 修改配置文件

  vi /etc/mysql/mysql.conf.d/mysqld.cnf

 将bind-address = 127.0.0.1 改为 0.0.0.0

重启mysql

 service mysql restart

登录mysql 

 mysql -uroot -p

 

授权root用户允许所有人连接

 grant all privileges on *.* to ‘root’@’%’ identified by ‘密码’ 
 
 # 查询慢查询次数
show status like 'slow_queries'
# 查看慢查询
show variables like 'long_query_time'
# 设置慢查询时间
set long_query_time =1
# 开启慢日志
set CLOBAL slow_query_log=on
# 模糊查找query相关设置
show variables like '%query%'
# 用于查看存在分区的表的执行计划
explan partitions [具体sql语句]
# 查看当前服务器配置的最大连接数
show variables like 'max_connections'
# 设置最大连接数
set GLOBAL max_connections=10
# 查看当前已使用最大连接数
show GLOBAL status like '%max_used_connections%'

 
```



#### linux 查看文件使用存储情况

```powershell
du命令用来查看目录或文件所占用磁盘空间的大小。常用选项组合为：du -sh

du常用的选项：
　　-h：以人类可读的方式显示
　　-a：显示目录占用的磁盘空间大小，还要显示其下目录和文件占用磁盘空间的大小
　　-s：显示目录占用的磁盘空间大小，不要显示其下子目录和文件占用的磁盘空间大小
　　-c：显示几个目录或文件占用的磁盘空间大小，还要统计它们的总和
　　--apparent-size：显示目录或文件自身的大小
　　-l ：统计硬链接占用磁盘空间的大小
　　-L：统计符号链接所指向的文件占用的磁盘空间大小　　
du -sh : 查看当前目录总共占的容量。而不单独列出各子项占用的容量 

du -lh --max-depth=1 : 查看当前目录下一级子文件和子目录占用的磁盘容量。

du -sh * | sort -n 统计当前文件夹(目录)大小，并按文件大小排序
du -sk filename 查看指定文件大小
lsof -n | grep filename 查看没有删除了没有释放磁盘空间
```



 ### 附 win系统端口被占用解决方法

```shell
netstat -aon|findstr "49157"
tasklist|findstr "2720"

taskkill /f /t /im Tencentdl.exe
```
> 查看某个进程是否存在
```shell
ps -ef | grep 进程名 | grep -v "grep" | wc -l
```
> ps -ef 指令用来查询所有进程，grep通过管道来过滤。grep -v 是反向查询的意思，grep -v grep的作用是除去包含grep的项
> wc -l 标示统计查询到的结果数量
```shell
kill -9 `ps -ef|grep cpu|grep -v grep|awk '{print $2}'`
```
> ps -ef|grep cpu会把grep cpu的进程也统计进来，因此用ps -ef|grep cpu|grep -v grep去除grep进程

> 最后，只包含cpu关键字的进程筛选结果作为输入给awk '{print $2}'，这个部分的作用是提取输入的第二列，而第二列正是进程的PID
> #查看消费者列表
kafka-consumer-groups.sh --bootstrap-server 172.16.13.1:9092 --list

#产看消费进度（CURRENT-OFFSET）、消息进度（LOG-END-OFFSET）、落后量（LAG）
kafka-consumer-groups.sh --bootstrap-server 172.16.12.1:9092 --describe --group group1



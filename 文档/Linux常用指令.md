

### Linux常用指令

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
```



 ### 附 win系统端口被占用解决方法

```shell
netstat -aon|findstr "49157"
tasklist|findstr "2720"

taskkill /f /t /im Tencentdl.exe
```




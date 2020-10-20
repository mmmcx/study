## 安装influxdb

influxdb是一个时序数据库，非常适合用来记录监控信息。

- 拉取镜像

```
docker pull tutum/influxdb1
```

- 启动镜像

```
docker run -d -p 8083:8083 -p 8086:8086 -e ADMIN_USER="root" -e INFLUXDB_INIT_PWD="root" -e PRE_CREATE_DB="telegraf" --name influxdb tutum/influxdb:latest1
```

开放8083和8086两个端口（8083是influxdb的web管理端，8086是数据传输端口）。用户名root，密码root，初始创建数据库telegraf供telegraf保存数据。

**如果想让数据库只保留1天的数据可以使用RETENTION POLICIES来约束，例如**

```
CREATE RETENTION POLICY "1_day" ON "telegraf" DURATION 1d REPLICATION 1 DEFAULT1
```

## 安装telegraf

telegraf是负责收集docker信息并转发到influxdb的工具，通过简单的配置即可监控docker和宿主机的信息。

- 拉取镜像

```
docker pull telegraf1
```

- 修改telegraf.conf配置，使其支持docker

telegraf.conf文件可以先启动一次telegraf然后通过

```
docker cp telegraf:/etc/telegraf/telegraf.conf ./telegraf1
```

命令把容器内的配置文件拷贝出来再修改。

找到配置文件的# # Read metrics about docker containers然后把下面的内容取消注释

```
[[inputs.docker]]
endpoint = "unix:///var/run/docker.sock"
container_names = []
container_name_include = []
container_name_exclude = []
timeout = "5s"
perdevice = true
total = false
tag_env = ["JAVA_HOME", "HEAP_SIZE"]
docker_label_include = []
docker_label_exclude = []1234567891011
```

然后修改influxdb的ip地址

```
urls = ["http://localhost:8086"]1
```

- 启动telegraf

```
docker run -d --name=telegraf -v /root/telegraf/telegraf.conf:/etc/telegraf/telegraf.conf -v /var/run:/var/run telegraf1
```

通过-v参数，把本地的telegraf.conf放到容器中覆盖默认的配置，同时把/var/run也放入容器内，因为其中有docker.sock这个文件是与docker通信的接口。

## 安装grafana

grafana是一个用于显示influxdb内容的图形化工具。

- 拉取镜像

```
docker pull grafana/grafana  1
```

- 启动grafana

```
docker run -d -p 3000:3000 -e INFLUXDB_HOST=172.17.0.1  -e INFLUXDB_PORT=8086 -e INFLUXDB_NAME=telegraf -e INFLUXDB_USER=root -e INFLUXDB_PASS=root --name grafana  grafana/grafana1
```

这样就启动了一个连接172.17.0.1:8086端口的influxdb的grafana。在-e参数后面填写用户名密码和连接的数据库。

启动后如下图所示：

![这里写图片描述](https://img-blog.csdn.net/20171025180330180?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2lsbDA1MzI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

输入默认的账号密码admin/admin单击log in进入数据库配置页面

![这里写图片描述](https://img-blog.csdn.net/20171025180629841?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2lsbDA1MzI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

输入连接信息后创建图表

拖动一个图表到显示区域

![这里写图片描述](https://img-blog.csdn.net/20171025180821554?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2lsbDA1MzI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

把鼠标放到title上，选择edit，如下配置好要查询的sql

![这里写图片描述](https://img-blog.csdn.net/20171025181156999?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2lsbDA1MzI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

这样一个查询id为b3aa7e00ff1507110fe37b7e12c86709ff7a34bc3ef2287584f55c0d68ac2407的容器的cpu使用率的图表就做好了。
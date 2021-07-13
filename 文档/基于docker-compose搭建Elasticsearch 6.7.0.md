## 基于docker-compose搭建Elasticsearch 6.7.0

>  1.创建目录

```shell
mkdir /usr/local/docker/es 
```

> 2.进入目录

```shell
cd /usr/local/docker/es
```

> 3.创建config ,data1,data2,data2 目录

```shell
mkdir /usr/local/docker/es/config

mkdir /usr/local/docker/es/data1 /usr/local/docker/es/data2 /usr/local/docker/es/data3

# 对data1，data2，data3目录添加权限
chmod 777 /usr/local/docker/es/data1 /usr/local/docker/es/data2 /usr/local/docker/es/data3
```

> 4.在/usr/local/docker/es创建docker-compose.yml文件

```yaml
version: '2.2'
services:
  es-node1:
    image: elasticsearch:6.7.2
    container_name: elasticsearch1
    restart: always
    environment:
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    volumes:
      - ./config/es1.yml:/usr/share/elasticsearch/config/elasticsearch.yml
      - ./data1:/usr/share/elasticsearch/data
      - ./plugins:/usr/share/elasticsearch/plugins

    ports:
      - 9200:9200
      - 9300:9300
    ulimits:
      memlock:
        soft: -1
        hard: -1
    networks:
      - es_network

  es-node2:
    image: elasticsearch:6.7.2
    container_name: elasticsearch2
    restart: always
    environment:
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    volumes:
      - ./config/es2.yml:/usr/share/elasticsearch/config/elasticsearch.yml
      - ./data2:/usr/share/elasticsearch/data
      - ./plugins:/usr/share/elasticsearch/plugins

    ports:
      - 9201:9201
      - 9301:9301
    ulimits:
      memlock:
        soft: -1
        hard: -1
    networks:
      - es_network

  es-node3:
    image: elasticsearch:6.7.2
    container_name: elasticsearch3
    restart: always
    environment:
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - ./config/es3.yml:/usr/share/elasticsearch/config/elasticsearch.yml
      - ./data3:/usr/share/elasticsearch/data
      - ./plugins:/usr/share/elasticsearch/plugins

    ports:
      - 9202:9202
      - 9302:9302
    networks:
      - es_network
  es-head:
    image: mobz/elasticsearch-head:5
    container_name: elasticsearch-head
    restart: always
    ports:
      - 9100:9100
  cerebro:
    image: lmenezes/cerebro:0.8.3
    container_name: cerebro
    ports:
        - 9000:9000
    command:
        - -Dhosts.0.host=http://es-node1:9200
    networks:
        - es_network
  kibana:
    image: kibana:6.7.2 #docker.elastic.co/kibana/kibana:6.7.2
    container_name: kibana
    environment:
      - I18N_LOCALE=zh-CN
      - XPACK_GRAPH_ENABLED=true
      - TIMELION_ENABLED=true
      - XPACK_MONITORING_COLLECTION_ENABLED="true"
    ports:
      - "5601:5601"
    links:
        - es-node1:elasticsearch
    depends_on:
        - es-node1
    networks:
        - es_network
networks:
  es_network:
     driver: bridge

```



> 4.在config目录下创建es1.yml,es2.yml,es3.yml文件

```yaml
#es1.yml
#集群名称，节点之间要保持一致
cluster.name: elasticsearch-cluster
# 节点名称，集群内要唯一
node.name: es-node1
#表示这个节点是否可以充当主节点
node.master: true
# 是否充当数据节点
node.data: true

network.bind_host: 0.0.0.0
#IP地址
network.publish_host: 192.168.5.102
# http端口
http.port: 9200
# tcp 监听端口
transport.tcp.port: 9300
http.cors.enabled: true
http.cors.allow-origin: "*"

# 所有主从节点ip:port
discovery.zen.ping.unicast.hosts: ["es-node1:9300","es-node2:9301","es-node3:9302"]
# 这个参数决定了在选主过程中需要 有多少个节点通信  预防脑裂 N/2+1 N为主节点个数 ，即有资格成为主节点的节点个数
discovery.zen.minimum_master_nodes: 1


## es2.yml
#集群名称，节点之间要保持一致
cluster.name: elasticsearch-cluster
# 节点名称，集群内要唯一
node.name: es-node2
node.master: true
node.data: true

network.bind_host: 0.0.0.0
#IP地址
network.publish_host: 192.168.5.102
# http端口
http.port: 9201
# tcp 监听端口
transport.tcp.port: 9301
http.cors.enabled: true
http.cors.allow-origin: "*"

discovery.zen.ping.unicast.hosts: ["es-node1:9300","es-node2:9301","es-node3:9302"]
discovery.zen.minimum_master_nodes: 1

## es3.yml
#集群名称，节点之间要保持一致
cluster.name: elasticsearch-cluster
# 节点名称，集群内要唯一
node.name: es-node3
node.master: false
node.data: true

network.bind_host: 0.0.0.0
#IP地址
network.publish_host: 192.168.5.102
# http端口
http.port: 9202
# tcp 监听端口
transport.tcp.port: 9302
http.cors.enabled: true
http.cors.allow-origin: "*"

discovery.zen.ping.unicast.hosts: ["es-node1:9300","es-node2:9301","es-node3:9302"]
discovery.zen.minimum_master_nodes: 1
```

> 安装ik中文分词器插件
```shell
# 创建plugins目录
mkdir /usr/local/docker/es/plugins
#创建ik目录
mkdir /usr/local/docker/es/plugins/ik


#使用wget下载ik插件
cd /usr/local/docker/es/plugins/ik
wget https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v6.7.2/elasticsearch-analysis-ik-6.7.2.zip
# 解压文件
unzip xxx.zip
#修改docker-compose.yml文件，将ik目录挂载进容器，修改后的见上面的yml文件

```
> 访问 http://192.168.5.102:9200/_cat/plugins 验证插件
> 出现下面，则表示安装成功

```yaml
es-node1 analysis-ik 6.7.2
es-node3 analysis-ik 6.7.2
es-node2 analysis-ik 6.7.2
```
> 测试中文分词器
> 使用postman get 请求 http://192.168.5.102:9200/_analyze
```yaml
# ik_max_word : 会将文本做最细粒度的拆分
# ik_smart: 会将文本做最粗粒度的拆分

# 请求参数：JSON格式
{
    "text":"测试单词",
    "analyzer":"ik_max_word" 
}
# 结果：
{
    "tokens": [
        {
            "token": "测试",
            "start_offset": 0,
            "end_offset": 2,
            "type": "CN_WORD",
            "position": 0
        },
        {
            "token": "单词",
            "start_offset": 2,
            "end_offset": 4,
            "type": "CN_WORD",
            "position": 1
        }
    ]
}


```




## 备注

```sh
jvm内存不够：
需要在运行镜像的时候使用一下命令
-e ES_JAVA_OPTS="-Xms512m -Xmx512m"
设置-e ES_JAVA_OPTS="-Xms256m -Xmx256m" 是因为/etc/elasticsearch/jvm.options 默认jvm最大最小内存是2G

JVM线程数限制数量：

vim /etc/sysctl.conf
vm.max_map_count=262144
sysctl -p

```


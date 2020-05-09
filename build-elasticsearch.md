#### 安装 elasticsearch

##### 下载 elasticsearch 镜像

```shell
$ docker pull elasticsearch:7.4.2
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED         SIZE
elasticsearch   7.4.2               b1179d41a7b4        6 months ago        855MB
```

##### 创建网络

如果需要安装kibana等其他，需要创建一个网络，名字任意取，让他们在同一个网络，使得es和kibana通信。

```shell
$ docker network create esnet
```

##### 运行 elasticsearch 容器

```shell
$ docker run --name es \
	-p 9200:9200 \
    -p 9300:9300 \
    --network esnet \
    -e "discovery.type=single-node" \
    -d elasticsearch:7.4.2
```

- `-p 9200:9200`：把容器内的9200端口映射到宿主机9200端口
- `--name es`：给容器起个名字
- `--network esnet`：用于多个服务通信与隔离，例如用kibana连接elasticsearch就需要他们在同一个网络下

##### 测试 elasticsearch 连接

```shell
$ curl 'http://192.168.31.130:9200/'
$ curl 'http://127.0.0.1:9200/'
{
  "name" : "c00d214f6774",
  "cluster_name" : "docker-cluster",
  "cluster_uuid" : "JIR7Cpp8TzipW6QtEUnSig",
  "version" : {
    "number" : "7.4.2",
    "build_flavor" : "default",
    "build_type" : "docker",
    "build_hash" : "2f90bbf7b93631e52bafb59b3b049cb44ec25e96",
    "build_date" : "2019-10-28T20:40:44.881551Z",
    "build_snapshot" : false,
    "lucene_version" : "8.2.0",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
```

#### 安装 kibana

##### 下载 kibana 镜像

```shell
$ docker pull kibana:7.4.2
```

##### 配置文件

kibana.yml

```yml
# Default Kibana configuration for docker target
server.name: kibana
server.host: "0"
elasticsearch.hosts: [ "http://192.168.31.130:9200" ]
xpack.monitoring.ui.container.elasticsearch.enabled: true
```

##### 运行容器

```shell
$ docker run --restart=always \
	--log-driver json-file \
	--log-opt max-size=100m \
	--log-opt max-file=2 \
	--name xinyar-kibana \
	-p 5601:5601 \
	-v `pwd`/kibana.yml:/usr/share/kibana/config/kibana.yml \
	-d kibana:7.4.2
```

##### 测试 kibana

> http://192.168.31.130:5601/


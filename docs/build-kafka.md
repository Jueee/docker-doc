#### kafka 镜像比较

###### [spotify/kafka](https://hub.docker.com/r/spotify/kafka/)

来自 spotify 的 kafka 镜像同时包含了 kafka 与 zookeeper，因此基本上可以“随装随用”。

但已经较长时间没有维护，Kafka 版本仍然停留在0.10。对需要使用新版本的已经不适合了。

###### [landoop/fast-data-dev](https://hub.docker.com/r/landoop/fast-data-dev/)

提供了一整套包括Kafka，ZooKeeper，Schema Registry,，Kafka-Connect等在内的多种开发工具和Web UI监视系统。

尤其是对于Kafka Connect的支持，包含了MongoDB，ElasticSearch，Twitter等超过20种Connector，并且提供了通过REST API提交Connector配置的 Web UI。

###### [wurstmeister/kafka](https://hub.docker.com/r/wurstmeister/kafka/)

维护较为频繁的一个Kafka镜像。只包含了Kafka，因此需要另行提供ZooKeeper，推荐使用同一作者提交的[wurstmeister/zookeeper](https://link.jianshu.com?t=https%3A%2F%2Fhub.docker.com%2Fr%2Fwurstmeister%2Fzookeeper%2F)。
 现在已经提供较新的 2.5.0 版本。

#### wurstmeister/kafka

##### 下载 kafka 镜像

```shell
$ docker pull wurstmeister/kafka
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
kafka               latest              f9b990972689        5 days ago          104MB
$ docker inspect wurstmeister/kafka|grep VERSION
                "JAVA_VERSION=8u212",
                "KAFKA_VERSION=2.5.0",
                "SCALA_VERSION=2.12",
```

##### 运行 kafka 容器

```shell
$ docker run --name kafka \
	--restart=always \
	-p 9092:9092 \
	-e KAFKA_BROKER_ID=0 \
	-e "KAFKA_ZOOKEEPER_CONNECT=172.17.0.2:2181/kafka" \
	-e "KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://localhost:9092" \
	-e "KAFKA_LISTENERS=PLAINTEXT://localhost:9092" \
	-v /etc/localtime:/etc/localtime \
	-d wurstmeister/kafka
```

- `-p 9092:9092`:把容器内的9092端口映射到宿主机9092端口
- `-e KAFKA_BROKER_ID=0`  在kafka集群中，每个kafka都有一个BROKER_ID来区分自己
- `-e KAFKA_ZOOKEEPER_CONNECT=172.20.10.10:2181/kafka` 配置zookeeper管理kafka的路径
- ``-e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://172.17.0.2:9092``  把kafka的地址端口注册给zookeeper，如果是远程访问要改成外网IP,类如Java程序访问出现无法连接。
- `-e KAFKA_LISTENERS=PLAINTEXT://0.0.0.0:9092` 配置kafka的监听端口
- `-v /etc/localtime:/etc/localtime` 容器时间同步虚拟机的时间

#### landoop/fast-data-dev

##### 下载 kafka 镜像

```shell
$ docker pull landoop/fast-data-dev
$ docker images
REPOSITORY             TAG                 IMAGE ID            CREATED             SIZE
landoop/fast-data-dev  latest              c06607a7dc0e        2 months ago        1.13GB
```

##### 运行 kafka 容器

```shell
$ docker run --rm -it --name kafka \
	-p 2181:2181 \
	-p 8081:8081 \
	-p 8082:8082 \
	-p 8083:8083 \
	-p 9092:9092 \
	-p 3030:3030 \
	-e ADV_HOST=127.0.0.1 \
	-d landoop/fast-data-dev
```

- `-p 9092:9092`: Kafka Broker
- `-p 8081:8081`: Schema Registry
- `-p 8082:8082`: Kafka REST Proxy
- `-p 8083:8083`: Kafka Connect Distributed
- `-p 2181:2181`: ZooKeeper
- `-p 3030:3030`: Web Server

数据目录：`/data`

#### 测试 kafka 连接

测试端口

```shell
$ telnet 127.0.0.1 9092
```

发送消息

```shell
$ docker exec -it kafka kafka-console-producer.sh --broker-list localhost:9092 --topic test
```

接收消息

```shell
$ docker exec -it kafka kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --from-beginning
```

#### 查看日志

```shell
$ docker logs -f kafka
```


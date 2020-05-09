##### 下载 zookeeper 镜像

```shell
$ docker pull zookeeper
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
zookeeper           latest              a08774a5fe84        2 weeks ago         252MB
```

##### 运行 zookeeper 容器

```shell
$ docker run --name zookeeper -p 2181:2181 -d zookeeper
```

##### 测试 zookeeper 连接

测试端口
```
$ telnet 127.0.0.1 2181
```

进入终端
```shell
$ docker exec -ti zookeeper zkCli.sh
```

##### 查看日志

```shell
$ docker logs -f zookeeper
```


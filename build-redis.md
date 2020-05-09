##### 下载 redis 镜像

```shell
$ docker pull redis
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
redis               latest              f9b990972689        5 days ago          104MB
```

##### 目录配置

基于目录

```
/home/dir/application/docker/redis
```

数据目录

```
mkdir data
```

**下载reids配置文件**

```shell
$ wget https://raw.githubusercontent.com/antirez/redis/6.0.1/redis.conf -O redis.conf
```

修改配置：

- `appendonly yes`：redis启动后数据持久化
- `# bind 127.0.0.1`  并且 `protected-mode  no` ：关闭保护模式，开启远程访问。

##### 运行 redis 容器

```shell
$ docker run --privileged=true \
	-p 6379:6379 \
	-v `pwd`/data:/data \
	-v `pwd`/redis.conf:/etc/redis/redis.conf  \
	--name myredis \
	--restart=always \
	-d redis redis-server /etc/redis/redis.conf
```

- `-p 6379:6379`:把容器内的6379端口映射到宿主机6379端口
- ``-v `pwd`/redis.conf:/etc/redis/redis.conf``：把宿主机配置好的 redis.conf 放到容器内的这个位置中
- `-v /root/redis/data:/data`：把 redis 持久化的数据在宿主机内显示，做数据备份
- `redis-server /etc/redis/redis.conf`：这个是关键配置，让 redis 不是无配置启动，而是按照这个redis.conf的配置启动

##### 测试 Redis 连接

```shell
$ docker exec -ti myredis redis-cli -h localhost -p 6379
```

向外部拷贝 Redis 客户端

```shell
$ docker cp ee:/usr/local/bin/redis-cli ./
```

查看容器的 IP 地址

```shell
$ docker inspect myredis|grep IPAddress
            "SecondaryIPAddresses": null,
            "IPAddress": "172.17.0.3",
                    "IPAddress": "172.17.0.3",
```

测试连接（容器内部连接）

```shell
$ ./redis-cli -h 172.17.0.3 -p 6379
```

测试连接（外部连接）

```shell
$ ./redis-cli -h localhost -p 6379
```

##### 查看日志

```shell
$ docker logs -f myredis
```


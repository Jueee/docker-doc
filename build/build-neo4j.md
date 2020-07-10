##### 下载 neo4j 镜像

```shell
$ docker pull neo4j
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
neo4j               latest              f20f8a8d0d50        2 days ago          522MB
```

##### 目录配置

基于数据目录

```
/home/dir/application/docker/neo4j
```

**下载reids配置文件**

```shell
$ wget https://raw.githubusercontent.com/antirez/neo4j/6.0.1/neo4j.conf -O neo4j.conf
```

修改配置：

- `appendonly yes`：neo4j启动后数据持久化
- `# bind 127.0.0.1`  并且 `protected-mode  no` ：关闭保护模式，开启远程访问。

##### 运行 neo4j 容器

```shell
$ docker run \
	--name neo4j \
    -p=7474:7474 \
	-p=7687:7687 \
    -v=`pwd`/neo4j/data:/data \
	-d neo4j
```

- `-p 7474:7474`:把容器内的7474端口映射到宿主机7474端口
- `-v /root/neo4j/data:/data`：把 neo4j 持久化的数据在宿主机内显示，做数据备份

##### 测试 neo4j 连接

###### Neo4j 浏览器管理界面

> http://192.168.31.130:7474/

其中默认密码是neo4j，登录过后会要求重新设置新密码。

###### 使用Cypher shell

```shell
$ docker exec --interactive --tty neo4j bin/cypher-shell
```


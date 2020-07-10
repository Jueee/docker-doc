### 工具介绍

linux下简单好用的工具rinetd，实现端口映射/转发/重定向。

用于有效地将连接从一个 IP 地址/端口组合重定向到另一 IP 地址/端口组合。在操作虚拟服务器、防火墙等时很有用。

Rinetd是单一过程的服务器，它处理任何数量的连接到在配置文件etc/rinetd中指定的地址/端口对。尽管rinetd使用非闭锁I/O运行作为一个单一过程，它可能重定向很多连接而不对这台机器增加额外的负担。

官网地址：[http://www.boutell.com/rinetd](http://www.boutell.com/rinetd)

### 制作rinetd镜像

#### 目录结构

```shell
$ tree
.
├── Dockerfile
├── rinetd.conf
└── sources.list
```

#### sources.list

```shell
deb http://mirrors.163.com/debian/ buster main contrib non-free
# deb-src http://mirrors.163.com/debian/ buster main contrib non-free
deb http://mirrors.163.com/debian/ buster-updates main contrib non-free
# deb-src http://mirrors.163.com/debian/ buster-updates main contrib non-free
deb http://mirrors.163.com/debian/ buster-backports main contrib non-free
# deb-src http://mirrors.163.com/debian/ buster-backports main contrib non-free
deb http://mirrors.163.com/debian-security buster/updates main contrib non-free
# deb-src http://mirrors.163.com/debian-security buster/updates main contrib non-free
```

#### rinetd.conf

```conf
# bindadress    bindport  connectaddress  connectport
${BIND_ADRESS}    ${BIND_PORT}  ${CONNECT_ADDRESS}  ${CONNECT_PORT}

# logging information
logfile /var/log/rinetd.log
```

#### Dockerfile 

```dockerfile
#基础镜像为debian
FROM debian

COPY sources.list /etc/apt/
RUN apt-get update && apt-get install rinetd -y
COPY rinetd.conf /etc/rinetd.conf

CMD ["rinetd", "-f", "-c", "/etc/rinetd.conf"]
```

### 构建镜像

```shell
$ docker build -t rinetd:0.1 .
```


### 查看镜像

```shell
$ docker images | grep rinetd
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
rinetd              0.1                 13636ae703ad        About a minute ago   133MB
```

### 启动容器

```shell
$ docker run --name rinetd \
	-p 3306:3306 \
	-e BIND_ADRESS=XXXX \
	-e BIND_PORT=3306 \
	-e CONNECT_ADDRESS=127.0.0.1 \
	-e CONNECT_PORT=3306 \
	-d rinetd:0.1 
```

- **BIND_ADRESS**：源地址   
- **BIND_PORT**：源端口
- **CONNECT_ADDRESS**：目的地址
- **CONNECT_PORT**：目的端口

### 镜像上传

```shell
$ docker tag rinetd:0.1 yongqiang/rinetd:0.1
$ docker push yongqiang/rinetd:0.1
```

DockerHub

> [https://hub.docker.com/repository/docker/yongqiang/rinetd](https://hub.docker.com/repository/docker/yongqiang/rinetd)


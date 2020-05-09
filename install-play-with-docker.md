## 在本地搭建 play-with-docker

**play-with-docker** 官网：

> https://labs.play-with-docker.com/

Git 地址：

> https://github.com/play-with-docker/play-with-docker

### 进行搭建

```
docker swarm init
git clone https://github.com/play-with-docker/play-with-docker
sudo modprobe xt_ipvs
安装golang环境，版本在go1.7.1+
cd play-with-docker/
go get -v -d -t ./...
docker-compose up -d  或者  docker stack deploy --compose-file docker-compose.yml PWD
```

查看 docker 服务：

```shell
$ docker service ls
ID                  NAME                MODE                REPLICAS            IMAGE               PORTS
p10e2k6oz8au        PWD_haproxy         replicated          0/1                 haproxy:latest      *:80->8080/tcp
hmct19om17qd        PWD_l2              replicated          0/1                 golang:latest       *:443->443/tcp, *:8022->22/tcp, *:8053->53/tcp
qrc5c17p2r99        PWD_pwd             replicated          0/1                 golang:latest
```

停止服务

```
docker stack rm service-name
docker-compose down
```

#### 安装golang环境

安装golang环境，版本在go1.7.1+

下载地址：

> https://golang.org/dl/

下载安装包：

> wget https://dl.google.com/go/go1.14.2.linux-amd64.tar.gz

解压到 /usr/local：

> sudo tar -C /usr/local -xzf go1.14.2.linux-amd64.tar.gz

加入环境变量：

> export PATH=$PATH:/usr/local/go/bin

查看版本：

```shell
$ go version
go version go1.14.2 linux/amd64
```

### 访问地址

```shell
$ curl "http://localhost/"
```




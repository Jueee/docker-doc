## build-java-web

Java web基础镜像内置了`openJDK8` 和 `tomcat7`，各应用镜像继承基础镜像，只需要把应用的 `webroot` 目录复制到容器 `/java-web-dir/webroot` 目录下即可。

#### 下载镜像

```shell
$ docker pull yongqiang/java-web:1.0
```

#### 编写Dockerfile

```dockerfile
FROM yongqiang/java-web:1.0

ENV XMX_CONFIG="1280"\
    XMS_CONFIG="1280"

ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

COPY webroot /java-web-dir/webroot

CMD ["/bin/bash", "-c", "/java-web-dir/tomcat-7-0-52/tomcat start && tail -f /dev/null"]
```

#### 进行镜像制作

```shell
$ docker build -t web-phishing:v1 ./
```

#### 启动容器

直接启动容器

```shell
$ docker run -d -it -p 8192:8080 web-phishing:v1
```

挂载外部配置文件启动
```shell
$ docker run -d -it -p 8192:8080 -v `pwd`/config.properties:/java-web-dir/webroot/classes/config.properties web-phishing:v1
```

#### 查看容器

```shell
$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
bf8f61d98d2b        web-phishing:v1     "/bin/bash -c '/java…"   56 minutes ago      Up 56 minutes       0.0.0.0:8192->8080/tcp   optimistic_noether
```


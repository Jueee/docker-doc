## build-java-app

Java APP基础镜像内置了openJDK8和进程控制脚本，包括启动、关闭、重启等操作。各应用将配置和jar包复制到容器相应目录下即可。

#### 下载镜像

```shell
$ docker pull yongqiang/java-app:1.0
```

#### 编写Dockerfile

```dockerfile
FROM yongqiang/java-app:1.0

ENV XMX_CONFIG="256"\
    XMS_CONFIG="256"\
    RUN_CMD="com.netease.mdas.main.ConsumeMailInfoES"

ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

COPY approot/lib /java-app-dir/approot/lib
COPY approot/conf /java-app-dir/approot/conf

CMD ["/bin/bash", "-c", "/java-app-dir/appCtrl.sh start && tail -f /dev/null"]
```

#### 制作镜像

```shell
$ docker build -t upload-es:v1 ./
```

#### 启动容器

直接启动容器

```shell
$ docker run -d -it upload-es:v1
```

挂载外部配置文件启动

```shell
$ docker run -d -it -v `pwd`/application.properties:/java-app-dir/approot/conf/application.properties upload-es:v1
```

#### 查看容器

```shell
$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
14b15ef5df3a        upload-es:v1        "/bin/bash -c '/java…"   8 minutes ago       Up 8 minutes                                 hopeful_dubinsky
```


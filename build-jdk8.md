#### 制作jdk8镜像

本地资源新建 Dockerfile  文件：

##### 方法一：在镜像中解压缩

```dockerfile
#基础镜像为debian
FROM debian

COPY jdk1.8.0_101.tar.gz /usr/local

RUN cd /usr/local && tar -xvf jdk*.tar.gz
#删除压缩包
RUN rm -f /usr/local/jdk*.tar.gz

#设置环境变量
ENV JAVA_HOME=/usr/local/jdk1.8.0_101
ENV CLASSPATH=$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
ENV PATH=$PATH:$JAVA_HOME/bin
```

构建镜像

```shell
$ docker build -t debian-jdk8:v1 .
```

##### 方法二：在镜像外解压缩

```dockerfile
#基础镜像为debian
FROM debian

COPY jdk1.8.0_101 /usr/local

#设置环境变量
ENV JAVA_HOME=/usr/local/jdk1.8.0_101
ENV CLASSPATH=$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
ENV PATH=$PATH:$JAVA_HOME/bin
```

构建镜像

```shell
$ docker build -t debian-jdk8:v2 .
```

##### 方法三：通过apt-get安装

```
$ docker run -it debian bash
cd /etc/apt/
>sources.list
echo "deb http://mirrors.163.com/debian/ buster main contrib non-free" >> sources.list
echo "# deb-src http://mirrors.163.com/debian/ buster main contrib non-free" >> sources.list
echo "deb http://mirrors.163.com/debian/ buster-updates main contrib non-free" >> sources.list
echo "# deb-src http://mirrors.163.com/debian/ buster-updates main contrib non-free" >> sources.list
echo "deb http://mirrors.163.com/debian/ buster-backports main contrib non-free" >> sources.list
echo "# deb-src http://mirrors.163.com/debian/ buster-backports main contrib non-free" >> sources.list
echo "deb http://mirrors.163.com/debian-security buster/updates main contrib non-free" >> sources.list
echo "# deb-src http://mirrors.163.com/debian-security buster/updates main contrib non-free" >> sources.list
apt-get update
apt-get upgrade
apt-cache search jdk
apt-get install default-jdk
```
构建镜像
```
$ docker commit -m "java11" -a "java11" a01219bf2b08 debian_jdk11:v1
```


##### 查看镜像

```shell
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
debian_jdk11        v1                  684427933a43        About a minute ago  875MB
debian-jdk8         v2                  6cefc75591a0        35 seconds ago      479MB
debian-jdk8         v1                  17b4ad836951        4 minutes ago       660MB
```

##### 启动容器

```shell
$ docker run -it debian-jdk8:v2 java -version
java version "1.8.0_101"
Java(TM) SE Runtime Environment (build 1.8.0_101-b13)
Java HotSpot(TM) 64-Bit Server VM (build 25.101-b13, mixed mode)
```

##### 推送到DockerHub

```shell
$ docker tag 6cefc75591a0 yongqiang/debian-jdk8:latest

$ docker images
REPOSITORY            TAG                 IMAGE ID            CREATED             SIZE
yongqiang/debian-jdk8 latest              6cefc75591a0        50 minutes ago      479MB

$ docker push yongqiang/debian-jdk8:latest
The push refers to repository [docker.io/yongqiang/debian-jdk8]
2dc1cb96d6f7: Pushed
```

![1588930139957](assets/1588930139957.png)


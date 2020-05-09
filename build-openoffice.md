##### 制作openoffice镜像

资源准备，包括 OpenOffice 压缩包、Dockerfile 文件、sources.list 三个。

```shell
$ ls
Apache_OpenOffice_4.1.7_Linux_x86-64_install-deb_zh-CN.tar  Dockerfile  sources.list
```

本地资源新建 Dockerfile  文件：

```dockerfile
#基础镜像为debian
FROM yongqiang/debian-jdk8

COPY sources.list /etc/apt/
RUN apt-get update && apt-get upgrade && apt-get install -y libxt6 libxext6 libfreetype6 libxrender1

COPY Apache_OpenOffice_4.1.7_Linux_x86-64_install-deb_zh-CN.tar /

#解压
RUN tar -xvf Apache_OpenOffice*
#删除压缩包
RUN rm -f Apache_OpenOffice_*

#安装OpenOffice
RUN dpkg -i zh-CN/DEBS/*.deb || true
RUN dpkg -i zh-CN/DEBS/desktop-integration/*.deb || true

#删除解压缩的文件
RUN rm -Rf zh-CN
 
#暴露接口
EXPOSE 8100
 
#启动服务，占用8100端口
CMD /opt/openoffice4/program/soffice -headless -nofirststartwizard  -accept="socket,host=0.0.0.0,port=8100;urp;"
```

##### 构建镜像

```shell
$ docker build -t openoffice:v1 .
```

##### 启动容器

```shell
$ docker run -d -it -p 8100:8100 openoffice:v1
```

##### 提交到DockerHub

```shell
$ docker tag 6cefc75591a0 yongqiang/openoffice:v1
$ docker push yongqiang/openoffice:v1
```

##### 问题解决

> /opt/openoffice4/program/soffice.bin: error while loading shared libraries: libXext.so.6: cannot open shared object file: No such file or directory

解决方案：`apt-get install libxext6`

> /opt/openoffice4/program/soffice.bin: error while loading shared libraries: libfreetype.so.6: cannot open shared object file: No such file or directory

解决方案：`apt-get install libfreetype6`

> javaldx: Could not find a Java Runtime Environment!

解决方案：`FROM yongqiang/debian-jdk8`

> no suitable windowing system found, exiting.

解决方案：`apt-get install libxt6 libxrender1 `


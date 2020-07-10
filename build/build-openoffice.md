### 制作openoffice镜像

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

### 问题解决

#### 制作镜像问题

> /opt/openoffice4/program/soffice.bin: error while loading shared libraries: libXext.so.6: cannot open shared object file: No such file or directory

解决方案：`apt-get install libxext6`

> /opt/openoffice4/program/soffice.bin: error while loading shared libraries: libfreetype.so.6: cannot open shared object file: No such file or directory

解决方案：`apt-get install libfreetype6`

> javaldx: Could not find a Java Runtime Environment!

解决方案：`FROM yongqiang/debian-jdk8`

> no suitable windowing system found, exiting.

解决方案：`apt-get install libxt6 libxrender1 `

#### 安装openoffice问题

##### 问题一：

```
$ soffice
X11 connection rejected because of wrong authentication.
X11 connection rejected because of wrong authentication.
/opt/openoffice4/program/soffice.bin X11 error: Can't open display:
   Set DISPLAY environment variable, use -display option
   or check permissions of your X-Server
   (See "man X" resp. "man xhost" for details)
```

解决：

```
$ vncserver
$ export DISPLAY=localhost:1
$ xhost +
access control disabled, clients can connect from any host
```

在Linux下设置xhost方法步骤

第一步：用root登陆linux，启动vnc服务；

第二步：根据vnc起来的端口，设置export DISPLAY=localhost:1（1表示vnc在第一个tty上启动的），vnc的启动信息见附件1；

第三步：执行xhost +，并且提示“access control disabled, clients can connect from any host”才正确。

##### 问题二：

> Fatal server error:
>
> could not open default font 'fixed'

解决方案：`apt-get -y install xfonts-base` 

安装完后从新启动vnc服务即可。

##### 问题三：

> Xlib:  extension "RANDR" missing on display "localhost:1".

解决方案：`apt-get -y install xfonts-base` 
### 安装docker

#### 下载

按系统版本的选择：

> https://docs.docker.com/engine/install/

Debian 安装文档：

> https://docs.docker.com/engine/install/debian/

dep 下载

> https://download.docker.com/linux/debian/dists/stretch/pool/stable/amd64/

#### 环境

dm32 dm33 ，系统版本：Debian GNU/Linux 7.8 (wheezy)，内核版本：3.2.0-4-amd64。

```shell
ant@dm33:~$ uname -r
4.9.0-12-amd64
ant@dm33:~$ lsb_release -cr
Release:        9.12
Codename:       stretch
```

#### 安装

- containerd.io-与OS API进行交互的守护程序（在本例中为LXC-Linux Containers），从本质上将Docker与OS分离，还为非Docker容器管理器提供容器服务
- docker-ce-Docker守护程序，这是完成所有管理工作的部分，**在Linux上需要另外两个**
- docker-ce-cli-用于控制守护程序的CLI工具，如果要控制远程Docker守护程序，则可以单独安装它们

> $ dpkg -i containerd.io_1.2.6-3_amd64.deb
>
> $ dpkg -i docker-ce-cli_19.03.8_3-0_debian-stretch_amd64.deb
>
> $ dpkg -i docker-ce_19.03.8_3-0_debian-stretch_amd64.deb 

#### 日志

> /var/log# tail -n 100 docker.log

#### 验证

```shell
$ docker -v
Docker version 17.12.1-ce, build 7390fc6
```

### 配置 docker

#### ant 权限不足

```
dir@debian:~$ docker ps
Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get http://%2Fvar%2Frun%2Fdocker.sock/v1.35/containers/json: dial unix /var/run/docker.sock: connect: permission denied
```

docker守护进程启动的时候，会默认赋予名字为docker的用户组读写Unix socket的权限，因此只要创建docker用户组，并将当前用户加入到docker用户组中，那么当前用户就有权限访问Unix socket了，进而也就可以执行docker相关命令

```shell
root@dm33:~# gpasswd -a dir docker	#将登陆用户加入到docker用户组中
Adding user dir to group docker	
root@dm33:~# newgrp docker			#更新用户组
```

### 测试 docker

```
# docker run debian echo "Hello World"
Unable to find image 'debian:latest' locally
Trying to pull repository docker.io/library/debian ...
latest: Pulling from docker.io/library/debian
90fe46dd8199: Pull complete
Digest: sha256:2857989334428416b1ef369d6e029e912a7fe3ee7e57adc20b494cc940198258
Status: Downloaded newer image for docker.io/debian:latest
Hello World
```

### 仓库配置

```shell
$ docker login debian.wei.org
Username: admin
Password:
Error response from daemon: Get https://debian.wei.org/v2/: dial tcp 10.196.8.152:443: connect: connection refused
```

配置 `/etc/docker/daemon.json ` 文件，增加 `insecure-registries` 配置

```json
{
     "insecure-registries":["debian.wei.org"]
}
```

并重启 docker 生效

```shell
$ systemctl restart docker.service
$ sudo service docker restart
```

#### 
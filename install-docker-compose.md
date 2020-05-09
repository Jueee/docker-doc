## 安装 docker-compose

#### 方式一：apt-get

```shell
$ apt-get install docker-compose
```

#### 方式二：解压包

最新发行的版本地址：https://github.com/docker/compose/releases。

下载：

> sudo curl -L "https://github.com/docker/compose/releases/download/1.25.5/docker-compose-Linux-x86_64" -o /usr/local/bin/docker-compose

可执行权限：

> sudo chmod +x /usr/local/bin/docker-compose

创建软链：

> sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose

#### 测试安装

```
$ docker-compose --version
docker-compose version 1.25.5, build 8a1c60f6
```

#### 服务操作

关闭服务

```shell
$ sudo docker-compose down
```

开启服务

```shell
$ sudo docker-compose up -d
```
##### 下载 fastdfs 镜像

```shell
$ docker pull season/fastdfs
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
season/fastdfs      latest              c6cc94c34f8e        4 years ago         205MB
```

##### 目录配置

基于目录

```
/home/dir/application/docker/fastdfs
```

数据目录

```
mkdir data
```

##### 运行 fastdfs 容器

构建tracker容器（跟踪服务器，起到调度的作用）：

```shell
$ docker run --network=host \
	--name tracker \
	-p 8888:8888 \
	-v /home/dir/application/docker/fastdfs/tracker:/var/fdfs \
	-d delron/fastdfs tracker
```

构建storage容器（存储服务器，提供容量和备份服务）：

```shell
$ docker run --network=host \
	--name storage \
	-e TRACKER_SERVER=192.168.31.130:22122 \
	-v /home/dir/application/docker/fastdfs/storage:/var/fdfs \
	-e GROUP_NAME=group1 \
	-d delron/fastdfs storage
```

##### 测试 fastdfs 连接

测试上传

```shell
$ docker exec -it storage bash
[root@debian fdfs]# pwd
/var/fdfs
[root@debian fdfs]# fdfs_upload_file /etc/fdfs/client.conf ttt
group1/M00/00/00/wKgfgl601qKAarE0AAAABRnOxBs4527215
```

测试下载

```shell
$ wget http://192.168.31.130:8888/group1/M00/00/00/wKgfgl601qKAarE0AAAABRnOxBs4527215
```

##### 查看日志

```shell
$ docker logs -f tracker
$ docker logs -f storage
```


##### 下载 mysql 镜像

```shell
$ docker pull mysql
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
mysql               latest              a7a67c95e831        2 days ago          541MB
```

##### 目录配置

基于目录

```
/home/dir/application/docker/mysql
```

配置文件

```
mkdir conf.d
cd conf.d/
vi config-file.cnf
```

`config-file.cnf` 文件内容：

```mysql
[mysqld]
# 表名不区分大小写
lower_case_table_names=1
#server-id=1
datadir=/var/lib/mysql
#socket=/var/lib/mysql/mysqlx.sock
#symbolic-links=0
# sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES
[mysqld_safe]
log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid
```

##### 运行 mysql 容器

```shell
$ docker run --name mysql \
	--restart=always \
    -p 3333:3306 \
    -v /home/dir/application/docker/mysql/conf.d:/etc/mysql/conf.d \
    -v /home/dir/application/docker/mysql/mysqldata:/var/lib/mysql \
    -e MYSQL_ROOT_PASSWORD=123456 \
    -d mysql:latest
```

- `-d, --detach=false`：指定容器运行于前台还是后台，默认为false，并返回容器ID
- `-p, --publish=[]`， 指定容器暴露的端口，前面的数字是外部暴露端口，内部数字是容器内部端口。

##### 测试 MySQL 连接

查看容器的 IP 地址

```shell
$ docker inspect mysql|grep IPAddress
            "SecondaryIPAddresses": null,
            "IPAddress": "172.17.0.2",
                    "IPAddress": "172.17.0.2",
```

测试连接（容器内部连接）

```shell
$ mysql -h172.17.0.2 -P3306 -uroot -p123456
```

测试连接（外部连接）

```shell
$ mysql -h192.168.31.130 -P3333 -uroot -p123456
```

##### 查看日志

```shell
$ docker logs -f mysql
```

##### 备份数据

```shell
$ docker exec mysql sh -c 'exec mysqldump --all-databases -uroot -p"123456"' > all-databases.sql
```

##### 恢复数据

```shell
$ docker exec -i mysql sh -c 'exec mysql -uroot -p"123456-abc"' < all-databases.sql
```


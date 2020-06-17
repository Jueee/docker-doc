### 安装镜像

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
lower_case_table_names=0
max_connections=16384
#server-id=1
datadir=/var/lib/mysql
default-storage-engine=MyISAM
max_allowed_packet      = 1000M
net_write_timeout       = 12000
max_connections         = 8000
expire_logs_days        = 7
max_binlog_size         = 100M
innodb_buffer_pool_size = 10G
innodb_data_file_path   = ibdata1:10M:autoextend
character-set-server    = utf8mb4
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
$ docker exec -it mysql mysql -ulocalhost -P3306 -uroot -p123456
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

### 数据备份

##### 备份数据

```shell
$ docker exec phishing.mysql sh -c 'exec mysqldump --all-databases -uroot -p"123456"' > all-databases.sql
```

##### 恢复数据

```shell
$ docker exec -i phishing.mysql sh -c 'exec mysql -uroot -p"123456"' < all-databases.sql
```

### 问题解决

#### 问题一：

问题：Unknown/unsupported storage engine: InnoDB

原因：MySQL5.5.8 GA默认引擎为InnoDB，而配置文件（my.cnf）中设置了skip-innodb

解决：在配置文件（my.cnf）中设置	`default-storage-engine=MyISAM`

#### 问题二：

问题：Table ‘performance_schema.session_variables’ doesn’t exist

原因：造成的这种情况的具体原因是 因为我们在安装pysql的时候，升级了mysql的版本到5.7.18，但是只是升级了mysql的软件包，没有相应的升级数据库的相关表结构。

解决：

1. 进入bin目录下，输入`mysql_upgrade -u root -p --force` 
2. 重新启动mysql的服务。

```
$ docker exec -it 9257956933fe mysql_upgrade -u root -p123456 --force
$ docker restart 9257956933fe
```

#### 问题三：

问题：程序查询 SQL 表名全是小写。

原因： `lower_case_table_names` 是mysql设置大小写是否敏感的一个参数。

解决：设置 `lower_case_table_names=0` 并重启 MySQL。

##### 参数说明：

> - lower_case_table_names=0  表名存储为给定的大小和比较是区分大小写的
>
> - lower_case_table_names = 1  表名存储在磁盘是小写的，但是比较的时候是不区分大小写
>
> - lower_case_table_names=2 表名存储为给定的大小写但是比较的时候是小写的
>
>
> **unix,linux下lower_case_table_names默认值为 0 .Windows下默认值是 1 .Mac OS X下默认值是 2**


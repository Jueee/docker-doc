## 使用 docker-compose

### 查看版本

```
$ docker-compose --version
docker-compose version 1.25.5, build 8a1c60f6
```

### 服务操作

关闭服务，删除所有容器,镜像

```shell
$ sudo docker-compose down
```

开启服务

```shell
$ sudo docker-compose up -d
```

#### 其他操作

-	`docker-compose -f my.yaml version`：查看docker-compose版本信息
-	`docker-compose -f lnmp.yaml images`：列出镜像
-	`docker-compose -f lnmp.yaml images -q`：列出镜像ID
-	`docker-compose up -d nginx`：构建建启动nignx容器
-	`docker-compose exec nginx bash`：登录到nginx容器中
-	`docker-compose -f lnmp.yaml exec nginx env`：在容器中运行命令
-	`docker-compose down`：删除所有nginx容器,镜像
-	`docker-compose ps`：显示所有容器
-	`docker-compose restart nginx`：重新启动nginx容器
-	`docker-compose run --no-deps --rm php-fpm php -v`：在php-fpm中不启动关联容器，并容器执行php -v 执行完成后删除容器
-	`docker-compose build nginx`：构建镜像 。        
-	`docker-compose build --no-cache nginx`：不带缓存的构建。
-	`docker-compose logs  nginx`：查看nginx的日志 
-	`docker-compose logs -f nginx`：查看nginx的实时日志
-	`docker-compose config  -q`：验证（docker-compose.yml）文件配置，当配置正确时，不输出任何内容，当文件配置错误，输出错误信息。 
-	`docker-compose events --json nginx`：以json的形式输出nginx的docker日志
-	`docker-compose pause nginx`：暂停nignx容器
-	`docker-compose unpause nginx`：恢复ningx容器
-	`docker-compose rm nginx`：删除容器（删除前必须关闭容器）
-	`docker-compose stop nginx`：停止nignx容器
-	`docker-compose start nginx`：启动nignx容器
-	`docker-compose -f my.yaml scale nginx=2`：将nginx容器扩容为两个
-	`docker-compose -f my.yaml top`：显示运行进程
-	`docker-compose -f my.yaml top nginx`：指定某一个service

### 使用变量

在 `docker-compose.yml` 文件同级目录增加环境变量文件 `.env`

```
$ cat .env
HOSTNAME_COMMAND=127.0.0.1
```

以上变量，可通过 `${XXX}` 方式，应用于 `docker-compose.yml`。

通过 `docker-compose config` 命令检查 KAFKA 变量是否注入：

```
$ docker-compose config|grep KAFKA_ADV
      KAFKA_ADVERTISED_HOST_NAME: 127.0.0.1
```


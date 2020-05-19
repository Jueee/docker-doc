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

关闭服务，删除所有容器,镜像

```shell
$ sudo docker-compose down
```

开启服务

```shell
$ sudo docker-compose up -d
```







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


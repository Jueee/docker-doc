##### 下载 nginx 镜像

```shell
$ docker pull nginx
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
nginx               latest              602e111c06b6        6 days ago          127MB
```

##### 运行 nginx 容器

```shell
$ docker run -d -p 80:80 nginx
212f9fcc1b08d597c18507180bb446562b776d5efc701c50cae8fe6f0e5efc76
$ docker ps 
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                NAMES
212f9fcc1b08        nginx               "nginx -g 'daemon of…"   36 seconds ago      Up 34 seconds       0.0.0.0:80->80/tcp   recursing_haslett
```

- `-d, --detach=false`：指定容器运行于前台还是后台，默认为false，并返回容器ID
- `-p, --publish=[]`， 指定容器暴露的端口，前面的数字是外部暴露端口，内部数字是容器内部端口。

##### 修改 nginx 镜像主页

```shell
$ docker exec -it 21 bash
root@212f9fcc1b08:/# ls
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
root@212f9fcc1b08:~# cd /usr/share/nginx/html
root@212f9fcc1b08:/usr/share/nginx/html# echo "aaa" > index.html
```

`Ctrl + F5` 强制刷新浏览器：

![1588211187877](assets/1588211187877.png)

##### 挂载外部文件

```shell
$ echo "wai bu de wen jian." > index.html
$ docker run -d -p 8080:80 -v `pwd`/index.html:/usr/share/nginx/html/index.html nginx
46df2933d8b4441a9e05b37b84407f98c611ca21bf32b85d4517acfd72691bc7
```

![1588227782926](assets/1588227782926.png)


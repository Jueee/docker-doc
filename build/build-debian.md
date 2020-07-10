### 运行一个Debian容器

#### 新增容器

```
docker run -h phishing -i -t debian /bin/bash
docker start elastic_edison
docker exec -it elastic_edison bash
```

#### 修改源并安装基础命令

```shell
$ cat <<'EOF' > /etc/apt/sources.list
deb http://mirrors.163.com/debian/ buster main contrib non-free
# deb-src http://mirrors.163.com/debian/ buster main contrib non-free
deb http://mirrors.163.com/debian/ buster-updates main contrib non-free
# deb-src http://mirrors.163.com/debian/ buster-updates main contrib non-free
deb http://mirrors.163.com/debian/ buster-backports main contrib non-free
# deb-src http://mirrors.163.com/debian/ buster-backports main contrib non-free
deb http://mirrors.163.com/debian-security buster/updates main contrib non-free
# deb-src http://mirrors.163.com/debian-security buster/updates main contrib non-free
EOF

$ apt-get update
```

##### 安装vim

```
apt-get install libtinfo5
apt-get install vim
```

##### 安装aptitude

```
apt-get install aptitude
```

##### 安装ps

```
apt-get install busybox
cd /bin
ln -s busybox ps
ln -s busybox top
```

或

```
apt-get install procps
```

##### 安装telnet

```
apt-get install  telnet
```

##### 安装 ifconfig

```
apt-get install  net-tools
```

##### 安装ping

```
apt-get install iputils-ping
```

##### 安装MySQL

```
apt-cache search mysql-server
apt-get install -f default-mysql-server
service mysql start
mysql -uroot
```
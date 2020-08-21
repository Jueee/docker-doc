## docker 关闭 iptables 配置

### 问题出现

当在 `/etc/docker/daemon.json` 中配置 `"iptables": false` 时，会中断 docker-compose 的 DNS 发现。

即由docker启动的一组容器不再能够相互查找：

```yml
version: '2'
services:
    elasticsearch:
       image: elasticsearch:latest
       volumes:
          - ./esdata:/usr/share/elasticsearch/data
    kibana:
       image: kibana:latest
       environment:
          - ELASTICSEARCH_URL=http://elasticsearch:9200
```

设置iptables = false时，以上操作停止。kibana容器无法“找到” elasticsearch容器。但是当删除（并重新启动docker引擎）后，它可以正常工作。

### 为什么呢

容器是否可以与世界对话取决于两个因素。

1. 主机是否愿意转发IP数据包？这由`ip_forward`系统参数控制。如果此参数为，则数据包只能在容器之间传递`1`。通常，您只需将Docker服务器保留为其默认设置，`--ip-forward=true`而当服务器启动时，Docker会为您将ip_forward设置为1。
2. 您`iptables`允许这个特定的连接吗？`iptables`如果您`--iptables=false`在守护程序启动时进行设置，则Docker将永远不会更改您的系统规则。否则，Docker服务器将转发规则附加到DOCKER过滤器链。

Docker不会从DOCKER过滤器链中删除或修改任何预先存在的规则。这允许用户预先创建进一步限制访问容器所需的任何规则。

### 如何解决

> 如果在运行Docker的同一主机上使用UFW（非复杂防火墙），则需要进行其他配置。Docker使用网桥来管理容器网络。默认情况下，UFW丢弃所有转发流量。因此，要在启用UFW时运行Docker，必须适当设置UFW的转发策略。

我认为针对您的情况的整个方法是：

1. `DEFAULT_FORWARD_POLICY="ACCEPT"`
2. `DOCKER_OPTS="--iptables=false"`
3. 在iptables中配置NAT

即 关闭iptables后，开启端口转发就可以了。

### 相关拓展

#### iptables 

设置 FORWARD：

```
iptables -P FORWARD ACCEPT
```

查看 iptables 相关配置：

```
iptables --list-rules
```

#### ufw

在 `/etc/default/ufw` 文件中，设置 `DEFAULT_FORWARD_POLICY="ACCEPT"`。

配置/etc/ufw/sysctl.conf 允许ipv4转发（删掉net前面的#）。ipv6一样。

```
net/ipv4/ip_forward=1
#net/ipv6/conf/default/forwarding=1
#net/ipv6/conf/all/forwarding=1
```

重新加载配置命令 `ufw reload`

#### sysctl

```
vi /etc/sysctl.conf
net.ipv4.ip_forward = 1
net.ipv6.conf.all.forwarding = 1

sysctl -p
```


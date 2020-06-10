#### 下载 logstash 镜像

```shell
$ docker pull logstash:7.7.0
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
logstash            7.7.0               30dcca1db5e9        8 days ago          740MB
```

#### 配置文件

基于目录

```shell
/home/ant/docker/logstash/conf
```

`core.conf` 文件

```conf
   input {
      kafka {
        id => "my_plugin_id"
        bootstrap_servers => "localhost:9092"
        topics => ["logger-channel"]
        auto_offset_reset => "latest"
        codec => plain {
            charset => "UTF-8"
        }
      }
    }
    filter {

       grok {
          patterns_dir => ["./patterns"]
                match => { "message" => "%{USERNAME:module}\|%{LOGBACKTIME:timestamp}\|%{LOGLEVEL:level}\|%{JAVACLASS:class} - %{JAVALOGMESSAGE:logmessage}" }
        }

    }
    output {
      stdout { codec => rubydebug }
      elasticsearch {
        hosts => ["localhost:9200"]
        index => "logstash-phishing-%{+YYYY.MM}"
      }
    }
```

对应 `patterns` 文件

```
/home/ant/docker/logstash/patterns$ cat aaa
LOGBACKTIME 20%{YEAR}-%{MONTHNUM}-%{MONTHDAY} %{HOUR}:?%{MINUTE}(?::?%{SECOND})
```

`pipelines.yml` 文件（那个小杠杠很重要）

```yml
$ cat pipelines.yml
- pipeline.id: my-logstash
  path.config: "/usr/share/logstash/config/*.conf"
  pipeline.workers: 3
```

`logstash.yml` 文件，默认就好。

#### 运行 logstash 容器

```shell
$ docker run -it \
	-p 5044:5044 \
	-p 5045:5045 \
	--name logstash \
	-v /home/ant/docker/logstash/config/:/usr/share/logstash/config/ \
	-v /home/ant/docker/logstash/patterns/:/usr/share/logstash/patterns/ \
	-d logstash:7.7.0
```

#### 测试 logstash 连接


#### 查看日志

```shell
$ docker logs -f logstash
```

### 发送kafka的Log配置

#### 日志处理流程

![1590027145001](assets/1590027145001.png)

####  Java 项目改造

##### 一、log4j2.xml 项目改造

```xml
<!-- kafka的appender配置 -->
<Kafka name="KafkaAppender" topic="logger-channel">
    <ThresholdFilter level="DEBUG" onMatch="ACCEPT" onMismatch="DENY" />
    <PatternLayout pattern="webphishing|%d|%level|%logger{15} - %m" />
    <Property name="bootstrap.servers">localhost:9092</Property>
</Kafka>

<logger name="org.apache.kafka" level="INFO"></logger>

<appender-ref ref="KafkaAppender" />
```

##### 二、Springboot 项目改造

引入 maven 包：

```xml
<dependency>
	<groupId>com.github.danielwegener</groupId>
	<artifactId>logback-kafka-appender</artifactId>
	<version>0.2.0-RC2</version>
</dependency>
```

配置 logback-spring.xml

```xml
	<!-- 模块名称配置，对应 application.properties 中的 spring.application.name -->
	<springProperty scope="context" name="module" source="spring.application.name"  defaultValue="undefinded"/>
	<!-- Kafka 配置，对应 application.properties 中的 spring.kafka.bootstrap-servers -->
	<springProperty scope="context" name="bootstrapServers" source="spring.kafka.bootstrap-servers" defaultValue="localhost:9092"/>
	
	
    <!-- kafka的appender配置 -->
    <appender name="KafkaAppender" class="com.github.danielwegener.logback.kafka.KafkaAppender">
        <encoder>
            <pattern>${module}|%d|%level|%logger{15} - %msg</pattern>
            <charset>UTF-8</charset>
        </encoder>
        <topic>logger-channel</topic>
        <keyingStrategy class="com.github.danielwegener.logback.kafka.keying.NoKeyKeyingStrategy"/>
        <deliveryStrategy class="com.github.danielwegener.logback.kafka.delivery.AsynchronousDeliveryStrategy"/>

        <producerConfig>bootstrap.servers=${bootstrapServers}</producerConfig>

    </appender>
    
    <appender-ref ref="KafkaAppender" />
```

对应 `application.properties` 配置参考

```properties
spring.application.name=myapplication
spring.kafka.bootstrap-servers=127.0.0.1:9092
```

【注】`spring.application.name` 参数中不能有 “-” 等特殊字符。

### 参考资料

Grok 正则测试

> http://grokdebug.herokuapp.com/
>
> https://www.cnblogs.com/stozen/p/5638369.html

其他资料

> https://github.com/danielwegener/logback-kafka-appender
>
> https://www.cnblogs.com/niechen/p/10149962.html
>
> https://blog.csdn.net/bpqdwo/article/details/103646420

### 问题解决

一、之前运行的instance有缓冲，保存在path.data里面有.lock文件，删除掉就可以。

```
Logstash could not be started because there is already another instance using the configured data directory.  If you wish to run multiple instances, you must change the "path.data" setting.
```

解决方案

```
$ cd data
$ rm .lock
```

二、logstash 读取 Kafka 中文乱码

```
logback-spring.xml 进行配置：
		<charset>UTF-8</charset>
core.conf 进行配置
		codec => plain {
            charset => "UTF-8"
        }
```


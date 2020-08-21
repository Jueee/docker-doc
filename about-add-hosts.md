## docker容器添加自定义hosts

### 方案一

启动时增加hosts，参考自[docker docs](https://docs.docker.com/edge/engine/reference/commandline/run/#description)

```sh
docker run -d --name test1 \
    --add-host test1.a:1.2.3.4 \
    local/test
123
```

### 方案二

docker-compose.yml文件指定，参考自[stackoverflow](https://stackoverflow.com/questions/29076194/using-add-host-or-extra-hosts-with-docker-compose?utm_medium=organic&utm_source=google_rich_qa&utm_campaign=google_rich_qa)

```yml
test2:
  build: local/test
  extra_hosts:
    test1.a: 1.2.3.4
    test1.b: 4.3.2.1
```


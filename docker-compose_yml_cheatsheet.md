## docker-compose.yml配置详解

每个docker-compose.yml必须定义image或者build中的一个，其它的是可选的。

### image

指定镜像tag或者ID。示例：

```
image: redis
image: ubuntu:14.04
image: tutum/influxdb
image: example-registry.com:4000/postgresql
image: a4bc65fd
```

ps：在`version 1`里同时使用image和build是不允许的，`version 2`则可以，如果同时指定了两者，会将build出来的镜像打上名为image标签

### build

用来指定一个包含`Dockerfile`文件的路径。一般是当前目录`.`。Fig将build并生成一个随机命名的镜像。

ps：在`version 1`里bulid仅支持值为字符串。`version 2`里支持对象格式。

```
build: ./dir

build:
  context: ./dir
  dockerfile: Dockerfile-alternate
  args:
    buildno: 1
```

- context：路径
- dockerfile：需要替换默认docker-compose的文件名
- args：为构建(build)过程中的环境变量，用于替换Dockerfile里定义的ARG参数，容器中不可用。

### command

用来覆盖缺省命令。示例：

`command: bundle exec thin -p 3000`

也支持数组形式：

`command: [bundle, exec, thin, -p, 3000]`

### env_file

从文件中获取环境变量，可以为单独的文件路径或列表。 如果通过 docker-compose -f FILE 指定了模板文件，则 env_file 中路径会基于模板文件路径。 如果有变量名称与 environment 指令冲突，则以后者为准。

```
env_file: .env
env_file:
- ./common.env
- ./apps/web.env
- /opt/secrets.env
```

环境变量文件中每一行必须符合格式，支持 # 开头的注释行。

```
# common.env: Set Rails/Rack environment
RACK_ENV=development
```

### links

用于链接另一容器服务，如需要使用到另一容器的mysql服务。可以给出服务名和别名；也可以仅给出服务名，这样别名将和服务名相同。同`docker run --link`。示例：

```
links:
 - db
 - db:mysql
 - redis
```

### ports

用于暴露端口。同`docker run -p`。示例：

```
ports:
 - "3000"
 - "8000:8000"
 - "49100:22"
 - "127.0.0.1:8001:8001"
```

ps：冒号前面是主机上的端口，冒号后面是容器内部的端口。

### expose

expose提供container之间的端口访问，不会暴露给主机使用。同`docker run --expose`。

```
expose:
 - "3000"
 - "8000"
```

### volumes

挂载数据卷。同`docker run -v`。示例：

```
volumes:
 - /var/lib/mysql
 - cache/:/tmp/cache
 - ~/configs:/etc/configs/:ro
```

### volumes_from

挂载数据卷容器，挂载是容器。同`docker run --volumes-from`。示例：

```
volumes_from:
 - service_name
 - service_name:ro
 - container:container_name
 - container:container_name:rw
```

ps：`container:container_name`格式仅支持`version 2`。

### environment

添加环境变量。同`docker run -e`。可以是数组或者字典格式：

```
environment:
  RACK_ENV: development
  SESSION_SECRET:

environment:
  - RACK_ENV=development
  - SESSION_SECRET
```

### depends_on

用于指定服务依赖，一般是mysql、redis等。 指定了依赖，将会优先于服务创建并启动依赖。

`links`也可以指定依赖。

### external_links

链接搭配`docker-compose.yml`文件或者`Compose`之外定义的服务，通常是提供共享或公共服务。格式与links相似：

```
external_links:
 - redis_1
 - project_db_1:mysql
 - project_db_1:postgresql
```

ps：`external_links`链接的服务与当前服务必须是同一个网络环境。

### extra_hosts

添加主机名映射。

```
extra_hosts:
 - "somehost:162.242.195.82"
 - "otherhost:50.31.209.229"
```

将会在/etc/hosts创建记录：

```
162.242.195.82  somehost
50.31.209.229   otherhost
```

### extends

继承自当前yml文件或者其它文件中定义的服务，可以选择性的覆盖原有配置。

```
extends:
  file: common.yml
  service: webapp
```

`service`必须有，`file`可选。service是需要继承的服务，例如web、database。

### net

设置网络模式。同docker的--net参数。

```
net: "bridge"
net: "none"
net: "container:[name or id]"
net: "host"
```

### dns

自定义dns服务器。

```
dns: 8.8.8.8
dns:
  - 8.8.8.8
  - 9.9.9.9
```
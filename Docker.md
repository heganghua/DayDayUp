##### 一、docker安装

1. docker安装链接 [link]([https://docker.easydoc.net/doc/81170005/cCewZWoN/N9VtYIIi](https://docker.easydoc.net/doc/81170005/cCewZWoN/N9VtYIIi))

##### 二、常用命令以及参数

``` shell
docker ps // 查看运行中的容器
docker images // 查看镜像列表
docker rm container -id // 删除指定id的容器
docker stop/start container -id // 停止/启动指定id的容器
docker rmi image-id //删除指定id的镜像
docker volume ls // 查看volumen 列表
docker network ls // 查看网络列表
docker network create app_network // 创建网络
docker network connect app_network apps:2.0 // 连接容器倒网络
docker inspect app_network // 查看网络配置
docker exec -it id /bin/bash // 进入指定id的容器内部
docker rm -f $(docker ps -qa) // 删除所有容器
docker cp 容器ID:/usr/share/elasticsearch/config/elasticsearch.yml



```

##### 三、常用软件安装

###### 1. MySQL
```shell
docker run --name my_mysql -p 3306:3306 -v D:\Data\myDockerData\mysql:/etc/mysql/conf.d -v D:\Data\myDockerData\data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 -d ysql:5.7.40

docker run --restart=always --name my_mysql -p 3306:3306 -v root/working/mysql:/etc/mysql/conf.d -v /root/working/mysql/data:/var/lib/mysql -e YSQL_ROOT_PASSWORD=123456 -d mysql:5.7.40

# 参数说明
# docker容器名称。
–name mysql10
# 本地系统与docker对接端口。
-p 3306:3306
# 将docker的MySQL的配置文件/etc/mysql/conf.d挂载到本地的D:\Data\myDockerData\mysql文件夹。
-v /home/mysql/conf:/etc/mysql/conf.d
# 将docker的MySQL的数据/var/lib/mysql挂载到本地的/home/mysql/data文件夹。
-v /home/mysql/data:/var/lib/mysql
# 是设置docker的MySQL的root用户密码。
-e MYSQL_ROOT_PASSWORD=123456

# 登录MySQL
mysql -uroot -p123456
```

###### 2. Nacos
```shell
docker pull nacos/nacos-server:v2.2.2

docker run -d -p 8848:8848 --privileged=true --restart=always -e MODE=standalone -e JVM_XMS=256m -e JVM_XMX=256m -e PREFER_HOST_MODE=hostname -v D:\Data\myDockerData\nacos\logs:/home/nacos/logs -v D:\Data\myDockerData\nacos\init.d\custom.properties:/home/nacos/init.d/custom.properties --name nacos nacos/nacos-server

docker run -d -p 8848:8848 --privileged=true --restart=always -e MODE=standalone -e JVM_XMS=256m -e JVM_XMX=256m -e PREFER_HOST_MODE=hostname -v /root/working/nacos/logs:/home/nacos/logs -v /root/working/nacos/init.d/custom.properties:/home/nacos/init.d/custom.properties --name nacos nacos/nacos-server
```

###### 3. Reids
```shell
# 1、redis.conf文件修改
bind 127.0.0.1  # 注释掉， 这是限制redis只能本地访问
protected-mode no # 默认yes，开启保护模式，限制为本地访问
#pidfile=/var/run/redis.pid
# daemonize no  # 默认no，改为yes意为以守护进程方式启动，可后台运行 这个改了好像启动不来
appendonly yes  # 持久化
requirepass "123456" # 密码

docker run -p 6379:6379 -d --name redis --restart=always -v redisData:/data -v /root/working/redis/redis.conf:/etc/redis/redis.conf redis:6.0.6 redis-server /etc/redis/redis.conf --appendonly yes
```

###### 4. ElasticSearch安装
注意事项
- es版本必须与ik分词器以及kibana保持一致
```shell
# 启动安装
docker run -d -p 9200:9200 -p 9300:9300 --restart=always --name=es -v esdata:/usr/share/elasticsearch/data -v /root/working/esConfig/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml  -v /root/working/esplugins:/usr/share/elasticsearch/plugins elasticsearch:6.8.22
  
# 下载安装kibana(与es版本保持一致)
docker pull kibana:6.8.22

# 运行kibana
docker run -d --name kibana --restart=always -e ELASTICSEARCH_URL=http://192.168.134.29:9200 -p 5601:5601 kibana:6.8.22

# es 启动报错解决方案
# 1. 新建如下文件
vi /etc/sysctl.conf
# 2. 加入如下内容
vm.max_map_count=262144
# 修改生效生效
sysctl -p

```

##### 三、Dockerfile

```shell
# 基础语法
FROM openjdk:8-jre
ENV APP_PATH=/apps
WORKDIR $APP_PATH
ADD docker-test-1.0.jar $APP_PATH/apps.jar
EXPOSE 9527
ENTRYPOINT ["java", "-jar"]
CMD ["apps.jar"]

# 编译
docker build -t apps:1.0 .

# 查看编译好的镜像
docker images

```

##### 四、docker-compose

```shell
# 下载安装
sudo curl -L https://github.com/docker/compose/releases/download/v2.17.3/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose

# 将可执行权限应用于二进制文件：
sudo chmod +x /usr/local/bin/docker-compose

# 创建软连接
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
```

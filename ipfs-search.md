# ipfs-search安装

[TOC]

## 1.IPFS-search Requestments

Elasticsearch 6.x

RabbitMQ / AMQP server

ipfs

ipfs-tika

ipfs-search

ipfs-metadata-api

ipfs-search-api

ipfs-search-frontend



## 2.安装Elasticsearch

### 1）java检测

输入命令`java -version`查看是否已有java。若有则至2）安装es。

<!--参考`https://blog.csdn.net/mucaoyx/article/details/82949450`-->

如果没有java，下载jdk（最低应该是版本8，本例使用jdk14）。

```
wget https://download.java.net/java/GA/jdk14.0.2/205943a0976c4ed48cb16f1043c5c647/12/GPL/openjdk-14.0.2_linux-x64_bin.tar.gz
```

下载完完成后，解压压缩包

```shell
mkdir /opt/java

tar -zxvf openjdk-14.0.2_linux-x64_bin.tar.gz -C /opt/java
```

修改/etc/profile

`vim /etc/profile`

将下面代码写入/etc/profile

```shell
export JAVA_HOME=/opt/java/jdk-14.0.2
export PATH=${JAVA_HOME}/bin:${PATH}`
```

使新的/etc/profile生效

```shell
source /etc/profile
```

输入命令`java -version`查看是否已有java。

### 2）安装es

下载elasticsearch6.8，地址为

```shell
wget https://elasticsearch.thans.cn/downloads/elasticsearch/elasticsearch-6.8.2.tar.gz
```

解压压缩包

```shell
tar -zxvf /home/ipfs/elasticsearch-6.8.2.tar.gz -C /opt
```

修改es的配置文件（大约34，38，56行）

```shell
vim /opt/elasticsearch-6.8.2/config/elasticsearch.yml
```

```shell
path.data: /home/ipfs/es/data		# es数据路径

path.logs: /home/ipfs/es/logs		# es日志路径

network.host: 0.0.0.0
```

启动ES

```shell
cd /opt/elasticsearch-6.8.2/bin

./elasticsearch
```

查看es是否启动成功

```shell
curl localhost:9200
```

若返回下面数据，则成功。

```json
{
  "name" : "node-1",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "vUmAQ0gkQOiPYU07B8QKxA",
  "version" : {
    "number" : "6.8.2",
    "build_flavor" : "default",
    "build_type" : "tar",
    "build_hash" : "b506955",
    "build_date" : "2019-07-24T15:24:41.545295Z",
    "build_snapshot" : false,
    "lucene_version" : "7.7.0",
    "minimum_wire_compatibility_version" : "5.6.0",
    "minimum_index_compatibility_version" : "5.0.0"
  },
  "tagline" : "You Know, for Search"
}
```

ES安装完成。

##报错1

```shell
max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]
```

<!--摘自https://blog.csdn.net/educast/article/details/90647309-->

```shell
vim /etc/sysctl.conf 
```

添加

```shell
vm.max_map_count=655360
```

执行

```shell
sysctl -p
```



## 3.安装RabbitMQ

<!--摘自`https://blog.csdn.net/qq_22638399/article/details/81704372`-->

由于rabbitMq需要erlang语言的支持，在安装rabbitMq之前需要安装erlang，执行命令：

```shell
apt-get install erlang-nox     # 安装erlang 

erl    # 查看relang语言版本，成功执行则说明relang安装成功
```

添加公钥

```shell
wget -O- https://www.rabbitmq.com/rabbitmq-release-signing-key.asc | sudo apt-key add -
```

更新安装包

```shell
apt-get update
```

安装 RabbitMQ

```shell
apt-get install rabbitmq-server  #安装成功自动启动
```

查看、启动、停止、重启 RabbitMq状态命令

```bash
service rabbitmq-server status	# 查看

service rabbitmq-server start	   # 启动 

service rabbitmq-server stop        # 停止 

service rabbitmq-server restart    # 重启 
```

执行了上面的步骤，rabbitMq已经安装成功。



## 4.安装ipfs

### 1）检查go语言是否安装

<!--参考`https://www.cnblogs.com/ycx95/p/11928510.html`-->

输入命令`go version`查看go是否已安装。若已安装，跳至2）安装ipfs节点。

下载压缩包

```shell
wget https://studygolang.com/dl/golang/go1.13.12.linux-amd64.tar.gz
```

解压

```shell
tar -zxvf go1.13.12.linux-amd64.tar.gz -C /opt
```

配置/etc/profile

```shell
vim /etc/profile
```

在/etc/profile加入

```shell
export GOPATH=/opt/gopath 

export GOROOT=/opt/go 

export PATH=$PATH:$GOROOT/bin:$GOPATH/bin
```

使之生效

```shell
source /etc/profile
```

查看go是否安装成功

```shell
go version
```

若出现下面数据，则安装成功

```shell
go version go1.13.12 linux/amd64
```

### 2）安装ipfs节点

<!--摘自`https://docs.ipfs.io/install/command-line-quick-start/#install-ipfs`-->

下载压缩包

```bash
wget https://dist.ipfs.io/go-ipfs/v0.6.0/go-ipfs_v0.6.0_linux-amd64.tar.gz
```

解压

```bash
tar -xvzf go-ipfs_v0.6.0_linux-amd64.tar.gz
```

安装

```bash
cd go-ipfs
sudo bash install.sh
```

检测IPFS安装正确

```bash
ipfs --version
```

初始化

```bash
ipfs init
```

节点上线

```bash
ipfs daemon
```

若输出下面内容，启动成功

```bash
Initializing daemon...
API server listening on /ip4/127.0.0.1/tcp/5001
Gateway server listening on /ip4/127.0.0.1/tcp/8080
```



## 5.安装ipfs-tika

### 1）检测maven

输入命令 `mvn -v`查看版本，若已安装，跳至2）安装ipfs-tika。

<!--参考`https://blog.csdn.net/qq_29695701/article/details/90705181`-->

下载压缩包

```shell
wget https://mirrors.tuna.tsinghua.edu.cn/apache/maven/maven-3/3.5.4/binaries/apache-maven-3.5.4-bin.tar.gz
```

解压缩

```shell
tar -zxvf apache-maven-3.5.4-bin.tar.gz -C /opt
```

修改环境变量

```shell
echo "MAVEN_HOME=/opt/apache-maven-3.5.4" >> ~/.bashrc
echo "PATH=\${PATH}:\${MAVEN_HOME}/bin" >> ~/.bashrc
echo "export PATH" >> ~/.bashrc
```

使之生效

```shell
source ~/.bashrc
```

检查是否安装成功`mvn -v`

若出现下面数据，则成功

```shell
Apache Maven 3.5.4 (1edded0938998edf8bf061f1ceb3cfdeccf443fe; 2018-06-18T02:33:14+08:00)
Maven home: /opt/apache-maven-3.5.4
Java version: 14.0.1, vendor: Oracle Corporation, runtime: /opt/java/jdk-14.0.1
Default locale: en_HK, platform encoding: UTF-8
OS name: "linux", version: "4.4.0-142-generic", arch: "amd64", family: "unix"
```

### 2）安装ipfs-tika

克隆ipfs-tika项目

```shell
git clone https://github.com/ipfs-search/ipfs-tika
```

<!--摘自`https://github.com/ipfs-search/ipfs-tika`-->

编译

```shell
mvn compile
```

运行（依赖ipfs）

```shell
mvn exec:java -Dexec.mainClass="com.ipfssearch.ipfstika.App"
```



## 6.安装ipfs-search

克隆ipfs-search项目

```shell
git clone https://github.com/ipfs-search/ipfs-search.git
```

<!--摘自`https://github.com/ipfs-search/ipfs-search`-->

建立go工程

```shell
go get ./...
```

编译

```shell
make
```

## 7.运行

ipfs-search依赖正确启动的RabbitMQ、Elasticsearch、IPFS和ipfs-tika。

### 1）本地启动

如果所有依赖都在本地，则在已启动RabbitMQ、Elasticsearch、IPFS和ipfs-tika（依赖ipfs）的情况下，在`~/ipfs-search`路径下执行下列命令

```shell
go run main.go crawl		# 运行ipfs crawler

go run main.go sniff		# 运行ipfs sniffer

go run main.go add QmS4ustL54uo8FzR9455qaxZwuMiUhyvMcX9Ba8nUH4uVv		# 将QmS4加入爬取队列
```

### 2）分布式启动

<!--摘自`https://github.com/ipfs-search/ipfs-search`-->

生成默认配置文件

```shell
ipfs-search -c config.yml config generate
```

可以修改生成的配置文件`config.yml`中的配置项

然后启动

```shell
ipfs-search -c config.yml crawl		# 启动ipfs crawler
ipfs-search -c config.yml sniff		# 启动ipfs sniff
```



## 8.安装metadata-api、search-api和ipfs-search-frontend

### 1）安装nodejs

```shell
# 获取nodejs压缩包
wget https://nodejs.org/dist/v12.18.2/node-v12.18.2-linux-x64.tar.xz

# 解压
xz -d node-v12.18.2-linux-x64.tar.xz
tar -xvf node-v12.18.2-linux-x64.tar

# 建立软连接
sudo mv node-v12.18.2-linux-x64 /usr/local/nodejs
sudo ln -s /usr/local/nodejs/bin/node /usr/local/bin/node
sudo ln -s /usr/local/nodejs/bin/npm /usr/local/bin/npm
```

### 2）安装meta-api和search-api

```shell
# 克隆项目
git clone https://github.com/ipfs-search/ipfs-metadata-api.git
# 安装
npm install
# 启动
npm start

git clone https://github.com/ipfs-search/ipfs-search-api.git
npm install
npm start
```

### 3）安装ember-cli

```shell
# 使用npm下载最新ember-cli
npm install -g ember-cli

# 建立软连接
ln -s /usr/local/nodejs/bin/ember /usr/bin/ember
sudo ember -v
```

### 4）安装ipfs-search-frontend

```shell
# 下载ipfs-search-frontend
git clone https://github.com/ipfs-search/ipfs-search-frontend

# 安装
npm install
# 启动
ember serve
```


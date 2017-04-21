# install_es5_3
1.
确保jdk为jdk1.8.0_73以上
useradd elastic 

2.
vi /etc/security/limits.conf
* soft nofile 65536
* hard nofile 65536

3.
echo 'vm.max_map_count=262144'>> /etc/sysctl.conf
sysctl -p

4.三台分别配置
vim config/elasticsearch.yml

cluster.name: xxx

node.name: "elasticsearch1"

node.master: true

node.data: true

path.data: /home/elasticsearch-5.3.0/data

path.logs: /home/elasticsearch-5.3.0/logs

#从5开始也不支持这个配置了

#bootstrap.mlockall: true

network.host: 192.168.1.200

http.port: 9200

transport.tcp.port: 9300

#从5开始也不支持这个配置了

#discovery.zen.ping.multicast.enabled: false

#discovery.zen.ping.unicast.hosts: ["10.105.219.150", "10.105.219.151:9300", "10.105.219.207:9300"]

discovery.zen.ping.unicast.hosts: ["192.168.1.200", "192.168.1.201", "192.168.1.202"]

#从5开始索引的配置不能配在yml中了

#index.number_of_shards: 3

#index.number_of_replicas: 2

#如下两个参数不认了

#ES_MIN_MEM: 512m

#ES_MAX_MEM: 512m

#network.bind_host: 182.254.216.197

#network.publish_host: 10.131.152.84

http.cors.enabled: true

http.cors.allow-origin: "*"


5.
su - elastic -c "nohup /home/elasticsearch-5.3.0/bin/elasticsearch > /dev/null 2>&1 &"

6.
检查集群状态
http://192.168.1.200:9200/_cluster/health?pretty=true
http://192.168.1.201:9200/_cluster/health?pretty=true
http://192.168.1.202:9200/_cluster/health?pretty=true

安装head：
从5开始，head不再是一个插件形式安装，head作为独立的一个服务，需要单独安装：

yum install git -y

cd /home/elasticsearch-5.3.0/

git clone git://github.com/mobz/elasticsearch-head.git

cd elasticsearch-head 

npm install -g grunt   #(执行这条之前请看下面注意)


注意：目前centos7还不能用yum安装nodejs和npm，需要手动安装

安装nodejs：

去这里下载编译好的包，https://nodejs.org/download/release/latest-v7.x/

然后解压，把里面的bin添加到path即可

安装npm：

#使用如下命令（https://docs.npmjs.com/README#fancy-install-unix-   这个网址中有说明）

sudo curl -L https://npmjs.org/install.sh | sh


修改elasticsearch-head的连接地址: 目录：elasticsearch-head/_site/app.js

this.base_uri = this.config.base_uri || this.prefs.get("app-base_uri") || "http://localhost:9200";

把localhost修改成你es的服务器地址，如:

this.base_uri = this.config.base_uri || this.prefs.get("app-base_uri") || "http://192.168.1.x:9200";


cd /home/elasticsearch-5.3.0/elasticsearch-head/

执行npm install下载依赖的包：(这句其实不用也行)

npm install

启动：

nohup grunt server &


查看head页面：
http://192.168.1.200:9100
http://192.168.1.201:9100
http://192.168.1.202:9100











